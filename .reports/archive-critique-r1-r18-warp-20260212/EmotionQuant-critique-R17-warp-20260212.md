# EmotionQuant 文档逐轮审查 — 第 17 轮 (R17)

| 元信息 | 值 |
|---|---|
| 轮次 | R17 |
| 审查范围 | `docs/design/enhancements/` (9 files: 4 drafts + 5 top-level) |
| 审查日期 | 2026-02-12 |
| 累计问题 | 61 (P1:1 · P2:31 · P3:29) |
| 提交 | `8b0ac0c` |

---

## 1 文件清单

| # | 文件 | 性质 | 结果 |
|---|---|---|---|
| 1 | drafts/ × 4 | 历史草稿 | ✅ Skip |
| 2 | README.md | 目录索引 | ✅ Clean Pass |
| 3 | enhancement-selection-analysis…md | 外挂选型论证 | ✅ Clean Pass |
| 4 | eq-improvement-plan-core-frozen.md | 执行主计划 | ✅ Clean Pass |
| 5 | monitoring-alerting-design.md | 监控告警 | ⚠️ 1 issue |
| 6 | scheduler-orchestration-design.md | 调度编排 | ⚠️ 同源 |

---

## 2 发现问题

### P3-R17-01 · `stock_info` 应为 `stock_basic`（2 文件同源）
- **文件**: `monitoring-alerting-design.md` L14/L30; `scheduler-orchestration-design.md` L18
- **问题**: 使用 `stock_info` 而非数据层规范 `stock_basic`（对应 TuShare `stock_basic` 接口）
- **修复**: 3 处 `stock_info` → `stock_basic`
- **验证**: ✅ 全部已更新

---

## 3 Clean Pass 确认

- **README.md**: ENH-01~09 概览表与选型/主计划完全对齐；文件列表完整；边界说明清晰
- **enhancement-selection-analysis**: 9 项 ENH 逐项裁决完整；冻结边界声明全面；排除项理由充分
- **eq-improvement-plan-core-frozen**: S0–S6 路线与 SPIRAL-CP-OVERVIEW 对齐；闭环模板完整；升级 6A 条件明确
- **monitoring-alerting-design**: P0/P1/P2 告警级别清晰；重试参数与调度器一致
- **scheduler-orchestration-design**: DAG 含 Validation Gate 节点；A 股交易时间正确；就绪检查点完备
- **drafts/ × 4**: 核心冻结边界一致；Spiral 映射合理（草稿不做修复要求）

---

## 4 交叉验证

| 检查项 | 结果 |
|---|---|
| ENH-01~09 裁决 (README ↔ selection ↔ main-plan) | ✅ 一致 |
| 冻结边界 (3 份正式文档) | ✅ 一致 |
| 重试参数 (monitoring ↔ scheduler) | ✅ 一致 |
| Validation Gate 阻断 (scheduler ↔ monitoring) | ✅ 一致 |
| 表名 stock_basic (monitoring ↔ scheduler ↔ data-layer ↔ ENH-05) | ✅ 已统一 |

---

## 5 统计

| 优先级 | 本轮 | 累计 (R1–R17) |
|---|---|---|
| P1 | 0 | 1 |
| P2 | 0 | 31 |
| P3 | 1 | 29 |
| **合计** | **1** | **61** |

---

## 6 覆盖范围评估

R1–R17 已审查所有活跃文档目录：

| 目录 | 轮次 | 状态 |
|---|---|---|
| Governance/ + specs/ + reference/ + config/ + agents | R1–R8 | ✅ |
| core-algorithms/mss/ | R9 | ✅ |
| core-algorithms/irs/ | R9 | ✅ |
| core-algorithms/pas/ | R10 | ✅ |
| core-algorithms/integration/ | R10 | ✅ |
| core-algorithms/validation/ | R11 | ✅ |
| core-infrastructure/data-layer/ | R12 | ✅ |
| core-infrastructure/backtest/ | R13 | ✅ |
| core-infrastructure/trading/ | R14 | ✅ |
| core-infrastructure/gui/ | R15 | ✅ |
| core-infrastructure/analysis/ | R16 | ✅ |
| enhancements/ (drafts + top-level) | R17 | ✅ |

**全部目录已覆盖。建议进行 1 轮跨子系统交叉验证（R18）作为收尾。**
