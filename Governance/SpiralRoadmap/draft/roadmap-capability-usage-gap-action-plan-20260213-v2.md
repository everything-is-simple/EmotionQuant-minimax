# 路线图能力消费整改行动计划（2026-02-13，v2 评审稿）

## 0. 文档定位

- 状态：Approved（2026-02-13；按 A 层执行）
- 目标：在不改冻结区语义、不新增主干 Spiral 的前提下，完成“能力建设与能力消费”断裂整改。
- 策略：采用“双层执行”。
  - A 层：已达共识项的基线收口（必须执行）
  - B 层：路线重排项的条件提案（批准后执行）

---

## 1. 输入基线与约束

### 1.1 上位约束（SoT）

1. `Governance/steering/系统铁律.md`
2. `Governance/steering/6A-WORKFLOW.md`
3. `Governance/Capability/SPIRAL-CP-OVERVIEW.md`
4. `docs/design/enhancements/eq-improvement-plan-core-frozen.md`

### 1.2 本次整改输入

1. `Governance/SpiralRoadmap/VORTEX-EVOLUTION-ROADMAP.md`
2. `Governance/SpiralRoadmap/DEPENDENCY-MAP.md`
3. `Governance/SpiralRoadmap/draft/roadmap-capability-usage-gap-analysis.md`
4. `Governance/SpiralRoadmap/draft/roadmap-capability-consumption-gap-analysis.md`

### 1.3 不可突破边界

1. 不改情绪优先与“单指标不得独立决策”语义。
2. 不新增主干 Spiral（S0-S6 编号保持）。
3. 微循环仅允许作为回修机制，不得替代主线闭环。
4. 每圈仍以 `run/test/artifact/review/sync` 五件套收口。

---

## 2. 现状判断（用于执行分层）

1. 路线图与依赖图已补入关键整改项（消费门禁、S2r 微循环、S4/S5/S6 消费硬门禁、S6 后周频小循环）。
2. 目前存在“已执行口径”与“待批准重排口径”混叠，影响执行清晰度。
3. `VORTEX-EVOLUTION-ROADMAP.md` 当前状态仍为 Candidate，需补审批与生效动作。
4. 改进行动主计划尚未显式吸收本次新增消费门禁口径，存在 SoT 同步缺口。

---

## 3. 总体执行策略（双层三包四门禁）

### 3.1 双层执行

1. A 层（基线收口层）：只做一致性收口，不做结构重排。
2. B 层（重排提案层）：仅保留提案与触发条件，不直接改主路线结构。

### 3.2 三个变更包

1. `PKG-A1` 口径一致性包：统一“执行版/Candidate/待批准”状态定义与版本记录。
2. `PKG-A2` SoT 同步包：把消费门禁与微循环口径同步到主计划和主控入口。
3. `PKG-B1` 路线重排包：S1-S3 子圈重排提案（仅在触发+批准后落地）。

### 3.3 四个硬门禁

1. 冻结区门禁：不得改核心算法与规则语义。
2. 消费证据门禁：每圈至少 1 个“谁消费/怎么消费/消费结论”证据。
3. 质量阻断门禁：S3 质量门 FAIL 必进 S2r，不得推进 S4。
4. SoT 同步门禁：桥梁文档更新后，必须完成 SoT 同步才可标记“执行版生效”。

---

## 4. A 层执行计划（必须执行）

### 4.1 目标

在不改变当前路线结构的前提下，完成“口径统一、证据闭环、SoT 同步、版本生效”。

### 4.2 任务与验收

| 步骤 | 任务 | 产出 | 验收标准 |
|---|---|---|---|
| A1 | 统一状态口径 | 状态定义段落 + 变更记录 | 清晰区分 Draft/Candidate/Approved/Effective |
| A2 | 校准桥梁文档 | `VORTEX` 与 `DEPENDENCY-MAP` 版本联动 | 两文档的消费门禁、S2r 条件、阻断规则一致 |
| A3 | 同步主控入口 | `SPIRAL-CP-OVERVIEW` 补“消费证据”执行说明 | 不改变 CP 主契约，仅补执行口径 |
| A4 | 同步主计划 | `eq-improvement-plan-core-frozen` 增补消费门禁与微循环口径 | 与 A2 口径一致，无语义冲突 |
| A5 | 同步闭环模板 | `Governance/specs/spiral-s*/` 增加“能力消费结论”字段模板 | 后续每圈可直接复用 |
| A6 | 记录层同步 | `development-status`/`debts`/`reusable-assets` 更新 | 反映新增门禁与执行资产 |

### 4.3 A 层 DoD

1. 路线桥梁文档状态口径统一。
2. SoT（主控+主计划）完成同步。
3. 至少 1 个可复用“消费证据模板”可直接落地。
4. 记录层完成同步，且无“执行版已生效”歧义描述。

---

## 5. B 层路线重排提案（待批准）

### 5.1 提案目标

将 S1-S3 从“集中打包”改为“渐进叠加 + 每步消费验证”，降低返工半径。

### 5.2 提案骨架（不改主干编号）

| 段 | 核心能力 | 最小消费验证 | 推进门禁 |
|---|---|---|---|
| S1a | MSS 最小可用 | `mss_micro_probe_report` | 有明确消费结论 |
| S1b | MSS-only 集成 | MSS-only 小回测结论 | 达标后进 IRS |
| S2a | IRS 叠加 + 处方 | MSS+IRS 对照结论 | 质量门初筛通过 |
| S2b | MSS+IRS 稳态集成 | 稳态推荐消费结论 | 达标后进 PAS |
| S3a | PAS 叠加 + 处方 | 三算法增量价值结论 | 增量不退化 |
| S3b | 全量集成稳态 + 质量门 | `quality_gate_report = PASS` | 才允许进入 S4 |

### 5.3 B 层触发条件（满足任一才启动）

1. S2 父圈预计超出 5 个工作日且无法按子圈自然收口。
2. 连续 2 次出现 S3→S2r 回修。
3. S2/S3 的质量门连续 FAIL 且定位为“结构性打包过重”。

### 5.4 B 层退出条件

1. 子圈拆分后仍满足“每圈 1-3 Slice”。
2. 新依赖链与阻断门禁完整可追溯。
3. 不改变 CP 主契约与冻结区语义。

---

## 6. 文档修订任务清单

### 6.1 A 层（立即）

| 编号 | 文档 | 改动 |
|---|---|---|
| A-R1 | `Governance/SpiralRoadmap/VORTEX-EVOLUTION-ROADMAP.md` | 统一状态口径，明确生效条件 |
| A-R2 | `Governance/SpiralRoadmap/DEPENDENCY-MAP.md` | 对齐阻断与回修规则描述口径 |
| A-R3 | `Governance/Capability/SPIRAL-CP-OVERVIEW.md` | 增补消费证据执行说明（不改 CP 主契约） |
| A-R4 | `docs/design/enhancements/eq-improvement-plan-core-frozen.md` | 补充消费门禁/微循环执行口径 |
| A-R5 | `Governance/specs/spiral-s*/` 模板 | 增加“本圈能力消费结论”字段 |
| A-R6 | `Governance/record/*.md` | 同步状态、债务、可复用资产 |

### 6.2 B 层（批准后）

| 编号 | 文档 | 改动 |
|---|---|---|
| B-R1 | `Governance/SpiralRoadmap/VORTEX-EVOLUTION-ROADMAP.md` | S1-S3 重排为 a/b 子圈执行图 |
| B-R2 | `Governance/SpiralRoadmap/DEPENDENCY-MAP.md` | 依赖链改为 `S1a→S1b→S2a→S2b→S3a→S3b` |
| B-R3 | `Governance/Capability/SPIRAL-CP-OVERVIEW.md` | 同步子圈映射说明 |

---

## 7. 风险与控制

| 风险 | 影响 | 控制策略 |
|---|---|---|
| A 层与 B 层混改 | 执行混乱 | 严格先 A 后 B，未批准不得改主结构 |
| SoT 同步遗漏 | 规则冲突 | 将 SoT 同步设为生效前门禁 |
| 过度文档化 | 开发阻塞 | 只补最小字段与最小证据模板 |
| 质量门阈值误判 | 误回修或误放行 | 采用组合阈值并按月复核 |

---

## 8. 完成定义（v2）

1. A 层任务 A1-A6 全部完成并可追溯。
2. 关键文档状态与版本口径一致。
3. SoT 同步完成且无冲突条款。
4. 消费证据模板可在下一圈直接执行。
5. B 层保持“提案态”，未批准不触发结构重排。
6. 回修阻断规则（S3 FAIL -> S2r）在桥梁文档一致呈现。
7. 所有新增条款可被后续 Spiral 任务卡直接引用。

---

## 9. 待你确认的审批项

1. 是否先按 A 层执行并收口为“正式生效版”。
2. 是否保留 B 层为条件触发提案（暂不立即改主结构）。
3. B 层触发阈值是否采用本稿 3 条标准。
4. A 层完成后是否立即进入主路线图修订执行。
