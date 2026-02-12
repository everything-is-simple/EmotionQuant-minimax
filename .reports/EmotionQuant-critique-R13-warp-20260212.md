# EmotionQuant 文档检查报告 — R13

**检查工具**: Warp (claude 4.6 opus max)
**检查时间**: 2026-02-12
**检查范围**: `docs/design/core-infrastructure/backtest/`（6 文件）
**累计轮次**: R1–R13（R1–R12 已修复 47 项）

---

## 检查范围

| # | 文件 | 结果 |
|---|------|------|
| 1 | backtest-algorithm.md | ⚠️ 见 P2-R13-02 |
| 2 | backtest-api.md | ✅ Clean Pass |
| 3 | backtest-data-models.md | ⚠️ 见 P2-R13-01, P2-R13-03 |
| 4 | backtest-engine-selection.md | ✅ Clean Pass |
| 5 | backtest-information-flow.md | ⚠️ 见 P3-R13-04 |
| 6 | backtest-test-cases.md | ✅ Clean Pass |

---

## 问题清单

### P2-R13-01 | data-models §1.4：Position dataclass 字段顺序违反 Python 规则

**位置**: backtest-data-models.md §1.4 L146-165

**现状**:
```python
@dataclass
class Position:
    stock_code: str
    stock_name: str
    industry_code: str
    direction: str = "long"   # ← 有默认值
    shares: int               # ← 无默认值 → TypeError
    cost_price: float
    ...
```

**问题**: Python dataclass 要求所有无默认值的字段必须排在有默认值字段之前。`direction: str = "long"` 之后出现 `shares: int`（无默认值），定义时会抛出 `TypeError: non-default argument 'shares' follows default argument`。

**修复方案**: 将 `direction` 移到所有无默认值字段之后，或改为 `direction: str = field(default="long")`，并将 `shares` 等字段移到 `direction` 之前：

```python
@dataclass
class Position:
    stock_code: str
    stock_name: str
    industry_code: str
    shares: int
    cost_price: float
    cost_amount: float
    market_price: float
    market_value: float
    unrealized_pnl: float
    unrealized_pnl_pct: float
    buy_date: str
    can_sell_date: str
    is_frozen: bool
    stop_price: float
    target_price: float
    signal_id: str
    direction: str = "long"   # 默认值字段放最后
```

---

### P2-R13-02 | algorithm §3.1 Step 2：recommendation 过滤未使用 min_recommendation 配置

**位置**: backtest-algorithm.md §3.1 L91-102, §3.2 L154-162

**现状**:
```python
# Step 2 硬编码：
if row.recommendation in {"AVOID", "SELL"}:
    continue
```

**BacktestConfig** 定义 (data-models §1.1 L43):
```python
min_recommendation: str = "BUY"
# 注释: 仅纳入该等级及以上；STRONG_BUY > BUY > HOLD > SELL > AVOID
```

**api.md §1.3** SignalProvider 声明:
```
应用 min_final_score / min_recommendation
```

**问题**: 算法伪代码硬编码排除 `{"AVOID", "SELL"}`，意味着 HOLD 信号会通过过滤。但 `min_recommendation="BUY"` 的语义是"仅纳入 BUY 及以上"，应同时排除 HOLD。api.md 声明 SignalProvider 会应用 `min_recommendation`，但算法伪代码未体现。

**修复方案**: 将 Step 2 硬编码过滤替换为基于 `config.min_recommendation` 的等级比较：
```python
RECOMMENDATION_RANK = {"STRONG_BUY": 0, "BUY": 1, "HOLD": 2, "SELL": 3, "AVOID": 4}
if RECOMMENDATION_RANK.get(row.recommendation, 99) > RECOMMENDATION_RANK[config.min_recommendation]:
    continue
```
同理修改 §3.2 BU 流程中的对应过滤。

---

### P2-R13-03 | data-models §4.1：L3 依赖表缺少 validation_gate_decision

**位置**: backtest-data-models.md §4.1 L427-432

**现状**: L3 依赖表仅列出：
- `integrated_recommendation`（直接读取）
- `pas_breadth_daily`（直接读取，BU）
- `mss_panorama`（间接透传）
- `irs_industry_daily`（间接透传）

**但 algorithm §3.1/§3.2 Step 0 直接查询**：
```python
gate = get_validation_gate_decision(signal_date)
if gate.final_gate == "FAIL":
    return []
```

**问题**: `validation_gate_decision` 是算法 Step 0 的前置阻断依赖，但未列入 §4.1 依赖表。

**修复方案**: 在 §4.1 表格增加一行：
`validation_gate_decision | 直接读取 | Step 0 前置门控 | final_gate`

---

### P3-R13-04 | info-flow §4 异常处理表缺少 Validation Gate FAIL 场景

**位置**: backtest-information-flow.md §4 L116-121

**现状**: 异常处理表仅 3 行（集成信号缺失 / BU 活跃度不足 / L1 行情缺失）。

**algorithm §6** 明确列出 4 种降级策略，其中包含 `Validation Gate = FAIL → 跳过当日信号生成并记录 blocked_by_gate`。

**问题**: info-flow §4 缺少 Gate=FAIL 场景，与 algorithm §6 不对齐。

**修复方案**: 在 §4 表格首行增加：
`Validation Gate = FAIL | 跳过当日信号生成，记录 blocked_by_gate`

---

## 统计

| 等级 | 本轮 | 累计 (R1–R13) |
|------|------|---------------|
| P1 | 0 | 1 |
| P2 | 3 | 26 |
| P3 | 1 | 24 |
| **合计** | **4 项** | **51 项** |

---

## Clean Pass 确认（3 / 6 文件）

- **backtest-api.md** ✅ — BacktestRunner/Engine/SignalProvider 接口与 data-models 类型一致；BacktestConfig 字段/默认值与 data-models §1.1 完全匹配；3.1/3.2 用法示例正确
- **backtest-engine-selection.md** ✅ — 选型决策 Qlib 主选 + 本地向量化基线 + backtrader 兼容，与 algorithm §1.3 / api §1.2 / data-models §3.6 三方一致；错误处理分级合理
- **backtest-test-cases.md** ✅ — 10 类 23 条用例覆盖 T+1、涨跌停、滑点、费用、仓位、出场、降级、绩效、模式、门禁，与 algorithm §3-§5 规则对齐

---

## 交叉验证确认

- `backtest_trade_records` DDL 字段与 data-layer-data-models §7.4 完全一致 ✓
- `backtest_results` 由 data-layer §7.5 正确引用到 backtest-data-models §2.3 ✓
- `BacktestSignal` 17 字段与 `integrated_recommendation` DDL 输出字段一一对应 ✓
- `AShareFeeConfig` 4 字段 (commission/stamp/transfer/min) 与 algorithm §4.4 费用模型口径一致 ✓
- Sharpe/Sortino/Calmar 公式与 `risk_free_rate=0.015` 参数口径在 algorithm/api/data-models 三文档一致 ✓
- 信号-执行日拆分 (signal_date=T, execute_date=T+1) 在全 6 文件中表述一致 ✓

---

## 下一轮预告

**R14** 预计范围: `docs/design/core-infrastructure/trading/`（4 文件）

---

## 剩余轮次预估

| 轮次 | 范围 | 文件数 |
|------|------|--------|
| R14 | `trading/` | 4 |
| R15 | `gui/` | 4 |
| R16 | `analysis/` | 4 |
| R17 | `enhancements/drafts/` (中文草稿) | ~4 |
| R18+ | 全局交叉复检 / 收尾 | TBD |

预计还需 **4–6 轮**（R14–R17 各模块 + 可能 1–2 轮收尾），视每轮发现问题数量而定。
