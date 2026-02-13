# 数据采集增强方案：ENH-10 & ENH-11

**提案日期**: 2026-02-13  
**提案人**: 用户需求  
**状态**: Draft（待评审）  
**目标**: 在现有路线图基础上，增加全自动数据采集能力

---

## 1. 需求背景

用户希望系统具备：
1. 分批下载（不想一次性下载 13 年数据）
2. 断点续传（中途失败能继续）
3. 多线程加速（并行下载）
4. 定时自动下载（每日 16:00 自动执行）
5. 开机自动运行（开机即启动）

当前路线图缺口：
- ❌ 分批下载
- ❌ 断点续传
- ❌ 多线程下载
- ❌ 定时自动下载
- ❌ 开机自动运行

---

## 2. 增强方案总览

新增外挂包：

| 编号 | 名称 | 目标 | 实施圈 | 预估工时 |
|---|---|---|---|---|
| ENH-10 | 数据采集增强 | 分批+断点续传+多线程 | S6后 | 2.5d |
| ENH-11 | 定时调度器 | 每日自动下载+开机自启 | S6后 | 1.5d |

实施策略：推荐 S6 后实施（不阻塞主线）

---

## 3. ENH-10: 数据采集增强

### 3.1 功能定义

核心能力：
1. 将大时间范围拆分为多个批次（如按年度）
2. 记录每个批次的下载进度
3. 支持断点续传（跳过已完成批次）
4. 支持多线程并行下载（默认 3 线程）
5. 提供进度查询命令

### 3.2 命令接口

```bash
# 分批下载 2013-2026 数据（每批 1 年，3 线程）
eq fetch-batch --start 20130101 --end 20260213 --batch-size 365 --workers 3

# 断点续传（自动跳过已完成批次）
eq fetch-batch --start 20130101 --end 20260213 --resume

# 查看下载进度
eq fetch-status
# 输出示例：
# 总批次: 14
# 已完成: 11 (2013-2023)
# 进行中: 1 (2024, 65%)
# 待下载: 2 (2025-2026)
# 失败: 0

# 重试失败批次
eq fetch-retry
```

### 3.3 技术实现要点

#### 进度文件格式

```json
{
  "version": "1.0",
  "total_batches": 14,
  "batches": [
    {
      "batch_id": "2013",
      "start_date": "20130101",
      "end_date": "20131231",
      "status": "completed",
      "completed_at": "2026-02-13 10:23:45"
    },
    {
      "batch_id": "2024",
      "start_date": "20240101",
      "end_date": "20241231",
      "status": "running",
      "progress": 0.65
    },
    {
      "batch_id": "2025",
      "start_date": "20250101",
      "end_date": "20251231",
      "status": "pending"
    }
  ]
}
```

存储位置：`${CACHE_PATH}/fetch_progress.json`

#### 多线程策略

- 默认 3 线程（避免 TuShare 限流）
- 每个线程处理一个批次
- 线程间互不干扰
- 失败批次自动重试 3 次

#### 断点续传逻辑

1. 启动时读取 `fetch_progress.json`
2. 过滤出 `status != "completed"` 的批次
3. 优先处理 `status == "failed"` 的批次
4. 每完成一个批次，立即更新进度文件

### 3.4 文件结构

```
src/data/
├── fetcher.py                    # 基础采集器（S0）
├── fetcher_enhanced.py           # 增强采集器（ENH-10）
└── batch_progress.py             # 进度管理

tests/
└── test_fetcher_enhanced.py      # 增强采集器测试

scripts/
└── fetch_batch.py                # CLI 入口
```

### 3.5 任务分解

| 任务 | 产出 | 工时 |
|---|---|---|
| ENH10-T1: 批次生成与进度管理 | `batch_progress.py` | 0.5d |
| ENH10-T2: 增强采集器实现 | `fetcher_enhanced.py` | 1.0d |
| ENH10-T3: CLI 命令接入 | `eq fetch-batch/status/retry` | 0.5d |
| ENH10-T4: 测试与文档 | 测试用例 + 使用文档 | 0.5d |

### 3.6 验收门禁

- [ ] `eq fetch-batch --start 20130101 --end 20131231` 可执行
- [ ] 中途 Ctrl+C 后，`eq fetch-batch --resume` 能继续
- [ ] `eq fetch-status` 能显示正确进度
- [ ] 多线程下载速度 > 单线程 2 倍
- [ ] 至少 1 组自动化测试通过

---

## 4. ENH-11: 定时调度器

### 4.1 功能定义

核心能力：
1. 每日 16:00 自动下载最新数据
2. 自动判断是否交易日（非交易日跳过）
3. 检查是否已下载（避免重复）
4. 失败时发送通知（可选）
5. 开机自动启动调度器

### 4.2 命令接口

```bash
# 启动调度器（后台运行）
eq scheduler start

# 查看调度器状态
eq scheduler status
# 输出示例：
# 状态: 运行中
# PID: 12345
# 下次执行: 2026-02-14 16:00
# 最近执行: 2026-02-13 16:00 (成功)
# 最近 5 次执行记录:
#   2026-02-13 16:00 - 成功 (耗时 2m 15s)
#   2026-02-12 16:00 - 成功 (耗时 1m 58s)
#   2026-02-11 16:00 - 跳过 (非交易日)

# 停止调度器
eq scheduler stop

# 手动触发一次（测试用）
eq scheduler run-once
```

### 4.3 技术实现方案

#### 方案 1：Windows 任务计划程序（推荐）

优点：
- 系统原生支持
- 开机自启简单
- 不占用 Python 进程

实现：
```powershell
# 创建任务（安装时执行一次）
eq scheduler install

# 内部调用 Windows API
$action = New-ScheduledTaskAction -Execute "eq" -Argument "run --date today --source tushare"
$trigger = New-ScheduledTaskTrigger -Daily -At 16:00
$settings = New-ScheduledTaskSettingsSet -StartWhenAvailable
Register-ScheduledTask -TaskName "EmotionQuant-DailyFetch" -Action $action -Trigger $trigger -Settings $settings
```

#### 方案 2：Python 内置调度器（备选）

优点：
- 跨平台
- 灵活控制

缺点：
- 需要常驻进程
- 开机自启需要额外配置

实现：
```python
# src/scheduler/daily_scheduler.py
import schedule
import time

class DailyScheduler:
    def start(self):
        schedule.every().day.at("16:00").do(self._daily_fetch)
        
        while True:
            schedule.run_pending()
            time.sleep(60)
    
    def _daily_fetch(self):
        # 1. 检查交易日
        if not self._is_trading_day():
            return
        
        # 2. 检查已下载
        if self._is_already_fetched(today()):
            return
        
        # 3. 执行下载
        self.fetcher.fetch(date=today())
```

### 4.4 开机自启配置

#### Windows

```powershell
# 方案 1：任务计划程序（推荐）
# 在任务属性中勾选"开机时启动"

# 方案 2：启动文件夹
# 创建快捷方式到启动文件夹
$startup = "$env:APPDATA\Microsoft\Windows\Start Menu\Programs\Startup"
Copy-Item "eq-scheduler.lnk" $startup
```

### 4.5 任务分解

| 任务 | 产出 | 工时 |
|---|---|---|
| ENH11-T1: 调度器核心实现 | `daily_scheduler.py` | 0.5d |
| ENH11-T2: Windows 任务计划集成 | `eq scheduler install/uninstall` | 0.5d |
| ENH11-T3: 交易日判断与去重 | 交易日历查询 + 已下载检查 | 0.3d |
| ENH11-T4: 测试与文档 | 测试用例 + 使用文档 | 0.2d |

### 4.6 验收门禁

- [ ] `eq scheduler install` 能创建 Windows 任务
- [ ] 任务在 16:00 自动触发
- [ ] 非交易日自动跳过
- [ ] 已下载数据不重复拉取
- [ ] `eq scheduler status` 能显示正确状态
- [ ] 开机后调度器自动运行

---

## 5. 路线图集成方案

### 5.1 加在哪里

#### 方案 A：S6 后独立实施（推荐）

```
S0 → S1 → S2 → S3 → S4 → S5 → S6 → ENH-10 → ENH-11
                                    (2.5d)   (1.5d)
```

**优点**：
- 不阻塞主线
- S0-S6 保持原有节奏
- 手动分批拉取历史数据完全够用

**缺点**：
- 需要等到 S6 完成（30.1d 后）

#### 方案 B：S0 扩展实施

```
S0 (4.5d → 7.0d) → S1 → S2 → S3 → S4 → S5 → S6 → ENH-11
                                                  (1.5d)
```

**优点**：
- 历史数据拉取更方便
- 一次性完成数据采集能力

**缺点**：
- S0 工期延长 55%（4.5d → 7.0d）
- 可能影响后续 Spiral 节奏

#### 方案 C：分阶段实施

```
S0 (4.5d) → S1 → ... → S3 (插入 ENH-10, 2.5d) → S4 → S5 → S6 → ENH-11 (1.5d)
```

**优点**：
- S3 前完成历史数据拉取（回测需要）
- 不影响 S0 节奏

**缺点**：
- 打断 S3 执行流程

### 5.2 怎么加

#### 5.2.1 文档修改清单

**需要修改的文档**：

1. **`docs/design/enhancements/eq-improvement-plan-core-frozen.md`**
   - 在 §4 外挂增强包表格中新增 ENH-10 和 ENH-11 两行
   - 位置：ENH-09 之后

2. **`Governance/SpiralRoadmap/VORTEX-EVOLUTION-ROADMAP.md`**
   - 在 §3.1 Spiral 进度看板中新增 ENH-10 和 ENH-11 两行
   - 在 §3.3 三分类工时分布中更新"系统外挂"总计
   - 在 §12 风险矩阵中新增相关风险

3. **`Governance/SpiralRoadmap/DEPENDENCY-MAP.md`**
   - 在 §5 ENH 外挂增强排布总览中新增 ENH-10 和 ENH-11
   - 在 §5.1 ENH 生命周期图中新增两行
   - 在 §5.2 外挂总工时中更新总计

4. **`Governance/Capability/SPIRAL-CP-OVERVIEW.md`**
   - 在变更记录中新增一条（记录 ENH-10/11 纳入）

#### 5.2.2 具体修改内容

**修改 1：`eq-improvement-plan-core-frozen.md` §4 表格**

在 ENH-09 之后新增：

```markdown
| ENH-10 | 数据采集增强 | 分批下载 + 断点续传 + 多线程 | 只做编排，不改采集语义 |
| ENH-11 | 定时调度器 | 每日自动下载 + 开机自启 | 只做调度，不改数据逻辑 |
```

**修改 2：`VORTEX-EVOLUTION-ROADMAP.md` §3.1 表格**

在 S6 之后新增：

```markdown
| ENH-10 | 🔄 采集增强 | 分批+断点续传+多线程 | - | 2.5d | 📋 未开始 |
| ENH-11 | ⏰ 自动调度 | 每日自动下载+开机自启 | - | 1.5d | 📋 未开始 |
| **合计** | | | | **34.1d** | |
```

**修改 3：`DEPENDENCY-MAP.md` §5 表格**

新增两行：

```markdown
| ENH-10 | 数据采集增强 | S6 后 | — | 高：历史数据拉取效率 |
| ENH-11 | 定时调度器 | S6 后 | — | 中：日常运营便利性 |
```

### 5.3 加什么

#### 5.3.1 新增文件清单

```
src/data/
├── fetcher_enhanced.py           # ENH-10 核心实现
└── batch_progress.py             # ENH-10 进度管理

src/scheduler/
├── __init__.py
├── daily_scheduler.py            # ENH-11 核心实现
└── windows_task.py               # ENH-11 Windows 集成

tests/
├── test_fetcher_enhanced.py      # ENH-10 测试
└── test_daily_scheduler.py       # ENH-11 测试

scripts/
├── fetch_batch.py                # ENH-10 CLI 入口
└── scheduler_cli.py              # ENH-11 CLI 入口

docs/
└── user-guide/
    ├── data-fetcher-advanced.md  # ENH-10 使用文档
    └── scheduler-setup.md        # ENH-11 使用文档
```

#### 5.3.2 新增命令清单

```bash
# ENH-10 命令
eq fetch-batch --start <date> --end <date> [--batch-size <days>] [--workers <n>] [--resume]
eq fetch-status
eq fetch-retry

# ENH-11 命令
eq scheduler install
eq scheduler uninstall
eq scheduler start
eq scheduler stop
eq scheduler status
eq scheduler run-once
```

---

## 6. 实施建议

### 6.1 推荐方案：S6 后实施

**理由**：
1. 不阻塞主线（S0-S6 是核心价值）
2. 手动分批拉取历史数据完全够用（只需执行一次）
3. ENH-10/11 是"锦上添花"，不是"雪中送炭"
4. S6 完成后系统已稳定，适合增加外挂功能

**时间线**：
```
Day 0-4.5:   S0 完成
Day 5-6.5:   手动分批拉取历史数据（2013-2026）
Day 7-30.1:  S1-S6 完成
Day 31-33.6: ENH-10 实施
Day 34-35.6: ENH-11 实施
```

**手动分批拉取脚本**（S0 完成后使用）：

```bash
# 按年度手动拉取（避免一次性超时）
for year in {2013..2026}; do
  eq run --start ${year}0101 --end ${year}1231 --source tushare
  echo "完成 $year 年数据"
  sleep 60  # 避免限流
done
```

### 6.2 激进方案：S0 扩展实施（不推荐）

**理由**：
- 仅当你认为"手动分批拉取"不可接受时才考虑
- 会延长 S0 工期 55%（4.5d → 7.0d）

**时间线**：
```
Day 0-7.0:   S0 + ENH-10 完成
Day 7-8.5:   自动分批拉取历史数据（2013-2026）
Day 9-32.6:  S1-S6 完成
Day 33-34.6: ENH-11 实施
```

### 6.3 折中方案：S3 前插入 ENH-10

**理由**：
- S3 回测需要历史数据
- 在 S3 前完成历史数据拉取更合理

**时间线**：
```
Day 0-4.5:   S0 完成
Day 5-6.5:   手动拉取部分数据（2024-2026）
Day 7-10.3:  S1 完成
Day 11-17.4: S2 完成
Day 18-20.9: ENH-10 实施
Day 21-22.4: 自动拉取全量历史数据（2013-2023）
Day 23-27.6: S3 完成
Day 28-35.1: S4-S6 完成
Day 36-37.6: ENH-11 实施
```

---

## 7. 风险评估

| 风险 | 概率 | 影响 | 缓解措施 |
|---|---|---|---|
| TuShare 限流导致多线程失效 | 中 | 中 | 限制最大线程数为 3，增加重试机制 |
| Windows 任务计划权限问题 | 低 | 中 | 提供管理员权限安装指南 |
| 断点续传文件损坏 | 低 | 低 | 每次写入前备份进度文件 |
| 定时任务未触发 | 中 | 低 | 提供手动触发命令 `eq scheduler run-once` |
| 开机自启失败 | 中 | 低 | 提供手动启动指南 |

---

## 8. 验收标准

### 8.1 ENH-10 验收

- [ ] 能分批下载 2013-2026 数据（每批 1 年）
- [ ] 中途 Ctrl+C 后能断点续传
- [ ] 多线程下载速度 > 单线程 2 倍
- [ ] `eq fetch-status` 能显示正确进度
- [ ] 失败批次能自动重试 3 次
- [ ] 至少 1 组自动化测试通过

### 8.2 ENH-11 验收

- [ ] `eq scheduler install` 能创建 Windows 任务
- [ ] 任务在 16:00 自动触发
- [ ] 非交易日自动跳过
- [ ] 已下载数据不重复拉取
- [ ] 开机后调度器自动运行
- [ ] `eq scheduler status` 能显示正确状态

---

## 9. 总结

### 9.1 核心结论

**可以做到**，但需要新增 ENH-10 和 ENH-11 两个外挂包。

### 9.2 推荐执行路径

```
1. 完成 S0 基础版（4.5d）
   ↓
2. 手动分批拉取历史数据（1-2d）
   ↓
3. 完成 S1-S6 主线（25.6d）
   ↓
4. 实施 ENH-10 高级下载（2.5d）
   ↓
5. 实施 ENH-11 定时调度（1.5d）
   ↓
6. 全自动数据采集系统完成（总计 35-36d）
```

### 9.3 关键决策点

**决策 1：何时实施 ENH-10/11？**
- 推荐：S6 后（不阻塞主线）
- 激进：S0 扩展（延长工期）
- 折中：S3 前插入 ENH-10（回测前完成历史数据）

**决策 2：是否必须实施？**
- ENH-10：非必须，手动分批拉取完全够用（只需执行一次）
- ENH-11：非必须，但能显著提升日常使用体验

**决策 3：实施优先级？**
- 高优先级：S0-S6 主线（核心价值）
- 中优先级：ENH-10（历史数据拉取便利性）
- 低优先级：ENH-11（日常运营便利性）

### 9.4 下一步行动

1. **评审本提案**：确认是否采纳 ENH-10/11
2. **选择实施方案**：S6 后 / S0 扩展 / S3 前插入
3. **更新路线图文档**：按照 §5.2 修改清单执行
4. **创建任务卡片**：为 ENH-10/11 创建详细任务分解

---

**提案人**: 用户需求  
**评审人**: 待定  
**决策人**: 待定  
**状态**: Draft（待评审）
