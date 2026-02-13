# EmotionQuant 涡旋演进路线图 (Vortex Evolution Roadmap)

**版本**: v1.7.0
**创建日期**: 2026-02-12
**最后更新**: 2026-02-13
**状态**: Effective（A 层生效；B 层重排已触发执行）
**适用范围**: S0-S6 全周期螺旋实施
**适用对象**: 单开发者、个人使用

---

## 0. 本文档是什么

本文档是 EmotionQuant 系统的**螺旋型实施总路线图**，融合业界优秀 Roadmap 模板要素与本项目治理体系，提供从 S0 到 S6 的完整演进视图。

### 0.1 优秀路线图的关键要素（本文档遵循）

1. **愿景锚定** — 每个 Spiral 必须回答"为什么做"，对齐系统定位。
2. **里程碑与时间线** — 可视化进度，以 Spiral 为时间单位（默认 7 天/圈）。
3. **依赖链与遗产链** — 显式标注上下游依赖与产出资产流转。
4. **进度与状态追踪** — 每个 Spiral 有状态标记，支持实时看板。
5. **风险矩阵与降级策略** — 提前识别阻塞并给出退路。
6. **验收门禁** — 每个 Spiral 有明确的 Go/No-Go 判定标准。
7. **活文档** — 随执行演进持续更新，不做一次性冻结。

### 0.2 上位约束

- 系统铁律：`Governance/steering/系统铁律.md`
- 核心原则：`Governance/steering/CORE-PRINCIPLES.md`
- 6A 工作流：`Governance/steering/6A-WORKFLOW.md`
- 执行主控：本文件 + `Governance/SpiralRoadmap/DEPENDENCY-MAP.md`
- 能力包（`Governance/Capability/CP-*.md`）仅作历史回顾，不作为当前执行入口
- 改进主计划：`docs/design/enhancements/eq-improvement-plan-core-frozen.md`

### 0.3 与已有文档的关系

- 历史草稿已完成收敛，本目录以本文件与 `Governance/SpiralRoadmap/DEPENDENCY-MAP.md` 作为正式桥梁入口。
- 本文档作为 `Governance/SpiralRoadmap/` 目录主入口，用于螺旋执行可视化与里程碑管理。
- 执行权威仍以 `docs/design/enhancements/eq-improvement-plan-core-frozen.md` 为最终口径，本文档为其可视化执行伴侣。

### 0.4 状态口径（2026-02-13 统一）

- `Draft`: 草拟中，不能作为执行入口。
- `Candidate`: 评审通过前的候选版本，不可视为生效基线。
- `Approved`: 审批通过但 SoT 尚未同步完成。
- `Effective`: SoT 已同步，可作为当前执行基线。

当前口径：

1. 本文档当前为 `Effective`（A 层整改已生效，B 层重排已触发）。
2. S1-S3 主线执行链切换为：`S1a → S1b → S2a → S2b → S3a → S3b`。
3. S4 入口条件升级为：`S3b` 收口完成且 `quality_gate_report.status = PASS`。

### 0.5 B 层触发记录（2026-02-13）

触发判定依据（满足任一即可）：

1. S2 父圈预计超出 5 个工作日且无法自然收口。
2. 连续 2 次出现 `S3 -> S2r` 回修。
3. S2/S3 质量门连续 FAIL 且定位为“结构性打包过重”。

本次执行决议：B 层已触发，按 `S1a/S1b/S2a/S2b/S3a/S3b` 顺序进入主路线图执行。

---

## 1. 系统愿景与路线目标

### 1.1 系统定位

EmotionQuant 是面向中国 A 股的**情绪驱动量化系统**。情绪因子为主信号来源，技术指标仅可用于辅助特征与对照实验，不得独立触发交易。

### 1.2 路线目标

通过 7 个螺旋周期（S0-S6），从"零可运行"推进到"可持续运营"：

```
S0 数据闭环 → S1 市场信号 → S2 信号生成+集成 → S3 回测闭环 → S4 纸上交易 → S5 可视化 → S6 稳定化
```

执行展开（B 层触发后）：

```
S0 → S1a → S1b → S2a → S2b → S3a → S3b → S4 → S5 → S6
```

每个 Spiral 交付一个**可运行闭环**，禁止"看起来完成"。

---

## 2. 全局执行契约

> 以下规则在所有 Spiral 中生效，不再在每个 Spiral 章节重复。

### 2.1 冻结区（只允许实现，不允许改语义）

1. 情绪优先与"单指标不得独立决策"。
2. MSS / IRS / PAS / Validation / Integration 核心公式与命名。
3. L1-L4 数据契约与关键业务表字段。
4. A 股规则：T+1、涨跌停（主板10%/创业板科创板20%/ST 5%）、交易时段、整手、费用模型、申万行业。

### 2.2 切片预算

- 一圈仅 1 个主目标。
- 一圈仅 1-3 个 CP Slice。
- 单 Task 超过 1 天必须拆分。
- 超出预算则拆为子圈，各留五件套证据。

### 2.3 五件套退出条件（每圈必须）

| 证据 | 要求 |
|---|---|
| run | 至少 1 条可复制命令 |
| test | 至少 1 组自动化测试 |
| artifact | 至少 1 个结构化产物 |
| review | `Governance/specs/spiral-s{N}/review.md` |
| sync | 最小同步 5 文件 |

### 2.4 错误分级

| 级别 | 定义 | 处理 |
|---|---|---|
| P0 | 核心输入缺失、契约破坏、合规违规 | 阻断 |
| P1 | 局部数据缺失、局部计算失败 | 降级 + 标记 |
| P2 | 非关键异常 | 重试 + 记录 |

### 2.5 Strict 6A 升级触发

- S4 默认 Strict 6A。
- S3 / S6 在以下条件触发：数据契约破坏性变化、Validation Gate 规则变更、新关键外部依赖进入主路径。

### 2.6 分类定义（用于工时分配）

- **核心算法**：MSS/IRS/PAS/Validation/Integration 的算法实现与验证。
- **核心基础设施**：Data/Backtest/Trading/GUI/Analysis 的编排、配置、适配、可运行能力。
- **系统外挂**：ENH-01~09，对核心语义不做改写的守卫、对照、可视化、回归资产。

### 2.7 能力消费门禁（新增）

- 每个 Spiral 除 `run/test/artifact/review/sync` 外，必须补 1 个“能力消费场景”证据（谁消费、怎么消费、消费结论）。
- 新能力在 1 圈内必须出现首次消费；若无法满足，需在当圈 `review.md` 明确延期原因与下一圈消费计划。
- S3 起启用“微循环”触发：若回测质量不达门槛，进入 S2r 子圈定向修正后重验，不允许直接跳过问题推进 S4。

### 2.8 执行参数默认值（可复制）

| 参数 | 含义 | 默认值（建议） | 适用范围 |
|---|---|---|---|
| `trade_date` | 交易日（`YYYYMMDD`） | 当周最近交易日（示例：`20260207`） | S0-S6 |
| `start` / `end` | 回测窗口 | 最近 120+ 交易日（示例：`20250801`~`20260207`） | S3/S6 |
| `source` | 数据来源模式 | `mock`（开发）/ `tushare`（准生产） | S0 |
| `engine` | 回测引擎口径 | `vectorized`（收口）/ `qlib`（研究旁路） | S3/S6 |
| `week_id` | 周频运营编号 | `YYYYWww`（示例：`2026W07`） | S6 后 |

执行要求：

1. 所有 `{placeholder}` 在当圈 `review.md` 必须替换为实际值。
2. 未声明参数默认值的命令不作为当圈收口证据。
3. 质量门涉及窗口长度时，以 `backtest_trade_days >= 120` 为最低样本基线。

### 2.9 证据落位约定（执行版）

每圈证据默认放在：

`artifacts/spiral-s{N}/{trade_date}/`

最小内容：

1. `run.log`
2. `test.log`
3. `gates.md`（门禁判定摘要）
4. `consumption.md`（消费结论）

若暂不使用统一目录，必须在 `review.md` 明确真实路径与文件名。

### 2.10 状态推进条件（Draft -> Effective）

| 状态迁移 | 必须满足 | 输出 |
|---|---|---|
| Draft -> Candidate | 文档结构完整、门禁可读、依赖链闭合 | 候选版号 |
| Candidate -> Approved | 评审通过且无冻结区冲突 | 审批结论 |
| Approved -> Effective | SoT 同步完成（VORTEX + DEPENDENCY + 主执行基线） | 生效版号 |

---

## 3. 路线总览看板

### 3.1 Spiral 进度看板

| Spiral | 代号 | 主目标 | 推荐 CP 组合 | 预估工时 | 状态 |
|---|---|---|---|---|---|
| S0 | 🌱 数据根基 | 数据最小闭环 | CP-01 | 4.5d | 🚧 待启动 |
| S1a | 🌡️-a 市场脉搏 | MSS 最小可用 + 微型回溯 | CP-01, CP-02 | 2.6d | 📋 未开始 |
| S1b | 🌡️-b MSS-only 集成 | MSS-only 小回测与消费验证 | CP-02, CP-05 | 1.2d | 📋 未开始 |
| S2a | 📡-a IRS 叠加 | IRS 叠加 + 处方门禁 | CP-03, CP-10 | 3.8d | 📋 未开始 |
| S2b | 📡-b 双算法稳态 | MSS+IRS 稳态集成输出 | CP-03, CP-05 | 2.3d | 📋 未开始 |
| S3a | 🔬-a PAS 增量 | PAS 叠加 + 增量价值验证 | CP-04, CP-10, CP-09 | 2.6d | 📋 未开始 |
| S3b | 🔬-b 三算法稳态 | 三算法全量集成 + 质量门 | CP-04, CP-05, CP-06, CP-09 | 1.6d | 📋 未开始 |
| S4 | 📜 纸上实战 | 纸上交易闭环（Strict 6A） | CP-07, CP-09 | 4.5d | 📋 未开始 |
| S5 | 🖥️ 可见之眼 | GUI + 日报闭环 | CP-08, CP-09 | 3.0d | 📋 未开始 |
| S6 | 🔒 稳态收敛 | 稳定化 + 债务清偿 | CP-10, CP-07, CP-09 | 4.0d | 📋 未开始 |
| **合计** | | | | **30.1d** | |

> 说明：B 层已触发，S1-S3 统一按 a/b 子圈顺序执行；仅当 `S3b` 收口且 `quality_gate_report.status = PASS` 时才可进入 S4。

### 3.2 累计能力演进图

```
S0  ██░░░░░░░░░░░░  数据可读
S1  █████░░░░░░░░░  市场温度可算
S2  █████████░░░░░  信号可出 + 推荐可生成
S3  ██████████░░░░  回测可验 + 绩效可比
S4  ████████████░░  交易可模拟 + 风控可执行
S5  █████████████░  结果可视化 + 日报可导
S6  ██████████████  全链路可复现 + 可持续运行
```

### 3.3 三分类工时分布

> 分类口径对齐 `docs/design/README.md` 三层结构。核心算法 = MSS/IRS/PAS/Validation/Integration；核心基础设施 = Data/Backtest/Trading/GUI/Analysis；系统外挂 = ENH-01~09。

| Spiral | 核心算法 | 核心基础设施 | 系统外挂(ENH) | 总计 |
|---|---:|---:|---:|---:|
| S0 | 0d (0%) | 3.0d (67%) | 1.5d (33%) | 4.5d |
| S1 | 2.3d (61%) | 0.8d (21%) | 0.7d (18%) | 3.8d |
| S2 | 5.2d (85%) | 0d (0%) | 0.9d (15%) | 6.1d |
| S3 | 0d (0%) | 2.1d (50%) | 2.1d (50%) | 4.2d |
| S4 | 0d (0%) | 3.3d (73%) | 1.2d (27%) | 4.5d |
| S5 | 0d (0%) | 2.2d (73%) | 0.8d (27%) | 3.0d |
| S6 | 1.0d (25%) | 2.0d (50%) | 1.0d (25%) | 4.0d |
| **合计** | **8.5d (28%)** | **13.4d (45%)** | **8.2d (27%)** | **30.1d** |

> 父圈预算口径下，核心算法主要集中在 S1-S2；基础设施主要分布在 S0/S3-S5；外挂承担早期可视与对照职责，全局占比约 27%。
> B 层触发后，S1-S3 采用子圈重排执行；本表暂保留父圈预算口径，待 `S1a-S3b` 首轮收口后回填实绩占比。

---

## 4. 依赖链与遗产链

### 4.1 Spiral 间依赖关系

```
S0 ──→ S1a ──→ S1b ──→ S2a ──→ S2b ──→ S3a ──→ S3b ──→ S4 ──→ S5 ──→ S6
│         │       │       │       │       │       │       │       │       │
│         │       │       │       │       │       │       │       │       ├── 全链路重跑一致性
│         │       │       │       │       │       │       │       ├── Dashboard + 日报
│         │       │       │       │       │       │       ├── 订单/持仓/风控
│         │       │       │       │       │       ├── 三算法稳态 + 质量门
│         │       │       │       │       ├── PAS 增量价值
│         │       │       │       ├── MSS+IRS 稳态集成
│         │       │       ├── IRS + 处方门禁
│         │       ├── MSS-only 消费验证
│         ├── MSS 温度/周期/趋势
├── L1/L2 数据集
                            ▲                 │
                            └──── S2r ◄───────┘  (S3b 质量门 FAIL 时触发微循环)
```

> `S2r` = S2 repair 子圈，仅允许对 IRS/PAS/Validation/Integration 做定向微修，可落在 S2a/S2b/S3a 任务池，不改 S0/S1 契约。

### 4.2 遗产流转链（Heritage Chain）

每个 Spiral 产出的可复用资产，及其下游消费者：

| 产出 Spiral | 遗产资产 | 消费 Spiral |
|---|---|---|
| S0 | `TuShareFetcher`（统一采集入口） | S1, S2 |
| S0 | `eq run` CLI 入口 | S1-S6 全部 |
| S0 | canary 数据包（离线回归） | S1-S6 全部 |
| S0 | `market_snapshot` / `industry_snapshot` L2 表 | S1, S2 |
| S1 | `normalize_zscore` 标准化实现 | S2 |
| S1 | `mss_panorama` 输出表 | S2 (Integration) |
| S1 | 周期/趋势判定模板 | S2 (IRS/PAS 参考) |
| S1 | MSS 微型回溯报告（`mss_micro_probe_report`） | S2 (先验校准) |
| S2 | `irs_industry_daily` | S2b, S3a |
| S2 | `validation_gate_decision` | S2 (Integration 前置门禁), S3 |
| S2 | `validation_prescription`（处方建议） | S2r, S3 |
| S2 | `integrated_recommendation` | S3 (回测), S4 (Trading) |
| S2 | `validation_weight_plan` 桥接表 | S3, S6 |
| S2 | 截面对照报告（`s2_cross_section_probe`） | S3 (回测前体检), S2r |
| S3 | `stock_pas_daily` | S3b, S2r |
| S3 | 三算法增量报告（`triple_signal_increment_report`） | S3b, S2r |
| S3 | 向量化回测器 | S4 (口径对齐), S6 (重跑) |
| S3 | `performance_metrics` | S5 (GUI 展示) |
| S3 | A/B/C 对照基线 | S6 (权重对照) |
| S3 | 轻量可视化产物（`quicklook_equity_curve`） | S4 (交易前评估), S5 |
| S4 | `trade_records` / `positions` / `risk_events` | S5 (GUI 展示) |
| S4 | 回测-交易口径一致性测试 | S6 (重跑验证) |
| S5 | GUI 基线模板 | S6 (稳态展示) |
| S5 | 日报模板与归档命名 | S6 (运营基线) |
| S6 | 全链路重跑基线 | 后续迭代 |
| S6 | 权重更新证据链 | 后续迭代 |

### 4.3 CP 跨 Spiral 参与矩阵

| CP | S0 | S1 | S2 | S3 | S4 | S5 | S6 |
|---|---|---|---|---|---|---|---|
| CP-01 Data | ★ | ○ | | | | | |
| CP-02 MSS | | ★ | | | | | |
| CP-03 IRS | | | ★ | | | | |
| CP-04 PAS | | | | ★ | | | |
| CP-05 Integration | | | ★ | | | | |
| CP-06 Backtest | | | | ★ | | | |
| CP-07 Trading | | | | | ★ | | ○ |
| CP-08 GUI | | | | | | ★ | |
| CP-09 Analysis | | | | ★ | ○ | ★ | ○ |
| CP-10 Validation | | | ★ | ○ | | | ○ |

> ★ = 主要实现圈, ○ = 复用/扩展圈

---

## 5. S0 🌱 数据根基

### 5.1 使命

无稳定 L1/L2 输入，后续所有算法无法形成可验证结果。S0 的使命是建立**可复现的最小数据链路**。

### 5.2 范围

**In Scope**:
- `raw_daily / raw_daily_basic / raw_limit_list / raw_index_daily / raw_trade_cal` 最小可用
- `market_snapshot` 完整字段（含 `data_quality / stale_days / source_trade_date`）
- `industry_snapshot` 骨架字段
- `eq run` 统一入口与环境配置
- 数据根目录通过 `DATA_PATH` 环境变量注入（实际部署：`G:\EmotionQuant_data`，仓库外独立目录）

**Out Scope**:
- MSS / IRS / PAS 评分
- Validation Gate
- Backtest / Trading / GUI

### 5.3 任务分解

| 任务 | 分类 | 主要产出 | 存在理由 | 工时 |
|---|---|---|---|---:|
| S0-T1 CLI 与配置落地 | 外挂 ENH-01 | `eq run`、`.env.example`、Config 接线 | 没有统一入口无法闭环复现 | 0.8d |
| S0-T2 TuShare 采集实现 | 核心基础设施（含 ENH-02） | `TuShareFetcher` 可用拉取 | 数据源入口必须先通 | 1.0d |
| S0-T3 Parquet->DuckDB 入库 | 核心基础设施 | Repository 入库链路 | 后续算法统一读本地 | 1.0d |
| S0-T4 L2 快照计算 | 核心基础设施 | `market_snapshot / industry_snapshot` | MSS/IRS 输入前置依赖 | 1.0d |
| S0-T5 守卫与回归资产 | 外挂 ENH-03/04/05/08 | `error_manifest`、canary、freeze_check | 保证失败可追踪、离线可回归 | 0.7d |

### 5.4 错误处理

| 场景 | 级别 | 处理 |
|---|---|---|
| `TUSHARE_TOKEN` 缺失/无效 | P0 | 立即阻断 |
| API 限流或网络抖动 | P2 | 重试3次 + 指数退避 |
| 非交易日无数据 | P1 | 回退最近交易日并标注 stale |
| Parquet/DuckDB 写入失败 | P0 | 阻断并写 error_manifest |
| 局部标的缺失 | P1 | 跳过并标注质量 |

### 5.5 验收门禁

1. `eq run --date 20260207 --source mock` 返回 0
2. `pytest tests/canary tests/contracts/test_data_* -q` 全通过
3. DuckDB 存在 `market_snapshot` 且字段契约完整
4. 失败流程能输出 `error_manifest.json`（含 `error_level`）
5. `freeze_check` 可执行并给出关键文档锚点结果

### 5.6 退出五件套

- **run**: `eq run --date {trade_date} --source mock`
- **test**: `pytest tests/canary tests/contracts/test_data_* -q`
- **artifact**: parquet + duckdb 表 + error_manifest
- **review**: `Governance/specs/spiral-s0/review.md`
- **sync**: 最小同步 5 文件

### 5.7 遗产产出

- 统一数据采集入口（TuShareFetcher）
- 统一运行入口（CLI）
- canary 数据夹（离线回归）
- 设计冻结锚点（防漂移）
- L2 快照表（S1/S2 直接消费）

### 5.8 降级策略

若 TuShare 不可用：以 mock 数据完成闭环，TuShare 接入延后到 S0 子圈。

---

## 6. S1 🌡️ 市场脉搏

### 6.0 B 层子圈执行（已触发）

| 子圈 | 目标 | 入口条件 | 退出门禁 | 最小消费证据 |
|---|---|---|---|---|
| S1a | MSS 最小可用 + 微型回溯 | S0 五件套收口完成 | `mss_panorama` 可用，`mss_micro_probe_report` 形成结论 | 温度分层与后续收益的消费结论 |
| S1b | MSS-only 集成小闭环 | S1a 收口完成 | `mss_only_probe_report` 已产出，且结论明确“是否进入 S2a” | MSS-only 推荐/小回测消费结论 |

### 6.1 使命

MSS 给出市场层主信号（温度/周期/趋势），是后续 Integration 的必需输入。S1 完成后，系统具备"感知市场情绪"的基础能力。

### 6.2 范围

**In Scope**:
- 六因子实现与温度合成（`0.17 / 0.34 / 0.34 / 0.05 / 0.05 / 0.05`）
- 周期判定（含 `unknown` 兜底）与趋势判定
- `mss_panorama` 落库与契约测试

**Out Scope**:
- IRS / PAS / Validation
- 交易触发逻辑

### 6.3 任务分解

| 任务 | 分类 | 主要产出 | 存在理由 | 工时 |
|---|---|---|---|---:|
| S1-T1 六因子引擎 | 核心算法 | `src/algorithms/mss/factors.py` | 没有因子就没有温度来源 | 1.0d |
| S1-T2 温度/周期/趋势 | 核心算法 | `src/algorithms/mss/engine.py` | 周期直接影响推荐等级规则 | 1.0d |
| S1-T3 Writer + CLI | 核心基础设施 | `mss_panorama` 与 `eq mss` | 没有落库无法被下游消费 | 0.8d |
| S1-T4 契约测试与可视化基础 | 外挂 ENH-04 | mss contract tests + L4 温度序列 | 防回归并为 S5 提前准备 | 0.7d |
| S1-T5 微型回溯验证 | 核心算法 | `mss_micro_probe_report` | 前移信号有效性验证，降低 S3 才暴露问题的返工风险 | 0.3d |

### 6.4 错误处理

| 场景 | 级别 | 处理 |
|---|---|---|
| `market_snapshot` 关键字段缺失 | P0 | 阻断并提示回到 S0 |
| 历史样本不足 | P1 | 分数置 50 并标记 cold_start |
| 温度越界 | P1 | 裁剪到 [0,100] 并告警 |
| 非交易日输入 | P0 | 阻断 |

### 6.5 验收门禁

1. `eq mss --date {trade_date}` 成功产出 `mss_panorama`
2. `pytest tests/contracts/test_mss_* -q` 通过
3. `temperature in [0,100]`、`neutrality in [0,1]`
4. 相同输入重复运行结果一致
5. 无交易触发字段（保持信号层边界）
6. `mss_micro_probe_report` 已产出，包含“温度分层 vs 后续收益”的消费结论
7. B 层触发下，S1 必须分 `S1a/S1b` 两次收口，`S1b` 必须产出 `mss_only_probe_report`

### 6.6 退出五件套

- **run**: `eq mss --date {trade_date}` + `eq mss-probe --end {trade_date} --window 20`
- **test**: `pytest tests/contracts/test_mss_* tests/contracts/test_mss_probe_* -q`
- **artifact**: `mss_panorama` + `mss_micro_probe_report`
- **review**: `Governance/specs/spiral-s1/review.md`
- **sync**: 最小同步 5 文件

### 6.7 遗产产出

- `normalize_zscore` 统一实现
- MSS 输出标准表
- 周期与趋势判定实现模板（IRS/PAS 可参考）
- MSS 微型回溯报告（`mss_micro_probe_report`）

### 6.8 降级策略

若六因子中部分数据源不可用：先实现可用因子子集（最少 3 因子），缺失因子给等权回退值，标记 `degraded`。

---

## 7. S2 📡 信号生成

### 7.0 B 层子圈执行（已触发）

| 子圈 | 目标 | 入口条件 | 退出门禁 | 最小消费证据 |
|---|---|---|---|---|
| S2a | IRS 叠加 + 处方化门禁 | S1b 收口完成 | `irs_industry_daily` + `validation_gate_decision` + `validation_prescription` 可用 | IRS 叠加后的消费结论（含问题定位） |
| S2b | MSS+IRS 稳态集成 | S2a 收口完成 | `integrated_recommendation` 可追溯，`s2_cross_section_probe.top_bottom_spread_5d > 0` | 双算法稳态推荐消费结论 |

### 7.1 使命

完成行业层信号与双算法集成，建立因子验证门禁，并产出 TopN 推荐与集成输出。S2 结束后形成 MSS+IRS 稳态信号并为 S3a 的 PAS 叠加提供基线。

> B 层触发执行口径：S2 以 `CP-03 + CP-10 + CP-05` 为主，`CP-04(PAS)` 顺延到 S3a。

### 7.2 范围

**In Scope**:
- IRS 六因子最小版（31 行业覆盖）
- Validation 因子门禁（IC / RankIC / ICIR / positive_ratio → PASS / WARN / FAIL）
- MSS+IRS baseline 集成输出（TopN 推荐 + `integrated_recommendation`）

**Out Scope**:
- PAS 三因子（放 S3a）
- 回测与绩效分析（放 S3）
- 权重 WFA（放 S6 CP10-S2）

### 7.3 任务分解

> 本段为 S2 父圈任务池。B 层触发后，S2 只执行 `S2a(IRS+Validation)` 与 `S2b(MSS+IRS Integration)` 两子圈；PAS 叠加顺延到 S3a。

| 任务 | 分类 | 主要产出 | 存在理由 | 工时 |
|---|---|---|---|---:|
| S2-T1 `industry_snapshot` 补齐 + IRS 引擎 | 核心算法 | `irs_industry_daily` | 三三制行业层输入 | 1.3d |
| S2-T2 双算法集成口径编排 | 核心算法 | `mss_irs_recommendation_probe` | 形成 MSS+IRS 可消费中间结论 | 1.3d |
| S2-T3 Validation 因子门禁 | 核心算法 | `validation_gate_decision` | Integration 前置门禁 | 1.2d |
| S2-T4 Integration 引擎 + `validation_weight_plan` | 核心算法 | `integrated_recommendation` + baseline plan | 下游唯一信号入口 | 1.1d |
| S2-T5 契约测试与编排骨架 | 外挂 ENH-04 | `eq recommend` 命令 | 完整信号链路可复现 | 0.9d |
| S2-T6 截面对照验证 + 处方产物 | 核心算法 | `s2_cross_section_probe` + `validation_prescription` | 让 Validation 从“门禁”升级为“可执行修正建议” | 0.3d |

### 7.4 错误处理

| 场景 | 级别 | 处理 |
|---|---|---|
| 行业覆盖不足 31 | P1 | 标记 stale，允许继续 |
| 估值样本不足 8 只 | P1 | 沿用前值，标记 cold_start |
| 个股停牌或缺行情 | P1 | 跳过该股 |
| Validation 输入缺失 | P0 | Gate=FAIL，阻断后续集成 |
| baseline plan 缺失 | P0 | 阻断（契约不完整） |
| 截面对照样本不足（<30 只） | P1 | 继续产出处方建议并标记低置信 |

### 7.5 验收门禁

1. `irs_industry_daily` 覆盖 31 行业
2. `mss_irs_recommendation_probe` 已产出并给出双算法增量结论
3. `validation_gate_decision` 具备 PASS / WARN / FAIL + reason
4. `integrated_recommendation.final_score` 可追溯到 MSS/IRS + 权重
5. TopN 推荐可生成且集成输出可追溯
6. `validation_prescription` 已产出且含 `problem_type / suggestion / target_factor`
7. `s2_cross_section_probe` 已产出并给出 TopN vs BottomN 的消费结论
8. `S2b` 必须满足 `s2_cross_section_probe.top_bottom_spread_5d > 0` 方可进入 S3a

### 7.6 退出五件套

- **run**: `eq recommend --date {trade_date}` + `eq recommend-probe --date {trade_date} --horizon 5`
- **test**: `pytest tests/contracts/test_irs_* tests/contracts/test_validation_* tests/contracts/test_integration_* tests/contracts/test_signal_probe_* -q`
- **artifact**: `irs_industry_daily` + `mss_irs_recommendation_probe` + `validation_gate_decision` + `validation_prescription` + `integrated_recommendation` + `s2_cross_section_probe`
- **review**: `Governance/specs/spiral-s2/review.md`
- **sync**: 最小同步 5 文件

### 7.7 遗产产出

- 行业信号标准表
- Validation Gate 决策表与桥接表
- Validation 处方建议表（`validation_prescription`）
- `integrated_recommendation` 双算法稳态输出表
- S2 截面对照报告（`s2_cross_section_probe`）
- S3a 可直接消费的双算法集成输出

### 7.8 降级策略

S2 复杂度最高（含 4 Slice），超时风险最大。降级优先级：
1. 拆为 S2a（IRS+Validation+处方）和 S2b（MSS+IRS Integration）两个子圈
2. S2b 可延后为独立子圈，不破 S2a 收口
3. IRS 先保最小 3 因子版

---

## 8. S3 🔬 回测闭环

### 8.0 B 层子圈执行（已触发）

| 子圈 | 目标 | 入口条件 | 退出门禁 | 最小消费证据 |
|---|---|---|---|---|
| S3a | PAS 叠加 + 增量价值验证 | S2b 收口完成 | `stock_pas_daily` + `triple_signal_increment_report` 可用 | 三算法增量价值结论（相对双算法） |
| S3b | 三算法稳态 + 质量门收口 | S3a 收口完成 | `quality_gate_report.status = PASS` | 明确“可进入 S4/不可进入 S4”消费结论 |

### 8.1 使命

用回测验证 S2 产出的集成推荐是否有效，并产出最小绩效摘要。S3 形成"信号到证据"的第一个完整闭环。

> B 层触发执行口径：S3a 引入 `CP-04(PAS)`，S3b 复用 `CP-05(Integration)`，并与 `CP-10/CP-06/CP-09` 一起收口。

### 8.2 范围

**In Scope**:
- CP-04 PAS 三因子叠加（S3a）
- 三算法稳态集成与质量门收口（S3b）
- CP-10 Validation 纳入回测前置校验
- CP-06 本地向量化回测器
- CP-09 最小绩效指标摘要
- Qlib 适配层可选接入（非阻断）

**Out Scope**:
- 权重 WFA（S6 CP10-S2）
- 交易执行（S4）
- 新增上游数据契约变更

### 8.3 任务分解

| 任务 | 分类 | 主要产出 | 存在理由 | 工时 |
|---|---|---|---|---:|
| S3-T0 PAS 三因子引擎 | 核心算法 | `stock_pas_daily` | 三算法补全的必经输入 | 0.8d |
| S3-T1 向量化回测器 | 核心基础设施 | `backtest_results / trade_records` | 验证信号有效性 | 1.0d |
| S3-T2 分析指标实现 | 核心基础设施 | `performance_metrics` | 形成可对比证据 | 0.8d |
| S3-T3 Qlib 适配器 | 外挂 ENH-09 | `qlib_adapter` | 保持研究主选兼容 | 0.7d |
| S3-T4 A/B/C 对照 + 增量报告 | 外挂 ENH-06 | 对照报表 + `triple_signal_increment_report` | 证明 PAS 叠加的增量价值 | 0.7d |
| S3-T5 轻量可视化 + 质量门评估 | 外挂 ENH-06 | `quicklook_equity_curve` + `quality_gate_report` | 在进入 S4 前形成 Go/No-Go 判定 | 0.2d |

### 8.4 错误处理

| 场景 | 级别 | 处理 |
|---|---|---|
| `integrated_recommendation` 缺失 | P0 | 阻断（回到 S2） |
| `stock_pas_daily` 缺失 | P0 | 阻断（回到 S3a） |
| Validation Gate=FAIL | P0 | 阻断回测 |
| 信号与行情日期错位 | P0 | 阻断 |
| 个别标的无行情 | P1 | 跳过并记录 |
| Qlib 不可用 | P2 | 仅执行向量化基线 |
| 质量门不达标 | P0 | 触发 S2r 子圈并阻断进入 S4 |

### 8.5 验收门禁

1. `eq pas --date {trade_date}` 成功产出 `stock_pas_daily`
2. `eq backtest --engine vectorized` 可复现
3. T+1 与涨跌停规则测试通过
4. 产出 `performance_metrics`（基线回测报告 + 指标摘要）
5. A/B/C 对照表可生成且归档
6. 产出 `triple_signal_increment_report` 与 `quicklook_equity_curve`
7. 产出 `quality_gate_report`，且 `S3b` 结果明确 PASS/FAIL
8. 若 `S3b` 质量门为 FAIL，必须创建 S2r 回修任务并形成消费结论后方可继续

### 8.5.1 质量门阈值与 S2r 入口/出口（B 层触发执行口径）

质量门采用组合阈值，避免单指标误判：

- 样本规模：`backtest_trade_days >= 120`
- 收益质量：`annualized_sharpe >= 0.60`
- 风险约束：`max_drawdown <= 0.25`
- 相对表现：`excess_return_vs_csi300 >= 0`

**S2r 入口条件**（满足任一）：
1. 上述 4 项中任意 2 项不达标；
2. `validation_gate_decision = FAIL`；
3. `s2_cross_section_probe.top_bottom_spread_5d <= 0`。

**S2r 出口条件**（全部满足）：
1. 产出 `s2r_patch_note`，明确“修复项→目标指标→影响范围”；
2. 重新执行 `eq recommend --date {trade_date}` 与 `eq backtest --engine vectorized --start {start} --end {end}`；
3. 新版 `quality_gate_report.status = PASS`；
4. 在 `review.md` 写明“回到 S3b 重验后是否可进入 S4”的消费结论。

> 单一问题最多允许 2 次 S2r 循环；连续 2 次仍 FAIL 时，阻断主线并升级到路线图评审。

### 8.6 退出五件套

- **run**: `eq pas --date {trade_date}` + `eq backtest --engine vectorized --start {start} --end {end}` + `eq backtest-quicklook --start {start} --end {end}`
- **test**: `pytest tests/contracts/test_pas_* tests/contracts/test_backtest_* tests/contracts/test_analysis_* tests/contracts/test_backtest_quality_gate.py -q`
- **artifact**: `stock_pas_daily` + `triple_signal_increment_report` + `backtest_results` + `performance_metrics` + A/B/C 报表 + `quicklook_equity_curve` + `quality_gate_report`
- **review**: `Governance/specs/spiral-s3/review.md`
- **sync**: 最小同步 5 文件

### 8.7 遗产产出

- 可复用回测引擎与指标模块
- PAS 个股信号标准表
- 三算法增量价值报告（`triple_signal_increment_report`）
- 对照基线数据资产
- 绩效指标标准表
- 轻量可视化产物（`quicklook_equity_curve`）
- S3 质量门报告（`quality_gate_report`）

### 8.8 降级策略

1. Qlib 环境不通：以向量化引擎为收口基线，Qlib 作为研究旁路
2. 对照任务超时：对照延后到 S6 不破圈
3. 质量门连续失败：锁定 S4 入口，转入 S2r 并限制回修次数

---

## 9. S4 📜 纸上实战（Strict 6A）

### 9.1 使命

验证"信号 → 订单 → 持仓 → 风控"的真实执行链路，并与 S3 回测口径对齐，防止回测与交易语义分叉。**本圈默认 Strict 6A**。

### 9.1.1 入场前置门禁（B 层触发后）

1. `S1a → S1b → S2a → S2b → S3a → S3b` 已按顺序收口。
2. `quality_gate_report.status = PASS`（来自 `S3b`）。
3. 不存在未闭合的 `S2r` 回修任务。

### 9.2 范围

**In Scope**:
- 订单状态机
- 持仓与 T+1 冻结
- 风控规则（20/30/80、止损8%、止盈15%）
- 回测-交易一致性验证

**Out Scope**:
- 实盘接入
- 异常恢复演练（S6 CP07-S3）

### 9.3 任务分解

| 任务 | 分类 | 主要产出 | 存在理由 | 工时 |
|---|---|---|---|---:|
| S4-T1 订单管理 | 核心基础设施 | `trade_records` | 交易链路最小单元 | 1.0d |
| S4-T2 持仓 / T+1 管理 | 核心基础设施 | `positions` + `t1_frozen` | A 股规则刚性要求 | 1.1d |
| S4-T3 风控引擎 | 核心基础设施 | `risk_events` | 防止执行失控 | 1.2d |
| S4-T4 口径对齐测试 | 外挂 ENH-04 | parity tests | 确保回测与交易一致 | 1.2d |

### 9.4 错误处理

| 场景 | 级别 | 处理 |
|---|---|---|
| 非交易日下单 | P0 | 阻断 |
| 涨停买入 / 跌停卖出 | P0 | 阻断并记录 risk_event |
| T+1 违规卖出 | P0 | 阻断 |
| 风控规则冲突 | P0 | 执行更严格规则 |
| 资金不足 | P1 | 降仓或跳过 |

### 9.5 验收门禁

1. `eq trade --mode paper --date {trade_date}` 可运行
2. `trade_records / positions / risk_events` 三表可追溯
3. 订单状态机可重放
4. 回测-交易同信号一致性测试通过
5. Strict 6A 文档证据齐全
6. 产出 `paper_trade_consumption_case`（至少 1 例“推荐→下单→风控→持仓调整”闭环）并给出消费结论

### 9.6 退出五件套

- **run**: `eq trade --mode paper --date {trade_date}`
- **test**: `pytest tests/contracts/test_trading_* tests/contracts/test_backtest_trading_signal_parity.py -q`
- **artifact**: `trade_records` + `positions` + `t1_frozen` + `risk_events` + `paper_trade_consumption_case`
- **review**: `Governance/specs/spiral-s4/review.md`
- **sync**: 最小同步 5 文件

### 9.7 遗产产出

- 纸上交易主链实现
- 风控事件标准日志
- 回测-交易一致性基线测试

### 9.8 降级策略

Strict 6A 文档负担过重时：仅保最小 6A 证据，不做额外文档扩张。

---

## 10. S5 🖥️ 可见之眼

### 10.1 使命

将 L3/L4 数据转为可见、可导出、可归档的产物，支撑日常使用与复盘。S5 是系统从"后端可运行"跨越到"前端可使用"的关键一步。

### 10.2 范围

**In Scope**:
- Streamlit 单页 Dashboard（温度、行业、TopN、持仓）
- 日报自动生成
- CSV / Markdown 导出与归档

**Out Scope**:
- 高级筛选（CP08-S2，留 S6 或后续）
- 深度归因漂移（S6）

### 10.3 任务分解

| 任务 | 分类 | 主要产出 | 存在理由 | 工时 |
|---|---|---|---|---:|
| S5-T1 Dashboard 最小版 | 核心基础设施 | `src/gui/app.py` 可运行 | 形成用户可视入口 | 1.2d |
| S5-T2 日报生成 | 核心基础设施 | `daily_report` + markdown 文件 | 固化每日证据 | 1.0d |
| S5-T3 导出归档 | 外挂 ENH-07 | CSV/MD 导出规范 | 运营可复查、可流转 | 0.8d |

### 10.4 错误处理

| 场景 | 级别 | 处理 |
|---|---|---|
| 上游表缺失 | P1 | 空态展示 + 告警 |
| 导出失败 | P2 | 重试并提示 |
| 数据刷新超时 | P2 | 回退最近快照 |

### 10.5 验收门禁

1. `eq gui --date {trade_date}` 可启动
2. 页面符合 A 股红涨绿跌
3. `daily_report` 可生成并归档
4. 导出文件与页面关键指标一致
5. 产出 `daily_insight_action_log`（至少 1 条“洞察→动作建议→预期影响”消费结论）

### 10.6 退出五件套

- **run**: `eq gui --date {trade_date}`
- **test**: `pytest tests/contracts/test_gui_* tests/contracts/test_analysis_* -q`
- **artifact**: 页面截图 + `daily_report` + 导出文件 + `daily_insight_action_log`
- **review**: `Governance/specs/spiral-s5/review.md`
- **sync**: 最小同步 5 文件

### 10.7 遗产产出

- GUI 基线模板
- 日报模板与归档命名规范
- 面向运营的可见化入口

### 10.8 降级策略

Plotly 图表实现超时：先用 Streamlit 原生组件出最小版，Plotly 图表延后到 S6。

---

## 11. S6 🔒 稳态收敛

### 11.1 使命

清债、稳态、校准。S6 的目标是让系统从"能跑"转到"可持续运行"，形成收敛里程碑。

### 11.2 范围

**In Scope**:
- 全链路重跑一致性
- P0 / P1 债务清偿
- Validation 权重对照最小版（CP10-S2）
- 归因与漂移报告（CP09-S3）

**Out Scope**:
- 新功能扩张
- 实盘接入

### 11.3 任务分解

| 任务 | 分类 | 主要产出 | 存在理由 | 工时 |
|---|---|---|---|---:|
| S6-T1 全链路重跑与一致性报告 | 核心基础设施 | run-all diff 报告 | 验证系统确定性 | 1.0d |
| S6-T2 债务清偿 | 核心基础设施 | debts P0/P1 清零记录 | 防技术债滚雪球 | 1.0d |
| S6-T3 CP10-S2 最小权重对照 | 核心算法 | baseline vs candidate 结论 | 让权重变更有证据 | 1.0d |
| S6-T4 归档与冻结全检 | 外挂 ENH-08 | freeze 报告 + 里程碑归档 | 防文档/实现漂移 | 1.0d |

### 11.4 错误处理

| 场景 | 级别 | 处理 |
|---|---|---|
| run-all 前后结果不可解释漂移 | P0 | 阻断收口，回溯变更 |
| 债务项无法关闭 | P1 | 明确延期圈号与阻断影响 |
| candidate 权重不优于 baseline | P1 | 回退 baseline |
| freeze_check 失败 | P0 | 阻断并定位变更来源 |

### 11.5 验收门禁

1. `eq run-all --start {start} --end {end}` 两次结果一致（允许时间戳差异）
2. `Governance/record/debts.md` 中 P0/P1 均有处置结论
3. `validation_weight_report` 与 `validation_weight_plan` 可追溯
4. 归因报告可生成且输入链可追溯
5. 冻结检查通过
6. 产出 `end_to_end_value_proof`（含 ROI、情绪主线相对基线结论、下一周期 Go/No-Go）

### 11.6 退出五件套

- **run**: `eq run-all --start {start} --end {end}`
- **test**: `pytest -q`（含 contracts / canary / parity）
- **artifact**: 一致性报告 + 债务清偿记录 + validation weight 报告 + freeze 报告 + `end_to_end_value_proof`
- **review**: `Governance/specs/spiral-s6/review.md`
- **sync**: 最小同步 5 文件

### 11.7 遗产产出

- 重跑一致性基线
- 权重更新证据链
- 可持续运营的收敛里程碑

### 11.8 降级策略

权重对照无明显优化：维持 baseline 权重，记录"无变更"结论，不强行改权重。

### 11.9 S6 后持续运营小循环（周频，新增）

S6 之后不再按“大圈建设”，改为周频小循环，目标是“持续消费能力并持续纠偏”：

| 周期步骤 | 核心动作 | 最小命令 | 最小产物 |
|---|---|---|---|
| 周复盘（周一） | 汇总上周绩效、风险与漂移 | `eq ops-review --week {yyyyww}` | `weekly_ops_review` |
| 周调参（周二-周三） | 基于处方与复盘结论生成候选权重/参数 | `eq weight-tune --mode candidate --week {yyyyww}` | `candidate_weight_plan` |
| 周重验（周四） | 以候选方案重跑回测与关键契约测试 | `eq backtest --engine vectorized --start {start} --end {end}` + `pytest tests/contracts/test_validation_* tests/contracts/test_backtest_* -q` | `weekly_revalidation_report` |
| 周决策（周五） | 决定采纳/回退/继续观察并登记证据 | `eq ops-close --week {yyyyww}` | `weekly_ops_decision` |

周频收口硬门禁：`weekly_ops_review`、`candidate_weight_plan`、`weekly_revalidation_report`、`weekly_ops_decision` 四件套缺一不可。

---

## 12. 风险矩阵

| 风险 | 影响 Spiral | 概率 | 影响度 | 缓解策略 |
|---|---|---|---|---|
| TuShare API 不可用/限流 | S0 | 中 | 高 | mock 数据兜底；canary 离线包 |
| S2 复杂度过高导致超时 | S2 | 高 | 高 | 优先保 IRS+Validation；S2b 双算法集成可后置 |
| Qlib 环境与本地向量化口径不一致 | S3 | 中 | 中 | 以向量化为收口基线；Qlib 为旁路 |
| S3 质量门阈值设置不当（误放行/过严） | S3/S2r | 中 | 高 | 采用组合阈值并按月复核阈值 |
| Strict 6A 文档负担拖慢 S4 | S4 | 中 | 中 | 仅保最小 6A 证据 |
| 回测与交易语义分叉 | S4 | 低 | 高 | S4-T4 显式口径对齐测试 |
| Plotly 图表实现复杂度 | S5 | 低 | 低 | Streamlit 原生组件先行 |
| 全链路重跑发现不可解释漂移 | S6 | 中 | 高 | 阻断收口，逐层回溯定位 |
| S6 后运营小循环中断 | S6+ | 中 | 中 | 周频四件套硬门禁（复盘/调参/重验/决策） |
| 文档先行但实现滞后（当前最大风险） | 全局 | 高 | 高 | 从 S0 开始立即写代码；铁律7 强制执行 |

---

## 13. 治理集成

### 13.1 与治理体系的接口

| 治理组件 | 在本路线图中的角色 |
|---|---|
| 系统铁律 | 每个 Spiral 验收时的合规检查基线 |
| 核心原则 | 冻结区定义的来源 |
| 6A 工作流 | 每个 Spiral 执行时遵循的步骤框架 |
| CP 能力包 | 每个 Spiral 的 Slice 来源 |
| Task 模板 | 每个子任务的标准卡片格式 |
| 技术债登记 | 每圈 review 时的同步目标 |
| 可复用资产 | 遗产登记与跨 Spiral 复用的依据 |

### 13.2 文档同步规则

每圈收口时强制更新：
1. `Governance/specs/spiral-s{N}/final.md`
2. `Governance/record/development-status.md`
3. `Governance/record/debts.md`
4. `Governance/record/reusable-assets.md`
5. `Governance/Capability/SPIRAL-CP-OVERVIEW.md`

本路线图自身：仅在 Spiral 完成或路线调整时更新，不要求每圈都改。

---

## 14. 使用指南

### 14.1 日常查阅

- 看全局进度 → 第 3 节 路线总览看板
- 看当前圈做什么 → 找到对应 Spiral 章节的任务分解
- 看上下游依赖 → 第 4 节 依赖链与遗产链
- 看风险 → 第 12 节 风险矩阵

### 14.2 执行时

1. 开始新 Spiral → 更新看板状态为 🚧
2. 若处于 S1-S3 段，按 `S1a/S1b/S2a/S2b/S3a/S3b` 顺序执行
3. 拆解 Task → 使用 `Governance/Capability/SPIRAL-TASK-TEMPLATE.md`
4. 执行 → 遵循 `Governance/steering/6A-WORKFLOW.md`
5. 收口 → 检查五件套 + 验收门禁
6. 完成 → 更新看板状态为 ✅，更新遗产表

### 14.3 路线调整时

1. 在本文档对应 Spiral 章节记录调整原因
2. 更新看板状态与工时
3. 若涉及 CP 契约变化，同步更新 `CP-*.md`
4. 若涉及主计划变化，同步更新 `docs/design/enhancements/eq-improvement-plan-core-frozen.md`

### 14.4 S0 启动剧本（可直接执行）

建议按以下顺序执行（示例日：`20260207`）：

1. 运行数据闭环：
   - `eq run --date 20260207 --source mock`
2. 运行最小测试：
   - `pytest tests/canary tests/contracts/test_data_* -q`
3. 检查关键产物：
   - DuckDB 中存在 `market_snapshot`
   - 出错场景可生成 `error_manifest.json`
4. 写入消费证据：
   - 在 `review.md` 记录“谁消费了 S0 产物（S1 MSS）”
5. 收口判定：
   - 五件套齐全后，S0 状态从 `🚧` 更新为 `✅`

### 14.5 单圈收口最小清单（复制即用）

- [ ] `run` 命令执行成功并有日志
- [ ] `test` 自动化测试通过并有日志
- [ ] `artifact` 产物可定位且可追溯
- [ ] `consumption` 有“谁消费/怎么消费/消费结论”
- [ ] `review` 已记录偏差、风险、降级策略
- [ ] `sync` 已完成最小 5 文件同步

---

## 变更记录

| 版本 | 日期 | 变更 |
|---|---|---|
| v1.7.0 | 2026-02-13 | 触发 B 层重排：S1-S3 切换为 `S1a→S1b→S2a→S2b→S3a→S3b` 主执行链；补齐各子圈入口/出口门禁；S4 入场条件升级为 `S3b` 收口 + `quality_gate_report.status=PASS` |
| v1.6.0 | 2026-02-13 | 执行化增强：新增 2.8 参数默认值、2.9 证据落位、2.10 状态推进条件；新增 14.4 S0 启动剧本与 14.5 单圈收口清单，使路线图可直接执行 |
| v1.5.0 | 2026-02-13 | A 层整改生效：统一状态口径（Draft/Candidate/Approved/Effective）；明确当前为 Effective（A 层）且 B 层重排保持提案态；执行入口收敛为 VORTEX+DEPENDENCY（CP-*.md 仅历史回顾） |
| v1.4.1 | 2026-02-12 | 升级为执行版：补齐 S1-T5/S2-T6/S3-T5 的 run/test/artifact；新增 S3 质量门阈值与 S2r 入口/出口；为 S4/S5/S6 增加消费硬门禁；新增 S6 后周频运营小循环 |
| v1.3.1 | 2026-02-12 | 链接口径整理：收敛 `draft/` 描述为正式桥梁入口；主计划与 CP 主控引用改为完整路径 |
| v1.3.0 | 2026-02-12 | 术语收敛为“核心算法/核心基础设施/系统外挂”；在 3.1 增补 S2 父圈拆分说明，显式对齐 1-3 Slice 约束 |
| v1.2.0 | 2026-02-12 | 新增 3.3 三分类工时分布（核心算法/基础设施/外挂）；所有任务表分类列统一为三层口径并标注 ENH 编号；对齐 `docs/design/README.md` 三层结构 |
| v1.1.1 | 2026-02-12 | 修复复核残留：消除 `||` 表格语法错误；S2 切片约束文案改为"父圈视图+执行拆圈"；修正"囨入"错字 |
| v1.1.0 | 2026-02-12 | P1: S2/S3 CP 分配对齐 SPIRAL-CP-OVERVIEW（S2 含 CP-05 Integration，S3 转为 CP-10/CP-06/CP-09 回测闭环）；P1: S6 CP 从"全部"改为 CP-10/CP-07/CP-09 合规切片；P2: 修正任务模板路径为完整路径；P3: 统一 risk_events 命名 |
| v1.0.0 | 2026-02-12 | 初版：融合业界 Roadmap 最佳实践与项目治理体系，覆盖 S0-S6 全周期 |
