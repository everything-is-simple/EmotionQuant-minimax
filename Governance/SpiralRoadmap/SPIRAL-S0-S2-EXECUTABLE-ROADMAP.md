# EmotionQuant S0-S2 真螺旋执行路线图（执行草案 v0.1）

**状态**: Candidate  
**生效日期**: 2026-02-13  
**适用范围**: S0-S2（数据层到双算法推荐最小闭环）  
**目标**: 把“阶段列表”改为“可收口螺旋序列”，每圈都有可运行闭环与消费证据

---

## 1. 执行定义（什么叫一圈完成）

每个 Spiral 完成必须同时满足 6 项，不允许“部分完成”：

1. `run`：核心命令可运行，返回码 0
2. `test`：自动化测试通过
3. `artifact`：产物可定位且可追溯
4. `gate`：门禁表达式判定通过
5. `consumption`：明确“谁消费、怎么消费、消费结论”
6. `review/sync`：复盘与最小 5 文件同步完成

统一证据目录：

`artifacts/{spiral_id}/{trade_date}/`

最小文件集合：

- `run.log`
- `test.log`
- `gate_report.md`
- `consumption.md`
- `review.md`
- `sync_checklist.md`

---

## 2. 螺旋约束（防止再次线性化）

每圈只允许：

- 1 个主目标
- 1-2 个能力切片
- 2-4 天工时预算
- 最多 3 个实现任务

禁止事项：

- 跨圈偷跑（上圈门禁未过直接进下圈）
- 同圈同时引入新功能和新口径
- 没有消费证据就宣布完成

---

## 3. S0-S2 重排总览

| Spiral | 预算 | 主目标 | 产出给谁 |
|---|---:|---|---|
| S0a | 2d | CLI 与 Config 可运行 | S0b |
| S0b | 3d | TuShare 拉取 + L1 入库闭环 | S0c |
| S0c | 3d | L2 快照 + 失败链路闭环 | S1a |
| S1a | 3d | MSS 最小评分引擎 | S1b |
| S1b | 2d | MSS 消费验证（小回溯） | S2a |
| S2a | 4d | IRS + Validation 最小版 | S2b |
| S2b | 3d | MSS+IRS 集成推荐闭环 | S3 或 S2r |
| S2r | 1-2d | 质量门修复子圈（条件触发） | S2b |

---

## 4. 各圈执行合同（写死）

### S0a

- 主目标：统一入口与环境注入可用
- In Scope：`eq` 入口、`Config.from_env()`、`--help` 与参数解析
- run：`eq run --date 20260213 --source mock --dry-run`
- test：`pytest tests/contracts/test_cli_entry.py tests/unit/config/test_config_defaults.py -q`
- gate：
  - `eq --help` 成功输出子命令清单
  - 所有路径来自环境变量或 `Config.from_env()`
- artifact：
  - `cli_contract.md`
  - `config_effective_values.json`
- consumption：
  - S0b 在 `consumption.md` 记录“已使用统一入口触发数据采集”

### S0b

- 主目标：L1 原始数据采集入库闭环
- In Scope：`TuShareFetcher`、限流重试、`raw_*` 最小表落地
- run：`eq run --date 20260213 --source tushare --l1-only`
- test：`pytest tests/contracts/test_data_fetcher_contract.py tests/contracts/test_l1_repository_contract.py -q`
- gate：
  - `raw_daily` 当日记录数 `> 0`
  - `raw_trade_cal` 包含 `trade_date=20260213`
  - 失败时输出 `error_manifest.json`
- artifact：
  - `raw_counts.json`
  - `fetch_retry_report.md`
  - `error_manifest_sample.json`
- consumption：
  - S0c 在 `consumption.md` 记录“从 L1 读取并生成 L2”

### S0c

- 主目标：L2 快照与失败链路闭环
- In Scope：`market_snapshot`、`industry_snapshot`、错误分级与兜底
- run：`eq run --date 20260213 --source tushare --to-l2`
- test：`pytest tests/contracts/test_snapshot_contract.py tests/canary/test_s0_canary.py -q`
- gate：
  - `market_snapshot` 有当日记录
  - 字段包含 `data_quality/stale_days/source_trade_date`
  - 失败流程包含 `error_level`
- artifact：
  - `market_snapshot_sample.parquet`
  - `industry_snapshot_sample.parquet`
  - `s0_canary_report.md`
- consumption：
  - S1a 在 `consumption.md` 记录“以 L2 作为 MSS 输入”

### S1a

- 主目标：MSS 最小评分可跑
- In Scope：六因子最小版、标准化、`mss_panorama` 输出
- run：`eq mss --date 20260213`
- test：`pytest tests/contracts/test_mss_contract.py tests/unit/algorithms/test_mss_* -q`
- gate：
  - `mss_panorama` 当日记录数 `> 0`
  - 字段包含 `mss_score/mss_temperature/mss_cycle`
- artifact：
  - `mss_panorama_sample.parquet`
  - `mss_factor_trace.md`
- consumption：
  - S1b 在 `consumption.md` 记录“读取 MSS 结果并执行小回溯”

### S1b

- 主目标：MSS 消费验证（不是只算分）
- In Scope：MSS-only 小回溯、消费结论
- run：`eq mss-probe --start 20251101 --end 20260213`
- test：`pytest tests/contracts/test_mss_probe_contract.py tests/contracts/test_mss_integration_contract.py -q`
- gate：
  - 产出 `mss_only_probe_report`
  - 报告包含 `top_bottom_spread_5d` 与结论
- artifact：
  - `mss_only_probe_report.md`
  - `mss_consumption_case.md`
- consumption：
  - S2a 在 `consumption.md` 记录“使用 S1b 结论作为 IRS 叠加前置依据”

### S2a

- 主目标：IRS + Validation 最小闭环
- In Scope：IRS 六因子最小版、`validation_gate_decision`（PASS/WARN/FAIL）
- run：`eq recommend --date 20260213 --mode mss_irs --with-validation`
- test：`pytest tests/contracts/test_irs_contract.py tests/contracts/test_validation_gate_contract.py -q`
- gate：
  - `irs_industry_daily` 当日记录数 `> 0`
  - `validation_gate_decision` 有当日判定
  - FAIL 时必须产出 `validation_prescription`
- artifact：
  - `irs_industry_daily_sample.parquet`
  - `validation_gate_decision_sample.parquet`
  - `validation_prescription_sample.md`
- consumption：
  - S2b 在 `consumption.md` 记录“使用 gate 决策驱动集成输出”

### S2b

- 主目标：MSS+IRS 集成推荐闭环
- In Scope：`integrated_recommendation`、质量门、进入 S3 的 Go/No-Go
- run：`eq recommend --date 20260213 --mode integrated`
- test：`pytest tests/contracts/test_integration_contract.py tests/contracts/test_quality_gate_contract.py -q`
- gate：
  - `integrated_recommendation` 当日记录数 `> 0`
  - `quality_gate_report.status in (PASS, WARN)`
  - 若 `FAIL`，禁止进 S3，强制进入 S2r
- artifact：
  - `integrated_recommendation_sample.parquet`
  - `quality_gate_report.md`
  - `s2_go_nogo_decision.md`
- consumption：
  - S3 在 `consumption.md` 记录“回测消费推荐结果的结论”

### S2r（条件触发）

- 触发条件：S2b 质量门 `FAIL`
- 主目标：只修不扩，恢复到可通过质量门
- In Scope：仅允许调整 IRS/Validation/Integration 参数与口径，不改 S0/S1 契约
- run：`eq recommend --date 20260213 --mode integrated --repair s2r`
- test：`pytest tests/contracts/test_validation_gate_contract.py tests/contracts/test_quality_gate_contract.py -q`
- gate：
  - `quality_gate_report.status = PASS or WARN`
  - 必须有 `s2r_patch_note` 和 `s2r_delta_report`
- artifact：
  - `s2r_patch_note.md`
  - `s2r_delta_report.md`
- consumption：
  - 返回 S2b 重验，并记录“修复前后差异与结论”

---

## 5. 状态推进规则（硬门禁）

状态只允许：`planned -> in_progress -> completed` 或 `blocked`。

推进规则：

1. 任一圈未满足 6 项收口条件，不得标记 `completed`
2. `completed` 前必须写完 `consumption.md`
3. `blocked` 超过 1 天必须出降级策略，不允许静默等待
4. S2b `FAIL` 不得进入 S3，只能进 S2r

---

## 6. 首圈启动（直接执行）

启动顺序：

1. 先跑 S0a，只验入口和配置，不碰业务算法
2. S0a 过门禁后才开 S0b
3. 任何圈失败都先补 `error_manifest` 与 `review.md`，再重跑

S0a 启动命令：

```bash
eq run --date 20260213 --source mock --dry-run
pytest tests/contracts/test_cli_entry.py tests/unit/config/test_config_defaults.py -q
```

---

## 7. 最小同步（每圈固定）

每圈完成后同步：

1. `Governance/specs/spiral-{spiral_id}/final.md`
2. `Governance/record/development-status.md`
3. `Governance/record/debts.md`
4. `Governance/record/reusable-assets.md`
5. `Governance/Capability/SPIRAL-CP-OVERVIEW.md`

---

## 8. 结论

这份草案的核心不是“写更多文档”，而是把 S0-S2 改为可收口的短周期螺旋。  
判断标准只有一个：每圈是否留下可运行、可验证、可消费的证据链。
