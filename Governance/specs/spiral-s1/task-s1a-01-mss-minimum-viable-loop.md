# Task 卡：S1a-01 MSS 最小可用闭环（可直接执行命令版）

**版本**: v1.0  
**创建日期**: 2026-02-13  
**状态**: Draft（待执行）  
**适用链路**: `S0 -> S1a -> S1b -> S2a -> S2b -> S3a -> S3b -> S4`

---

## 1. 基本信息

- Spiral: `S1a`
- Task ID: `S1a-01`
- Owner: `solo-dev`
- 预计工时: `1d`
- 关联能力: `CP-01 + CP-02`
- 风险等级: `Medium`
- trade_date: `20260207`
- 证据目录: `artifacts/spiral-s1a/20260207/`

---

## 2. 目标（Scope）

在 `trade_date=20260207` 下，形成 MSS 最小可用能力并输出可消费证据：`mss_panorama` + `mss_micro_probe_report`。

---

## 3. 范围（Scope）

- In Scope:
  - MSS 六因子最小实现（允许先以可回归口径实现）
  - 温度/周期/趋势输出与 `unknown` 兜底
  - `mss_panorama` 产物写出（至少文件级产物可检查）
  - `mss_micro_probe_report` 消费结论产物
  - 最小自动化测试（至少 1 组）
- Out Scope:
  - MSS-only 集成小回测（属于 `S1b`）
  - IRS/PAS/Validation/Integration（属于后续子圈）
  - GUI/Trading/Backtest 业务实现

---

## 4. 输入契约（Scope）

| 输入 | 来源 | 就绪条件 | 失败处理 |
|---|---|---|---|
| `market_snapshot` | S0 产物 | `trade_date=20260207` 可读取 | P0 阻断，回到 S0 |
| A 股规则口径 | `Governance/steering/系统铁律.md` | T+1/涨跌停等规则已冻结 | P0 阻断，禁止改语义 |
| 命名口径 | `docs/naming-conventions.md` | `snake_case` + 统一枚举命名 | P1 修正后继续 |

---

## 5. 输出契约（Build）

- 代码文件（目标）:
  - `src/algorithms/mss/factors.py`
  - `src/algorithms/mss/engine.py`
  - `src/algorithms/mss/io.py`
- 测试文件（目标）:
  - `tests/unit/algorithms/test_mss_factors.py`
  - `tests/unit/algorithms/test_mss_engine.py`
  - `tests/unit/algorithms/test_mss_probe.py`
- 产物文件（必须）:
  - `artifacts/spiral-s1a/20260207/mss_panorama.parquet`
  - `artifacts/spiral-s1a/20260207/mss_micro_probe_report.md`
  - `artifacts/spiral-s1a/20260207/run.log`
  - `artifacts/spiral-s1a/20260207/test.log`
  - `artifacts/spiral-s1a/20260207/consumption.md`

---

## 6. 执行命令（Verify，可直接复制）

说明：
- 第 A 组是“当前仓库基线可执行命令”（现在即可跑）。
- 第 B 组是“S1a 完成后验收命令”（本任务完成时必须跑通）。

```bash
# A. 当前仓库基线可执行（立即可跑）
conda run -n base python -m scripts.quality.local_quality_check --session --scan
conda run -n base pytest tests/unit/config/test_config_defaults.py tests/unit/scripts/test_local_quality_check.py -q

# B. S1a 完成后验收（本任务收口必须跑通）
conda run -n base python -m src.algorithms.mss.run --trade-date 20260207 --output artifacts/spiral-s1a/20260207/mss_panorama.parquet
conda run -n base python -m src.algorithms.mss.probe --trade-date 20260207 --window 20 --output artifacts/spiral-s1a/20260207/mss_micro_probe_report.md
conda run -n base pytest tests/unit/algorithms/test_mss_factors.py tests/unit/algorithms/test_mss_engine.py tests/unit/algorithms/test_mss_probe.py -q
```

---

## 7. 验收门禁（Verify）

- [ ] A 组基线命令已执行并留痕
- [ ] B 组验收命令全部成功
- [ ] `mss_panorama` 产物存在且字段可检查
- [ ] `mss_micro_probe_report` 存在并包含消费结论
- [ ] `temperature` 范围满足 `[0,100]`
- [ ] `cycle` 仅允许：`emergence/fermentation/acceleration/divergence/climax/diffusion/recession/unknown`
- [ ] `trend` 仅允许：`up/down/sideways`
- [ ] 关键日志无未处理 P0/P1 错误

---

## 8. 消费证据（本圈硬门禁）

文件：`artifacts/spiral-s1a/20260207/consumption.md`

最小模板（执行时填写）：

```markdown
# S1a 消费结论（20260207）

- 消费方：S1b（MSS-only 集成小回测）
- 消费对象：mss_panorama + mss_micro_probe_report
- 消费方式：读取当日 mss 输出并执行小样本探测
- 消费结论：是否允许进入 S1b（是/否）
- 若否，阻断原因与修复动作：
```

---

## 9. 同步清单（Sync）

- [ ] `Governance/specs/spiral-s1/review.md` 更新 S1a 执行结果
- [ ] `Governance/specs/spiral-s1/final.md` 更新阶段结论
- [ ] `Governance/record/development-status.md` 登记进度
- [ ] `Governance/record/debts.md` 登记新增债务（如有）
- [ ] `Governance/record/reusable-assets.md` 登记可复用资产（如有）

---

## 10. 风险与回滚

- 风险：
  - MSS 六因子数据口径与现有数据模型暂不完全对齐
  - CLI 入口尚未实现导致验收命令不可执行
- 回滚方式：
  - 保留 `S0` 基线，仅回滚 `src/algorithms/mss/*` 与本任务新增测试
  - 将失败原因与回滚点写入 `review.md`
- 新增债务（预期）：
  - 若先以最小实现收口，需在 `S1b` 清偿命令统一与产物 schema 固化

---

## 11. 完成定义（DoD）

1. MSS 最小闭环可运行（run/test/artifact 完整）。
2. `mss_panorama` 与 `mss_micro_probe_report` 可被 `S1b` 消费。
3. 证据目录 `artifacts/spiral-s1a/20260207/` 五件最小证据齐全。
4. 同步清单完成，且结论可追溯。
