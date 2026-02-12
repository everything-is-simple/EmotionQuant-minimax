# EmotionQuant 全文档批判性审查 — 第 5 轮

**审查人**: Warp (Claude Opus)
**日期**: 2026-02-11
**轮次**: R5
**范围**: `docs/design/enhancements/` 正式文档 4 份（eq-improvement-plan-core-frozen.md + enhancement-selection-analysis + scheduler-orchestration-design + monitoring-alerting-design）
**累计**: R1-R4 19 项（已修复）+ 本轮 4 = **23 项**

---

## 审查方法

逐文件精读 4 份正式外挂设计文档，与以下权威源交叉对比：

- `docs/reference/tushare/` 实际文件清单
- `docs/design/core-infrastructure/data-layer/data-layer-data-models.md`（L1 表标准命名）
- `Governance/steering/系统铁律.md` / `CORE-PRINCIPLES.md` 当前版本
- `docs/system-overview.md` 当前版本
- `docs/design/enhancements/eq-improvement-plan-core-frozen.md` 当前版本

---

## 发现汇总

| ID | 优先级 | 文件 | 位置 | 问题 |
|---|---|---|---|---|
| P2-R5-01 | P2 | eq-improvement-plan-core-frozen.md | L49 | 引用 `tushare-config-5000-官方.md` 不存在（路径断链） |
| P2-R5-02 | P2 | enhancement-selection-analysis | L26 | 依赖文档表 Validation 行 `||` 双竖线导致首列缺失（表格渲染错误） |
| P3-R5-03 | P3 | enhancement-selection-analysis | L19,28,29 | 依赖文档表 3 处版本号过时 |
| P3-R5-04 | P3 | scheduler-orchestration-design | L41,44 | 就绪检查点表名前缀不一致 |

---

## 问题详述

### P2-R5-01 — 主计划引用不存在的 TuShare 配置文件

**位置**: `eq-improvement-plan-core-frozen.md` line 49

**当前**: `配置权威: docs/reference/tushare/tushare-config-5000-官方.md`

**问题**: `docs/reference/tushare/` 目录下实际文件为：
- `tushare-config.md`
- `tushare-config-10000.md`
- `tushare-topic-index.md`

无 `tushare-config-5000-官方.md`。路径断链。

**修复建议**: 确认正确的配置文件名后修复引用。若 5000 积分配置未单独成文，可改为 `tushare-config.md`（通用配置）。

---

### P2-R5-02 — enhancement-selection-analysis 表格渲染错误

**位置**: `enhancement-selection-analysis_claude-opus-max_20260210.md` line 26

**当前**:
```
|| `docs/design/core-algorithms/validation/factor-weight-validation-data-models.md` | v2.1.1 | 五张 Validation 表、ValidationWeightPlan 桥接 |
```

**问题**: 行首 `||` 双竖线导致 Markdown 表格该行首列（依赖文档）渲染为空，`docs/design/...` 路径显示在"版本"列。

**修复建议**: 去掉多余的 `|`，改为：
```
| `docs/design/core-algorithms/validation/factor-weight-validation-data-models.md` | v2.1.1 | 五张 Validation 表、ValidationWeightPlan 桥接 |
```

---

### P3-R5-03 — enhancement-selection-analysis 依赖文档版本号过时

**位置**: `enhancement-selection-analysis_claude-opus-max_20260210.md` lines 19, 28, 29

**当前 / 实际**:
| 行 | 引用版本 | 当前版本 | 变更内容 |
|---|---|---|---|
| L19 | `system-overview.md` v4.1.2 | v4.1.3 | R3 后补充设计三层导航 |
| L28 | `eq-improvement-plan-core-frozen.md` v2.0.0 | v2.1.0 | 主计划对齐审计 |
| L29 | `系统铁律.md` v5.1.0 | v5.1.1 | R1 铁律5 补充涨跌停比率+申万行业 |

**问题**: 依赖文档表的版本号是"最后一次对齐验证时的版本"，当前已有更新但未重新验证。内容变更较小（R1-R4 微调），不影响外挂裁决结论，但版本号过时降低了表格的参考价值。

**修复建议**: 更新 3 处版本号至当前值。

---

### P3-R5-04 — scheduler-orchestration-design 就绪检查点表名前缀不一致

**位置**: `scheduler-orchestration-design.md` §就绪检查点 lines 41, 44

**当前**:
```
| `trade_calendar` | 当日为交易日且日历可读 | 2 分钟 |
| `raw_daily`      | 覆盖当日目标股票池     | 10 分钟 |
| `raw_index_daily`| 指数数据可用           | 5 分钟  |
| `index_classify(SW2021)` | 31 行业映射齐全 | 5 分钟 |
```

**问题**: `raw_daily` / `raw_index_daily` 使用标准 `raw_` 前缀，但 `trade_calendar` / `index_classify` 缺少 `raw_` 前缀。DDL 权威定义为 `raw_trade_cal`（§2.8）/ `raw_index_classify`（§2.6）。同一表格内前缀不一致。

**修复建议**: 统一为 `raw_trade_cal` / `raw_index_classify`。

---

## 无问题确认（Clean Pass）

### eq-improvement-plan-core-frozen.md ✅
- §1 冻结边界权威源列表完整且路径正确
- §3 允许变更范围与禁止事项清晰
- §4 ENH-01~09 裁决与排期一致、`validation_weight_plan` 归属 CP-10 明确
- §5 S0-S6 路线的 CP 切片/外挂增强/建议命令/必要产物结构统一
- §6 每圈闭环模板 5 项与 6A-WORKFLOW 一致
- §7 Strict 6A 升级条件完整
- §8 入口文档同步规则合理（仅路径/流程/状态变更时同步）

### enhancement-selection-analysis ✅（除 R5-02/03）
- §1 审查框架三维度清晰
- §2 ENH-01~08 裁决逻辑（核心链路依赖/不做后果/投入产出）充分
- §3 ENH-09 新增论证完整、Validation 桥接归属 CP-10 明确
- §6 明确排除清单合理（AKShare/vn.py/RQAlpha/backtrader/CP 脚本/Adapter 文档）
- §7 三份方案差异处理结论清晰

### scheduler-orchestration-design ✅（除 R5-04）
- DAG 链路完整：Data → MSS/IRS/PAS → Validation Gate → Integration → Trading → Analysis/GUI
- 执行约束 4 条正确（Gate FAIL 阻断 Integration）
- 调度窗口细化到 Validation 时段（R25 已修复）
- 重试策略（指数退避 5 次、幂等键）完整

### monitoring-alerting-design ✅
- 监控范围覆盖全 6 层（含 Validation）
- Validation Gate FAIL 定义为 P0 阻断 ✅
- `stale_days > 3` 告警 ✅
- `final_score` 越界定义为 P0 ✅
- 告警升级路径（P1×3→P0, P2×10→P1）合理
- 重试参数与调度器对齐（max_delay=480s）

---

## 统计

| 优先级 | 数量 | 编号 |
|--------|------|------|
| P2 | 2 | R5-01, R5-02 |
| P3 | 2 | R5-03, R5-04 |
| **合计** | **4** | |

**累计（R1-R5）**: 6 + 6 + 6 + 1 + 4 = **23 项**（R1-R4 全部已修复）

---

## 下一轮预告

R6 将检查剩余域：`docs/reference/`（README + astock-rules + tushare/ 3 文件 + workflows/）、`.claude/`、根目录配置文件（CLAUDE.md / AGENTS.md / WARP.md / README.md / README.en.md）。预估 10-12 个文件。若文件量过大将分两轮。
