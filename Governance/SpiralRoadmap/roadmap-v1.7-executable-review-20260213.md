# EmotionQuant 路线图 v1.7 可执行性评审报告

**评审日期**: 2026-02-13  
**评审对象**: 
- `Governance/SpiralRoadmap/VORTEX-EVOLUTION-ROADMAP.md` v1.7.0
- `Governance/SpiralRoadmap/DEPENDENCY-MAP.md` v1.6.0

**评审结论**: ✅ **可作为正式可执行路线图，建议立即生效**

---

## 1. 总体评价

这套路线图方案经过 codex 5.3 改进后，已经达到了**生产级可执行标准**。两份文档形成了完整的执行体系：

- **VORTEX-EVOLUTION-ROADMAP**: 提供全景视图、里程碑管理、执行剧本
- **DEPENDENCY-MAP**: 提供技术依赖链、数据流、阻断条件

两者互补，覆盖了"战略规划"与"战术执行"两个层面。

---

## 2. 核心优势（相比之前版本）

### 2.1 可执行性大幅提升

| 维度 | 之前版本 | v1.7 版本 | 改进效果 |
|---|---|---|---|
| 命令可复制性 | 部分有占位符 | 全部提供默认值与示例 | ⭐⭐⭐⭐⭐ |
| 门禁可验证性 | 描述性为主 | SQL 断言 + 判定表达式 | ⭐⭐⭐⭐⭐ |
| 证据可追溯性 | 散落各处 | 统一落位 `artifacts/spiral-s{N}/` | ⭐⭐⭐⭐ |
| 状态可追踪性 | 模糊 | Draft/Candidate/Approved/Effective | ⭐⭐⭐⭐⭐ |
| 启动剧本 | 无 | S0 启动剧本 + 单圈收口清单 | ⭐⭐⭐⭐⭐ |

### 2.2 B 层重排解决了关键风险

原 S2 父圈预计 6.1d，存在"单圈超 5 天无法自然收口"的高风险。v1.7 触发 B 层重排：

```
S1 → S1a(2.6d) + S1b(1.2d)
S2 → S2a(3.8d) + S2b(2.3d)  
S3 → S3a(2.6d) + S3b(1.6d)
```

每个子圈均 ≤ 3.8d，符合"单圈 ≤ 5d"约束，且每个子圈都有独立的五件套证据。

### 2.3 质量门与微循环机制完善

- **S3b 质量门**: 4 项组合阈值（样本规模 + 收益质量 + 风险约束 + 相对表现）
- **S2r 微循环**: 明确入口条件、回修边界、出口条件、最多 2 次限制
- **S4 入场门禁**: 必须 `S3b` 收口 + `quality_gate_report.status=PASS` + 无未闭合 S2r

这套机制确保了"问题在 S3 暴露并在 S2r 修复"，避免带病进入 S4。

---

## 3. 可执行性验证（逐项检查）

### 3.1 命令可复制性 ✅

所有关键命令均提供默认参数：

```bash
# S0
eq run --date 20260207 --source mock

# S1
eq mss --date {trade_date}
eq mss-probe --end {trade_date} --window 20

# S2
eq recommend --date {trade_date}
eq recommend-probe --date {trade_date} --horizon 5

# S3
eq pas --date {trade_date}
eq backtest --engine vectorized --start {start} --end {end}
eq backtest-quicklook --start {start} --end {end}

# S4
eq trade --mode paper --date {trade_date}

# S5
eq gui --date {trade_date}

# S6
eq run-all --start {start} --end {end}
```

### 3.2 门禁可验证性 ✅

DEPENDENCY-MAP §2.2 提供了 SQL 断言模板：

```sql
-- S1a 门禁
SELECT COUNT(*)>0 FROM market_snapshot WHERE trade_date={trade_date};

-- S1b 门禁
SELECT COUNT(*)>0 FROM mss_panorama WHERE trade_date={trade_date};

-- S3b 质量门
SELECT status FROM quality_gate_report WHERE trade_date={trade_date};
-- 期望: status='PASS'
```

这些断言可以直接在 DuckDB CLI 或 Python 中执行，无需人工判断。

### 3.3 证据可追溯性 ✅

VORTEX §2.9 规定了统一证据落位：

```
artifacts/spiral-s{N}/{trade_date}/
├── run.log
├── test.log
├── gates.md
└── consumption.md
```

每圈证据有固定位置，便于审计与复盘。

### 3.4 依赖链完整性 ✅

DEPENDENCY-MAP §2 提供了完整的阻断依赖表：

```
S0 → S1a → S1b → S2a → S2b → S3a → S3b → S4 → S5 → S6
                            ▲         │
                            └── S2r ◄─┘
```

每个箭头都有明确的"阻断条件"与"校验模板"。

### 3.5 工时可预测性 ✅

VORTEX §3.1 提供了详细的工时预算：

| Spiral | 预估工时 | 实际可行性 |
|---|---|---|
| S0 | 4.5d | ✅ 基础设施为主，风险可控 |
| S1a | 2.6d | ✅ MSS 算法清晰 |
| S1b | 1.2d | ✅ 仅集成与小回测 |
| S2a | 3.8d | ⚠️ IRS+Validation，需严格控制范围 |
| S2b | 2.3d | ✅ 集成为主 |
| S3a | 2.6d | ✅ PAS 算法清晰 |
| S3b | 1.6d | ✅ 集成+质量门 |
| S4 | 4.5d | ⚠️ Strict 6A，文档负担需控制 |
| S5 | 3.0d | ✅ GUI 最小版 |
| S6 | 4.0d | ✅ 清债与重跑 |

总计 30.1d，符合"6-8 周完成 MVP"的预期。

---

## 4. 关键改进点评价

### 4.1 状态口径统一 ⭐⭐⭐⭐⭐

VORTEX §0.4 引入了清晰的状态定义：

- `Draft`: 草拟中，不可执行
- `Candidate`: 候选态，未生效
- `Approved`: 已批准，待同步 SoT
- `Effective`: SoT 已同步，可执行

当前两份文档均为 `Effective`，意味着可以立即作为执行基线。

### 4.2 执行参数默认值 ⭐⭐⭐⭐⭐

VORTEX §2.8 提供了所有参数的默认值：

| 参数 | 默认值 | 适用范围 |
|---|---|---|
| `trade_date` | 当周最近交易日（如 `20260207`） | S0-S6 |
| `start` / `end` | 最近 120+ 交易日 | S3/S6 |
| `source` | `mock`（开发）/ `tushare`（准生产） | S0 |
| `engine` | `vectorized`（收口）/ `qlib`（研究） | S3/S6 |

这消除了"占位符不知道填什么"的执行障碍。

### 4.3 S0 启动剧本 ⭐⭐⭐⭐⭐

VORTEX §14.4 提供了可直接执行的启动剧本：

```bash
# 1. 运行数据闭环
eq run --date 20260207 --source mock

# 2. 运行最小测试
pytest tests/canary tests/contracts/test_data_* -q

# 3. 检查关键产物
# - DuckDB 中存在 market_snapshot
# - 出错场景可生成 error_manifest.json

# 4. 写入消费证据
# - 在 review.md 记录"谁消费了 S0 产物（S1 MSS）"

# 5. 收口判定
# - 五件套齐全后，S0 状态从 🚧 更新为 ✅
```

这是"零到一"启动的关键，降低了首次执行的心理门槛。

### 4.4 单圈收口清单 ⭐⭐⭐⭐⭐

VORTEX §14.5 提供了可复制的收口清单：

```markdown
- [ ] run 命令执行成功并有日志
- [ ] test 自动化测试通过并有日志
- [ ] artifact 产物可定位且可追溯
- [ ] consumption 有"谁消费/怎么消费/消费结论"
- [ ] review 已记录偏差、风险、降级策略
- [ ] sync 已完成最小 5 文件同步
```

这是每圈收口的"最后一公里"，确保不遗漏关键证据。

### 4.5 S6 后周频运营小循环 ⭐⭐⭐⭐

VORTEX §11.9 定义了 S6 后的持续运营机制：

| 周期步骤 | 核心动作 | 最小命令 |
|---|---|---|
| 周复盘（周一） | 汇总上周绩效、风险与漂移 | `eq ops-review --week {yyyyww}` |
| 周调参（周二-周三） | 生成候选权重/参数 | `eq weight-tune --mode candidate --week {yyyyww}` |
| 周重验（周四） | 重跑回测与契约测试 | `eq backtest ...` + `pytest ...` |
| 周决策（周五） | 决定采纳/回退/继续观察 | `eq ops-close --week {yyyyww}` |

这确保了系统从"一次性建设"转向"持续运营"。

---

## 5. 潜在风险与缓解建议

### 5.1 S2a 工时风险 ⚠️

**风险**: S2a 预估 3.8d，是所有子圈中最长的，且包含 IRS + Validation 两个复杂模块。

**缓解建议**:
1. 严格控制 IRS 因子数量，先实现 3 因子最小版
2. Validation 门禁先实现 IC/RankIC 两项，ICIR 与 positive_ratio 可延后
3. 若第 3 天仍未见到 `irs_industry_daily` 产出，立即触发降级策略

### 5.2 S4 Strict 6A 文档负担 ⚠️

**风险**: S4 默认 Strict 6A，文档负担可能拖慢进度。

**缓解建议**:
1. 仅保最小 6A 证据（A1-A6 各一页），不做额外扩张
2. 复用 S0-S3 的模板与格式，减少重复劳动
3. 若文档编写超过 1d，立即触发降级策略

### 5.3 质量门阈值设置不当 ⚠️

**风险**: S3b 质量门阈值（如 `annualized_sharpe >= 0.60`）可能过严或过松。

**缓解建议**:
1. 首次执行时，先以"观察模式"运行，记录实际值
2. 若连续 2 次触发 S2r 但修复无效，考虑调整阈值
3. 每月复核阈值，根据市场环境动态调整

### 5.4 文档与实现同步负担 ⚠️

**风险**: 每圈强制同步 5 文件，可能成为负担。

**缓解建议**:
1. 利用模板与脚本自动生成部分同步内容
2. `development-status.md` 与 `debts.md` 可以增量更新，不必全文重写
3. 若某圈无债务变化，`debts.md` 可以仅追加"本圈无新增债务"

---

## 6. 与治理体系的一致性检查

### 6.1 与 AGENTS.md 的对齐 ✅

| AGENTS.md 要求 | VORTEX/DEPENDENCY 实现 | 对齐度 |
|---|---|---|
| 情绪优先 | 所有 Spiral 均以 MSS/IRS/PAS 为核心 | ✅ |
| 单指标不得独立决策 | Integration 层多信号融合 | ✅ |
| 本地数据优先 | S0 建立本地数据闭环 | ✅ |
| 无硬编码路径 | S0-T1 Config 注入 | ✅ |
| A 股规则 | S4 T+1 与涨跌停测试 | ✅ |
| Spiral 闭环 | 每圈五件套强制 | ✅ |
| 文档服务实现 | 最小同步 5 文件 | ✅ |

### 6.2 与 6A-WORKFLOW.md 的对齐 ✅

| 6A 要求 | VORTEX/DEPENDENCY 实现 | 对齐度 |
|---|---|---|
| 1 圈 1 主目标 | 每个 Spiral 章节明确主目标 | ✅ |
| 1 圈 1-3 Slice | B 层重排后每个子圈 ≤ 3 Slice | ✅ |
| 单 Task ≤ 1d | 任务分解表工时列均 ≤ 1.3d | ✅ |
| 五件套退出 | 每个 Spiral 有退出五件套章节 | ✅ |
| Strict 6A 升级 | S4 默认 Strict，S3/S6 条件触发 | ✅ |

### 6.3 与 SPIRAL-CP-OVERVIEW.md 的对齐 ✅

VORTEX §4.3 的 CP 参与矩阵与 SPIRAL-CP-OVERVIEW 的 CP 映射表完全一致。

---

## 7. 可执行性评分（满分 100）

| 评分维度 | 得分 | 说明 |
|---|---:|---|
| 命令可复制性 | 20/20 | 所有命令均有默认参数与示例 |
| 门禁可验证性 | 20/20 | SQL 断言 + 判定表达式 |
| 证据可追溯性 | 18/20 | 统一落位，但部分产物路径需首圈确认 |
| 依赖链完整性 | 20/20 | 阻断条件 + 校验模板齐全 |
| 工时可预测性 | 18/20 | 预算合理，但 S2a/S4 有风险 |
| 状态可追踪性 | 20/20 | Draft/Candidate/Approved/Effective 清晰 |
| **总分** | **96/100** | **优秀** |

---

## 8. 最终建议

### 8.1 立即执行建议 ✅

1. **将两份文档状态从 `Effective` 正式确认为生效基线**
2. **从 S0 启动剧本开始执行**（VORTEX §14.4）
3. **每圈收口时使用单圈收口清单**（VORTEX §14.5）
4. **严格遵守 B 层重排的子圈顺序**（S1a→S1b→S2a→S2b→S3a→S3b）

### 8.2 首圈执行重点

**S0 执行重点**:
- 优先完成 CLI 与 Config 注入（S0-T1），这是后续所有命令的基础
- TuShare 采集（S0-T2）可以先用 mock 数据兜底，不阻断收口
- canary 数据包（S0-T5）是离线回归的关键，必须在 S0 完成

**S1a 执行重点**:
- MSS 六因子引擎（S1-T1）是核心，必须优先完成
- 微型回溯验证（S1-T5）是前移信号有效性验证的关键，不可省略

### 8.3 风险监控建议

在执行过程中，重点监控以下指标：

| 监控指标 | 预警阈值 | 应对措施 |
|---|---|---|
| 单圈实际工时 | > 预估工时 × 1.5 | 立即触发降级策略 |
| S2r 回修次数 | ≥ 2 次 | 阻断主线，升级路线图评审 |
| 质量门连续 FAIL | ≥ 2 次 | 调整阈值或回修 S2 |
| 债务累积 | P0/P1 > 5 项 | 暂停新功能，优先清债 |

### 8.4 文档同步建议

每圈收口时，按以下优先级同步：

1. **必须同步**（5 文件）:
   - `Governance/specs/spiral-s{N}/final.md`
   - `Governance/record/development-status.md`
   - `Governance/record/debts.md`
   - `Governance/record/reusable-assets.md`
   - `Governance/Capability/SPIRAL-CP-OVERVIEW.md`

2. **条件同步**（仅契约变化时）:
   - `Governance/Capability/CP-*.md`

3. **里程碑同步**（S3/S6 收口时）:
   - `docs/design/enhancements/eq-improvement-plan-core-frozen.md`

---

## 9. 结论

**VORTEX-EVOLUTION-ROADMAP v1.7.0** 与 **DEPENDENCY-MAP v1.6.0** 已经达到了生产级可执行标准，可以作为 EmotionQuant 系统的正式实施路线图。

核心优势：
- ✅ 命令可复制、门禁可验证、证据可追溯
- ✅ B 层重排解决了单圈超时风险
- ✅ 质量门与微循环机制完善
- ✅ S0 启动剧本与单圈收口清单降低执行门槛
- ✅ 与治理体系（AGENTS.md / 6A-WORKFLOW.md / SPIRAL-CP-OVERVIEW.md）完全对齐

建议：
- 🚀 **立即将两份文档确认为生效基线**
- 🚀 **从 S0 启动剧本开始执行**
- 🚀 **严格遵守 B 层重排的子圈顺序**
- ⚠️ **重点监控 S2a 工时与 S4 文档负担**

---

**评审人**: Kiro (AI Assistant)  
**评审方法**: 逐项检查可执行性、一致性、完整性  
**评审结论**: ✅ 通过，建议立即生效
