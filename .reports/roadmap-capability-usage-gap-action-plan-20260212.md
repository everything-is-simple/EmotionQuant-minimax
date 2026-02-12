# 路线图能力使用裂缝修订行动计划（2026-02-12，执行版）

## 1. 目标

在不改核心语义、不新增主干 CP 的前提下，修复路线图中“能力建设与能力使用断裂”的 3 个问题：

1. 反馈滞后（S1/S2 信号到 S3 才首次实证）
2. 可见性延迟（S5 才有可视入口）
3. 检验单向（Gate/对照不驱动上游修正）

---

## 2. 约束

1. 不改冻结区定义（情绪优先、A 股规则、核心命名与主契约）
2. 不引入新的主线 Spiral，仅允许“子圈/微循环”作为回修机制
3. 仅做最小增量：补消费门禁、补轻量产物、补触发规则
4. 继续遵循 6A 与五件套收口

---

## 3. 修订范围

## 3.1 文档范围

1. `Governance/SpiralRoadmap/VORTEX-EVOLUTION-ROADMAP.md`
2. `Governance/SpiralRoadmap/DEPENDENCY-MAP.md`

## 3.2 变更包

1. 全局契约新增“能力消费门禁”
2. S1/S2 前移“微型实证”任务与验收条款
3. S2 Validation 增加“处方化产物”（不破坏既有 Gate 主契约）
4. S3 前移“轻量可视化”与“微循环触发”
5. 依赖图新增 S3→S2r 反馈链路
6. 更新工时看板、风险矩阵、变更记录

---

## 4. 执行步骤

| 步骤 | 产出 | 验收标准 |
|---|---|---|
| P1 | 更新全局执行契约 | 出现“能力消费门禁”条款，明确每圈至少 1 个消费场景 |
| P2 | 更新 S1/S2/S3 任务与门禁 | S1/S2 有微型实证，S3 有轻量可视与微循环触发 |
| P3 | 更新依赖链与遗产链 | 出现 S3→S2r 回修路径；新增微型实证与处方产物消费者 |
| P4 | 更新看板与风险 | 工时表/风险矩阵与新增任务一致 |
| P5 | 更新变更记录 | 两份文档均有当日版本记录 |

---

## 5. 具体修订清单

1. 在 `VORTEX` 第 2 节新增“2.7 能力消费门禁（新增）”
2. 在 `VORTEX` 第 6 节（S1）新增 `S1-T5` 微型回溯验证
3. 在 `VORTEX` 第 7 节（S2）新增 `S2-T6` 截面对照验证，并将 `validation_prescription` 纳入 S2 产物
4. 在 `VORTEX` 第 8 节（S3）新增 `S3-T5` 轻量可视化；在验收门禁加入“触发 S2r”的组合阈值判定
5. 在 `VORTEX` 第 4.1 依赖图标注微循环，补 `S2r` 说明
6. 在 `DEPENDENCY-MAP` 阻断依赖后新增“反馈微循环（非主线）”条款
7. 在 `DEPENDENCY-MAP` 全链路图与遗产消费说明中补充微型实证/处方/轻量可视路径

---

## 6. 风险与缓解

| 风险 | 说明 | 缓解 |
|---|---|---|
| 微型实证导致过拟合 | 过度依赖单指标阈值 | 采用组合触发（Sharpe + 回撤 + 样本规模） |
| 文档复杂度上升 | 新增条款过多影响可读性 | 坚持最小增量，放弃新增章节级大重构 |
| 与冻结区冲突 | 处方字段可能误改主契约 | 采用旁路产物 `validation_prescription`，不改主 Gate 字段 |

---

## 7. 完成定义（DoD）

1. 两份路线图文档均完成修订并可读
2. 新增条款能够回答“能力何时被谁在何场景消费”
3. S3 具备“失败即触发回修子圈”的规则描述
4. 所有改动可被后续 Spiral 任务卡直接引用

---

## 8. 执行化落地映射（本次修订）

### 8.1 VORTEX 执行化补齐

1. S1 新增 `S1-T5`，并补齐 `run/test/artifact`（`eq mss-probe`、`test_mss_probe_*`、`mss_micro_probe_report`）
2. S2 新增 `S2-T6`，并补齐 `run/test/artifact`（`eq recommend-probe`、`test_signal_probe_*`、`validation_prescription` + `s2_cross_section_probe`）
3. S3 新增 `S3-T5`，并补齐 `run/test/artifact`（`eq backtest-quicklook`、`test_backtest_quality_gate.py`、`quicklook_equity_curve` + `quality_gate_report`）

### 8.2 S3 质量门与 S2r 条件

1. 在 S3 新增组合阈值（样本规模、Sharpe、回撤、相对收益）
2. 明确 S2r 入口条件（质量门失败 / Gate FAIL / 截面对照失效）
3. 明确 S2r 出口条件（补丁说明、重跑命令、质量门 PASS、消费结论）

### 8.3 DEPENDENCY-MAP 回路补齐

1. 新增“反馈微循环与新增产物流向”图与产物表
2. 阻断依赖中将 S4 入口升级为 `quality_gate_report.status = PASS`
3. 新增“反馈微循环（非主线）”执行约束（最大回修次数、阻断规则）

### 8.4 S4/S5/S6 消费硬门禁

1. S4：新增 `paper_trade_consumption_case`（推荐→下单→风控→持仓调整）
2. S5：新增 `daily_insight_action_log`（洞察→动作建议→预期影响）
3. S6：新增 `end_to_end_value_proof`（ROI + 相对基线 + Go/No-Go）

### 8.5 S6 后周频小循环

1. 新增周频四步：复盘、调参、重验、决策
2. 新增四件套硬门禁：`weekly_ops_review`、`candidate_weight_plan`、`weekly_revalidation_report`、`weekly_ops_decision`
3. 明确 S6 后从“建设主线”切换为“运营闭环”

---

## 9. 执行状态

1. 本行动计划已升级为执行版口径
2. 对应主文档已同步到：
   - `Governance/SpiralRoadmap/VORTEX-EVOLUTION-ROADMAP.md`
   - `Governance/SpiralRoadmap/DEPENDENCY-MAP.md`

---

## 10. 路线重排提案（新增，待批准）

### 10.1 结论

现有 `S0→S6` 在“能力打包顺序”上偏乐观，尤其 S2 同时承载 IRS/PAS/Validation/Integration，复杂度与返工风险过高。  
建议改为“渐进集成 + 每步见效”的路线：**先 MSS 出手，再 IRS 叠加，再 PAS 收敛**。

### 10.2 新执行骨架（保持 S0-S6 编号，不新增主干 Spiral）

| 阶段 | 目标 | 必做能力 | 必做消费验证 |
|---|---|---|---|
| S0 | 数据根基 | L1/L2、CLI、契约守卫 | canary + mock 回归 |
| S1a | MSS 最小可用 | MSS 因子/温度/周期/趋势 | `mss_micro_probe_report` |
| S1b | MSS 集成试运行 | MSS-only Integration（权重占位） | MSS-only 小回测 + 消费结论 |
| S2a | IRS 叠加 | IRS + Validation 处方 | MSS+IRS 对照结论 |
| S2b | MSS+IRS 集成稳态 | 集成权重重算、质量门初版 | 通过才允许进入 PAS |
| S3a | PAS 叠加 | PAS + Validation 处方 | MSS+IRS+PAS 对照结论 |
| S3b | 全量集成稳态 | 完整 Integration + 质量门 | 通过才进入 S4 |
| S4-S6 | 回测/交易/可视/稳态 | 保持现有定义 | 增加硬消费门禁 |

说明：
1. 不改主干编号，采用 `S1a/S1b、S2a/S2b、S3a/S3b` 子圈切分，符合 6A 的 1-3 Slice 约束。
2. 每次只新增一个核心算法能力，并立刻做集成和效果验证。
3. 任一子圈质量门 FAIL，直接触发回修，不得推进下一子圈。

### 10.3 关键门禁（新增）

1. **增量价值门禁**：新能力叠加后，核心指标不得显著劣化（Sharpe、回撤、超额收益至少满足“2/3 不退化”）。
2. **可解释门禁**：每次叠加必须产出“为什么更好/为什么变差”的归因结论。
3. **推进门禁**：未形成消费结论，不得进入下一能力叠加圈。

### 10.4 文档修订任务（下一步执行）

| 步骤 | 文档 | 改动 |
|---|---|---|
| R1 | `Governance/SpiralRoadmap/VORTEX-EVOLUTION-ROADMAP.md` | 将 S1-S3 改写为 `a/b` 子圈顺序，重排任务表与门禁 |
| R2 | `Governance/SpiralRoadmap/DEPENDENCY-MAP.md` | 依赖链改为 `S1a→S1b→S2a→S2b→S3a→S3b`，补阻断条件 |
| R3 | `Governance/Capability/SPIRAL-CP-OVERVIEW.md` | 同步子圈映射（不改 CP 主契约） |
| R4 | `Governance/specs/spiral-s*/` 模板 | 增加“本圈新增能力消费结论”模板字段 |

### 10.5 风险与控制

| 风险 | 影响 | 控制 |
|---|---|---|
| 子圈增多导致节奏变慢 | 时间线拉长 | 每子圈只保最小闭环，严禁功能扩张 |
| 对照实验噪声大 | 误判能力优劣 | 固定样本窗口 + 固定基线 + 月度复核阈值 |
| 文档更新成本上升 | 执行阻塞 | 仅改桥梁文档与必要模板，不扩写叙事 |

### 10.6 通过标准（批准后才改主路线图）

1. 你确认采用“渐进集成（MSS→IRS→PAS）”作为 S1-S3 主执行策略。
2. 允许将当前 S1-S3 看板工时重算为子圈口径。
3. 同意 S4 入口由“完成 S3”调整为“完成 S3b 且质量门 PASS”。
