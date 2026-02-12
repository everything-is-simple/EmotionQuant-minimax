# EmotionQuant 文档检查报告 — R6

**检查工具**: Warp (claude 4.6 opus max)
**检查时间**: 2026-02-11
**检查范围**: `docs/reference/` 全部文件（11 个）
**累计轮次**: R1–R6（R1–R5 已修复 23 项）

---

## 检查范围

| # | 文件 | 结果 |
|---|------|------|
| 1 | docs/reference/README.md | ⚠️ 见 P2-R6-01 |
| 2 | docs/reference/astock-rules-handbook.md | ⚠️ 见 P2-R6-01 |
| 3 | docs/reference/tushare/tushare-config.md | ⚠️ 见 P2-R6-01, P3-R6-02 |
| 4 | docs/reference/tushare/tushare-config-10000.md | ✅ Clean Pass |
| 5 | docs/reference/tushare/tushare-config-5000-官方.md | ⚠️ 见 P2-R6-01, P3-R6-03 |
| 6 | docs/reference/tushare/tushare-topic-index.md | ✅ Clean Pass |
| 7 | docs/reference/A股市场/A股市场交易规则-tushare版.md | ✅ Clean Pass |
| 8 | docs/reference/A股市场/A股涨跌停板制度-tushare版.md | ✅ Clean Pass |
| 9 | docs/reference/A股市场/A股申万行业指数-tushare版.md | ✅ Clean Pass |
| 10 | docs/reference/workflows/6A工作流.md | ✅ Clean Pass |
| 11 | docs/reference/workflows/RIPER-5_With_Conditional_Review_Gate_CN.md | ✅ Clean Pass |

---

## 问题清单

### P2-R6-01 | 4 个文件 × 9 处断链：`design/data-layer/` / `design/backtest/` 缺少 `core-infrastructure/` 层级

**根因**: `data-layer/` 和 `backtest/` 位于 `docs/design/core-infrastructure/` 下，不直接在 `docs/design/` 下。`docs/design/data-layer/` 和 `docs/design/backtest/` 不存在。

**涉及文件与行号**:

1. **README.md**
   - L265: `../design/data-layer/` → `../design/core-infrastructure/data-layer/`
   - L266: `../design/backtest/` → `../design/core-infrastructure/backtest/`

2. **astock-rules-handbook.md**
   - L265: `../design/data-layer/` → `../design/core-infrastructure/data-layer/`
   - L266: `../design/backtest/` → `../design/core-infrastructure/backtest/`

3. **tushare/tushare-config.md**
   - L244: `../../design/data-layer/data-layer-data-models.md` → `../../design/core-infrastructure/data-layer/data-layer-data-models.md`
   - L246: `../../design/data-layer/data-layer-api.md` → `../../design/core-infrastructure/data-layer/data-layer-api.md`

4. **tushare/tushare-config-5000-官方.md**
   - L92: 链接 href `../../design/data-layer/data-layer-api.md` → `../../design/core-infrastructure/data-layer/data-layer-api.md`（注：显示文本已含正确路径，仅 href 错误）
   - L407: `../../design/data-layer/data-layer-data-models.md` → `../../design/core-infrastructure/data-layer/data-layer-data-models.md`
   - L409: `../../design/data-layer/data-layer-api.md` → `../../design/core-infrastructure/data-layer/data-layer-api.md`

**处理方案**: 全部补上 `core-infrastructure/` 路径层级。

---

### P3-R6-02 | tushare-config.md L301–302: 页脚版本/日期与页眉不一致

- 页眉（L3–4）: `v3.1` / `2026-02-04`
- 页脚（L301–302）: `v3.0` / `2026-01-09`

**处理方案**: 页脚改为 `v3.1` / `2026-02-04`，与页眉对齐。

---

### P3-R6-03 | tushare-config-5000-官方.md: 章节编号错误（3 处）

1. **L164,191**: `### 3.1` / `### 3.2` 位于 `## 四、系统集成` 下 → 应为 `### 4.1` / `### 4.2`
2. **L217**: `## 四、频率控制与限制` — "四"重复（L162 已用）→ 应为 `## 五、频率控制与限制`，后续 五→六, 六→七, 七→八, 八→九 依次递增
3. **L370,377**: `### 7.3 Token泄露应急处理` 排在 `### 7.2 最佳实践` 之前 → 调换顺序（或重新编号）

**处理方案**: 统一修正章节编号；调整 7.2/7.3 顺序。注意：修正"四"重复后，后续所有中文编号均需 +1。

---

## 统计

| 等级 | 本轮 | 累计 (R1–R6) |
|------|------|--------------|
| P1 | 0 | 1 |
| P2 | 1（9 处断链） | 10 |
| P3 | 2 | 14 |
| **合计** | **3 项** | **26 项** |

---

## Clean Pass 确认（7 / 11 文件）

- docs/reference/tushare/tushare-config-10000.md ✅
- docs/reference/tushare/tushare-topic-index.md ✅
- docs/reference/A股市场/A股市场交易规则-tushare版.md ✅
- docs/reference/A股市场/A股涨跌停板制度-tushare版.md ✅
- docs/reference/A股市场/A股申万行业指数-tushare版.md ✅
- docs/reference/workflows/6A工作流.md ✅
- docs/reference/workflows/RIPER-5_With_Conditional_Review_Gate_CN.md ✅

---

## 下一轮预告

**R7** 预计范围: `.claude/` 目录（README, INTEGRATION, agents/kfc/ 7 个 spec, commands/ 7 个命令）及根配置文件（CLAUDE.md, AGENTS.md, WARP.md, README.md, README.en.md）
