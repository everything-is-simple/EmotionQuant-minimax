# EmotionQuant 跨 Spiral 依赖与数据流图

**版本**: v1.3.0
**最后更新**: 2026-02-12
**伴随文档**: `VORTEX-EVOLUTION-ROADMAP.md`

---

## 1. 全链路数据流

```
┌─────────────────────────────────────────────────────────────────────┐
│                        EmotionQuant 数据流全景                       │
└─────────────────────────────────────────────────────────────────────┘

  S0 Data Layer                S1 MSS              S2 Signal
  ┌──────────┐              ┌──────────┐        ┌──────────────┐
  │ TuShare  │──→ raw_*  ──→│ 六因子   │        │ IRS 六因子   │
  │ Fetcher  │              │ 引擎     │        │ 引擎         │
  └──────────┘              └────┬─────┘        └──────┬───────┘
       │                         │                     │
       ▼                         ▼                     ▼
  ┌──────────┐              ┌──────────┐        ┌──────────────┐
  │ Parquet  │              │ 温度合成 │        │ irs_industry │
  │ + DuckDB │              │ 周期判定 │        │ _daily       │
  └──────────┘              │ 趋势判定 │        └──────┬───────┘
       │                    └────┬─────┘               │
       ▼                         │                     │
  ┌──────────┐                   ▼               ┌──────────────┐
  │ market_  │              ┌──────────┐         │ PAS 三因子   │
  │ snapshot │──────────────│ mss_     │         │ 引擎         │
  └──────────┘              │ panorama │         └──────┬───────┘
       │                    └────┬─────┘               │
       ▼                         │                     ▼
  ┌──────────┐                   │              ┌──────────────┐
  │ industry │                   │              │ stock_pas    │
  │ _snapshot│                   │              │ _daily       │
  └──────────┘                   │              └──────┬───────┘
                                 │                     │
                                 │              ┌──────┴───────┐
                                 │              │ Validation   │
                                 │              │ Gate         │
                                 │              └──────┬───────┘
                                 │                     │
                                 ▼                     ▼
                           ┌─────────────────────────────────┐
              S2/S3  │        Integration Engine        │
                           │  MSS + IRS + PAS → final_score  │
                           └──────────────┬──────────────────┘
                                          │
                           ┌──────────────▼──────────────────┐
                           │     integrated_recommendation    │
                           └──────────────┬──────────────────┘
                                          │
                        ┌─────────────────┼─────────────────┐
                        ▼                 ▼                 ▼
                 ┌────────────┐   ┌────────────┐   ┌────────────┐
          S3     │ Vectorized │   │ performance│   │ A/B/C      │
                 │ Backtest   │   │ _metrics   │   │ 对照报表   │
                 └─────┬──────┘   └─────┬──────┘   └────────────┘
                       │                │
                       ▼                │
                ┌────────────┐          │
         S4     │ Paper      │          │
                │ Trading    │          │
                │ Engine     │          │
                └─────┬──────┘          │
                      │                 │
           ┌──────────┼──────────┐      │
           ▼          ▼          ▼      │
      ┌─────────┐ ┌────────┐ ┌─────────┐  │
      │ trade_  │ │ posit- │ │ risk_   │  │
      │ records │ │ ions   │ │ events  │  │
      └────┬────┘ └───┬────┘ └────┬────┘  │
           │          │         │      │
           └──────────┼─────────┘      │
                      ▼                ▼
               ┌─────────────────────────────┐
        S5     │   Streamlit Dashboard       │
               │   + daily_report            │
               │   + CSV/MD 导出             │
               └─────────────┬───────────────┘
                             │
                             ▼
               ┌─────────────────────────────┐
        S6     │   全链路重跑一致性           │
               │   债务清偿 + 权重对照        │
               │   freeze 检查               │
               └─────────────────────────────┘
```

### 1.1 反馈微循环与新增产物流向（新增）

```
S2: validation_prescription + s2_cross_section_probe
                     │
                     ▼
S3: quality_gate_report + quicklook_equity_curve
        │ PASS                         │ FAIL
        ▼                              ▼
      S4 主线推进                 S2r 定向回修
                                      │
                                      ▼
                          s2r_patch_note + recommendation_delta
                                      │
                                      └────→ 回流 S3 重验
```

| 产物 | 产出位置 | 消费位置 | 用途 |
|---|---|---|---|
| `validation_prescription` | S2 | S2r / S3 | 给出“问题类型→修复建议→目标因子” |
| `s2_cross_section_probe` | S2 | S3 / S2r | 回测前体检，验证 TopN 与 BottomN 区分度 |
| `quality_gate_report` | S3 | S4 / S2r | S4 入口判定（PASS）或触发回修（FAIL） |
| `quicklook_equity_curve` | S3 | S4 / S5 | 交易前可视化评估与展示 |
| `s2r_patch_note` | S2r | S3 | 记录修复项、目标指标与影响范围 |
| `s2r_recommendation_delta` | S2r | S3 | 对比回修前后推荐变化与风险暴露 |

---

## 2. Spiral 间阻断依赖（必须满足才能进入下一圈）

| 目标 Spiral | 前置 Spiral | 阻断条件 |
|---|---|---|
| S1 | S0 | `market_snapshot` 必须存在且字段完整 |
| S2 | S0, S1 | `industry_snapshot` 骨架就绪；`mss_panorama` 落库 |
| S3 | S2 | `integrated_recommendation` + `validation_gate_decision` + `s2_cross_section_probe` 可用 |
| S4 | S2, S3 | `integrated_recommendation` + `backtest_results` 可用且 `quality_gate_report.status = PASS` |
| S5 | S3, S4 | `performance_metrics` + `trade_records` + `positions` 可用 |
| S6 | S0-S5 | 全部 Spiral 已收口 |

### 2.1 反馈微循环（非主线）

| 触发点 | 回修入口 | 回修边界 | 回修出口 |
|---|---|---|---|
| S3 `quality_gate_report.status = FAIL` | `S2r` | 仅允许修改 IRS/PAS/Validation/Integration，不改 S0/S1 契约 | 重新回到 S3 并产出 PASS 的 `quality_gate_report` |
| `validation_gate_decision = FAIL` | `S2r` | 必须附带 `validation_prescription` 的修复映射 | 产出 `s2r_patch_note` 与 `s2r_recommendation_delta` 后重验 |
| `s2_cross_section_probe.top_bottom_spread_5d <= 0` | `S2r` | 优先修复权重与评分口径，不扩张新功能 | 重验通过后方可恢复 S4 主线 |

微循环执行约束：
1. 单一问题最多允许 2 次 S2r 回修。
2. 连续 2 次回修仍 FAIL，阻断主线并升级到路线图评审。
3. S4 及后续 Spiral 不得绕过质量门进入执行。

---

## 3. 共享基础设施依赖

以下基础设施在 S0 建立，所有后续 Spiral 共享：

| 基础设施 | 建立圈 | 消费圈 | 说明 |
|---|---|---|---|
| CLI 入口 (`eq`) | S0 | S0-S6 | 统一命令行，子命令按 Spiral 扩展 |
| Config 注入 | S0 | S0-S6 | 环境变量 + `.env` + Config 对象 |
| Parquet + DuckDB | S0 | S0-S6 | Parquet 落盘 + DuckDB 单库查询（对齐 `docs/system-overview.md` 存储策略） |
| error_manifest | S0 | S0-S6 | 标准化错误输出格式 |
| canary 数据包 | S0 | S0-S6 | 离线回归的确定性输入 |
| `normalize_zscore` | S1 | S2 | Z-Score 标准化通用函数 |

---

## 4. CP 能力包生命周期

```
CP-01 ████████░░░░░░░░░░░░░░░░░░  S0 主建, S1 复用
CP-02 ░░░░░░░░████████░░░░░░░░░░  S1 主建
CP-03 ░░░░░░░░░░░░░░░░████░░░░░░  S2 主建
CP-04 ░░░░░░░░░░░░░░░░████░░░░░░  S2 主建
CP-05 ░░░░░░░░░░░░░░░░████░░░░░░  S2 主建
CP-06 ░░░░░░░░░░░░░░░░░░░░████░░  S3 主建
CP-07 ░░░░░░░░░░░░░░░░░░░░░░████  S4 主建, S6 扩展
CP-08 ░░░░░░░░░░░░░░░░░░░░░░░░██  S5 主建
CP-09 ░░░░░░░░░░░░░░░░░░░░██████  S3-S6 渐进
CP-10 ░░░░░░░░░░░░░░░░████░░░░██  S2 主建, S6 扩展
```

---

## 5. ENH 外挂增强排布总览

> 对齐 `docs/design/enhancements/eq-improvement-plan-core-frozen.md` §4 外挂增强包。

| ENH | 名称 | 主建圈 | 复用/扩展圈 | 核心链路依赖度 |
|---|---|---|---|---|
| ENH-01 | 统一运行入口 CLI | S0 | S1-S6 CLI 扩展 | 高：五件套 run 证据起点 |
| ENH-02 | 数据预检与限流守卫 | S0 | — | 高：TuShare 采集稳定性 |
| ENH-03 | 失败产物协议 | S0 | S4 复用 | 中：问题定位效率 |
| ENH-04 | 适配层契约测试 | S0 | S1-S4 分批 | 高：模块间接口守卫 |
| ENH-05 | 金丝雀数据包 | S0 | S1-S6 全圈复用 | 高：离线测试前置 |
| ENH-06 | A/B/C 对照看板 | S3 | — | 低：证据工具 |
| ENH-07 | L4 产物标准化 | S5 | — | 低：展示层增强 |
| ENH-08 | 设计冻结检查 | S0（骨架） | S6（全量） | 低：长期守卫 |
| ENH-09 | Qlib 适配层 | S3 | — | 最高：核心诉求 |

### 5.1 ENH 生命周期

```
         S0    S1    S2    S3    S4    S5    S6
ENH-01 ████████████████████████████  S0 主建, S1-S6 CLI 扩展
ENH-02 ████░░░░░░░░░░░░░░░░░░░░░░░░  S0 嵌入
ENH-03 ████░░░░░░░░░░░░████░░░░░░░░  S0 主建, S4 复用
ENH-04 ████████████████████░░░░░░░░  S0-S4 分批
ENH-05 ████████████████████████████  S0 主建, 全圈复用
ENH-06 ░░░░░░░░░░░░████░░░░░░░░░░░░  S3 主建
ENH-07 ░░░░░░░░░░░░░░░░░░░░████░░░░  S5 主建
ENH-08 ████░░░░░░░░░░░░░░░░░░░░████  S0 骨架, S6 全量
ENH-09 ░░░░░░░░░░░░████░░░░░░░░░░░░  S3 主建
```

### 5.2 外挂总工时

外挂总计 ~8.2d / 30.1d ≈ **27%**，符合 `enhancement-selection-analysis` 预估的 20-25% + ENH-09 工时。

---

## 变更记录

| 版本 | 日期 | 变更 |
|---|---|---|
| v1.3.0 | 2026-02-12 | 新增反馈微循环执行定义：补 §1.1 产物流向、§2.1 非主线回修规则；S4 阻断条件升级为 `quality_gate_report.status = PASS` |
| v1.2.1 | 2026-02-12 | 链接口径整理：`system-overview` 与改进主计划引用改为完整路径 |
| v1.2.0 | 2026-02-12 | 新增 §5 ENH 外挂增强排布总览（分布表 + 生命周期图 + 工时占比） |
| v1.1.0 | 2026-02-12 | 对齐 SoT：S2 含 Integration，S3 转为回测闭环；修正存储口径为 Parquet+DuckDB；修正 risk_evt 为 risk_events |
| v1.0.0 | 2026-02-12 | 初版：全链路数据流、阻断依赖、共享基础设施、CP 生命周期 |
