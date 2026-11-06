# Baseball Analytics

用 MLB 逐球数据（Statcast）做棒球量化分析的持续项目。每天提交一次微小进展，逐日累积成完整的分析体系。

## 项目简介

本项目的目标是用公开的逐球级数据回答一个具体问题：**在棒球比赛中，什么样的战术决策能提高得分期望？**

分析分三层推进：

1. **数据层**：抓取并清洗 Statcast 逐球数据，建立可复用的数据表；
2. **统计层**：从逐球记录汇总出场级别的指标（盗垒成功率、得分期望）；
3. **模型层**：用马尔可夫链描述垒上局面与出局数的状态转移，量化盗垒、短打等战术的期望得分价值。

## 数据来源

| 项目 | 说明 |
|---|---|
| 数据源 | MLB Baseball Savant（https://baseballsavant.mlb.com） |
| 获取工具 | Python 库 `pybaseball` |
| 数据粒度 | 逐球（一次投球 = 一行） |
| 覆盖范围 | 全联盟完整逐球覆盖自 2015 年起 |
| 样本 | 单日全部比赛约 4000 次投球、约 15 场比赛 |

数据由公开渠道获取，仅用于学习与研究。

## 当前进度

| Day | 主题 | 产出 | 状态 |
|---|---|---|---|
| 1 | 抓取 Statcast 逐球数据 | `notebooks/01_fetch_statcast_data.ipynb`、`data/raw/2025-06-14_statcast.csv` | 已完成 |
| 2 | 计算盗垒成功率 | `notebooks/02_calculate_stolen_base.ipynb`、`data/processed/2025-06-14_sb_summary.csv` | 已完成 |
| 3 | 得分期望柱状图（RE24 雏形） | `notebooks/03_plot_expected_runs.ipynb` | 计划中 |
| 4+ | 马尔可夫链战术决策建模 | `notebooks/04_...` | 计划中 |

## 目录结构

```
Baseball-Analytics/
├── README.md
├── requirements.txt
├── notebooks/                  # 按序号命名的分析脚本
│   └── 01_fetch_statcast_data.ipynb
└── data/
    └── raw/                    # 原始数据，按日期命名
        └── 2025-06-14_statcast.csv
```

命名规则：`序号_动词_主题.ipynb`，序号决定阅读顺序。

## 环境

- Python 3.10+
- 依赖见 `requirements.txt`：`pybaseball`、`pandas`、`numpy`、`matplotlib`

安装：

```bash
python -m pip install -r requirements.txt
```

国内网络可加镜像源：

```bash
python -m pip install -r requirements.txt -i https://mirrors.aliyun.com/pypi/simple/
```

## 数据字段说明

Day 1 保存的 CSV 包含以下主要字段：

| 字段 | 含义 | 备注 |
|---|---|---|
| `game_date` | 比赛日期 | |
| `pitcher` | 投手 MLBAM ID | |
| `batter` | 打者 MLBAM ID | |
| `release_speed` | 出手球速（mph） | |
| `events` | 该打席的最终结果 | 仅每个打席最后一球有值 |
| `launch_speed` | 击球初速（mph） | 仅击出的球有值 |
| `launch_angle` | 击球仰角（度） | 仅击出的球有值 |
| `game_pk` | 比赛编号 | 用于合并与去重 |
| `inning` / `inning_topbot` | 局数 / 上下半局 | |
| `outs_when_up` | 投球前的出局数 | 得分期望计算的状态量 |
| `on_1b` / `on_2b` / `on_3b` | 各垒跑者 ID | 空表示该垒无人 |

缺失值不等于错误：`events`、`launch_speed`、`launch_angle` 只在特定行有值，这是逐球数据的正常结构。

## 术语说明

- **Statcast**：MLB 的逐球追踪系统，记录球速、击球初速、仰角等。
- **RE24**：以 24 个基础状态（3 种出局数 × 8 种跑垒员组合）为索引的期望得分表。
- **盗垒成功率**：盗垒成功次数 / 盗垒尝试次数。
- **马尔可夫链**：用状态和状态间转移概率描述过程，本项目用它计算不同战术下的期望得分。

## 运行方式

依次运行 `notebooks/` 下的 notebook，序号即执行顺序。每个 notebook 开头都有 Markdown 说明：这一步做了什么、数据从哪来、结果是什么。

## 更新记录

每天一次提交，提交信息格式：`Day N: 具体改动`。
