# 增量功能设计池（Feature Designs）

## 1. 目的

- 冻结基线设计不直接改动：`docs/design/**`、`docs/system-overview.md`、`docs/module-index.md`、`docs/naming-conventions.md`。
- 后续新增/增强功能，先在本目录沉淀为“增量设计”，再进入 Spiral 实施。

## 2. 使用规则

- 新增功能设计文件统一放在：`Governance/specs/feature-designs/`。
- 建议命名：`FD-YYYYMMDD-<topic>.md`。
- 每份增量设计至少包含：背景、目标、影响模块、接口/数据变更、验收标准、回滚方案。
- 如来源于批判报告或外部评审，必须记录来源路径（例如 `.reports/...`）。

## 3. 生命周期

- Draft: 设计草案，待评审
- Accepted: 评审通过，纳入某个 Spiral
- Delivered: 已实现并在对应 Spiral `final.md` 收口

## 4. 首批种子材料

- 存放目录：`Governance/specs/feature-designs/seed-20260209-from-reports/`
- 来源：`.reports/` 中 2026-02-09 的批判与行动计划文档（复制留存，原件不动）
