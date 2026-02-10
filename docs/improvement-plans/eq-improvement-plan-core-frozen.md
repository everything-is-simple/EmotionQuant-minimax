# EmotionQuant 改进行动主计划（核心设计冻结）

## 0. 文档信息

- 状态: 当前唯一有效主计划
- 最后更新: 2026-02-10
- 适用: Spiral S0-S3
- 本地目录锚点:
  - Repo 根目录: `G:\EmotionQuant-gpt`
  - 数据目录: `G:\EmotionQuant_data`
  - 说明: 仅作为部署锚点，代码实现仍必须通过 Config/env 注入路径

## 1. 执行边界（不改核心）

以下内容一律冻结，不在本计划中修改:

1. `情绪优先` 主逻辑
2. `MSS/IRS/PAS + Validation` 核心语义与公式
3. `Spiral + CP` 主路线与闭环证据定义
4. R1-R31 已收口的核心设计结论（含命名、枚举、DDL 对齐、权重桥接）

## 2. 目标

在不改核心设计前提下，补齐工程落地护栏，避免实现阶段口径回退。

## 3. 允许改动范围（白名单）

- `tests/contracts/`
- `scripts/quality/`
- `src/adapters/`
- `src/pipeline/`（仅编排/错误产物，不改算法评分逻辑）
- `Governance/specs/spiral-s*/`
- `.reports/`（仅过程复盘）

## 4. 优化动作（OP）

### OP-01 核心设计冻结检查

- 交付: `scripts/quality/design_freeze_check.py`
- 验收: 核心权威文档关键锚点变化时自动失败并输出差异

### OP-02 命名与枚举契约测试

- 交付: `tests/contracts/test_naming_enums_contract.py`
- 验收: 覆盖 `MssCycle`/`Trend`/`RotationStatus`/`PasDirection`/推荐等级

### OP-03 Validation -> Integration 桥接回归

- 交付: `tests/contracts/test_validation_integration_bridge.py`
- 验收: 覆盖 `validation_gate_decision -> validation_weight_plan -> integrated_recommendation`

### OP-04 Backtest <-> Trading 一致性守卫

- 交付: `tests/contracts/test_backtest_trading_signal_parity.py`
- 验收: 同输入下两端信号过滤一致，偏差可追溯字段 diff

### OP-05 失败产物协议标准化

- 交付: `src/pipeline/error_manifest.py`（或等价模块）
- 验收: P0/P1/P2 都能产出结构化 `error_manifest.json`

### OP-06 S0 五件套模板固化

- 交付: `Governance/specs/spiral-s0/review.md`、`Governance/specs/spiral-s0/final.md` 实填样例
- 验收: `run/test/artifact/review/sync` 五件套可复核

## 5. 10 天切片

1. D1: OP-01 脚本骨架与最小报告输出
2. D2: OP-02 命名枚举契约测试
3. D3: OP-03 权重桥接回归测试
4. D4: OP-05 失败产物接入 pipeline
5. D5: OP-06 五件套演练
6. D6-D7: OP-04 一致性守卫首版
7. D8: 回归修复日
8. D9: 产物归档与治理同步
9. D10: 收口复盘与下一圈输入整理

## 6. DoD

全部满足才算完成:

1. 不改核心设计前提下，新增护栏测试通过
2. 至少 4 个契约测试可稳定运行
3. 至少 1 次失败演练可产出 `error_manifest.json`
4. 五件套证据齐全
5. `Governance/record/development-status.md`、`Governance/record/debts.md`、`Governance/record/reusable-assets.md` 已同步

## 7. 关联输入

- `docs/system-overview.md`
- `docs/module-index.md`
- `docs/naming-conventions.md`
- `Governance/ROADMAP/ROADMAP-OVERVIEW.md`
- `.reports/对标开源A股量化系统批判_20260209_150147.md`
- `.reports/对标开源A股量化系统批判_修订版_20260209.md`
- `.reports/对标批判响应行动计划_20260209_233929.md`
- `.reports/EmotionQuant_行动计划_采纳批判建议_20260209_2341.md`

## 8. 变更记录

| 日期 | 版本 | 变更 |
|---|---|---|
| 2026-02-10 | v1.0.0 | 首版主计划；建立 docs 统一入口，收敛分散计划 |

