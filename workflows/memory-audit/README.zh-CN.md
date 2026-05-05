<p align="right">
<a href="README.md">English</a> |
<a href="README.zh-CN.md">简体中文</a> |
<a href="README.zh-TW.md">繁體中文</a> |
<a href="README.ja.md">日本語</a>
</p>

# Memory Audit Workflow

记忆审计系统 — 自动化记忆质量管理框架。

## 问题

AI Agent 的持久记忆会随时间退化：冗余条目积累、旧信息过时、分类混乱。一个充满错误记忆的 Agent 比没有记忆更危险——它会基于错误前提做决策。

Memory Audit 通过定期审查、分级响应、反馈追踪来维持记忆质量。

## 功能

- **三级审计** — Daily（轻量扫描）/ Weekly（中等审查）/ Monthly（深度分析）
- **严重度分级** — trivial → critical，从自动修复到人工审批
- **反馈生命周期** — pending → decided → archived，完整追踪每个发现
- **智能清理** — 根据待决状态决定是否自动删除

## 被审计的记忆模型

审计系统适用于分层持久记忆架构。参考模型（五层）：

| 层级 | 内容 | 变化频率 |
|------|------|----------|
| Config | 平台凭据、服务配置 | 极少 |
| Profile | 交互偏好、沟通风格 | 偶尔 |
| Memory | 短事实索引 | 中等 |
| Skills | 完整流程、深度知识 | 中等 |
| Session | 当前对话上下文 | 极高 |

> 这是参考模型，不是硬性要求。你的 Agent 可能有不同的层级结构。详见 [ARCHITECTURE.md](ARCHITECTURE.md)。

## 目录结构

```
memory-audit/
├── ARCHITECTURE.md          # 设计思想文档
├── README.md                # 本文件
└── templates/               # 模板文件
    ├── config.json          # 配置模板
    ├── daily-report.md      # 每日报告模板
    ├── weekly-report.md     # 周报告模板
    ├── monthly-report.md    # 月报告模板
    ├── feedback.json        # 反馈状态模板
    └── gitignore            # Git 忽略规则
```

## 运行时目录

审计系统运行时的数据目录结构：

```
$DATA_DIR/memory-audit/
├── state/
│   └── feedback.json              # 反馈状态追踪
├── records/
│   ├── daily/                     # 每日报告（可配置保留期）
│   │   ├── YYYY-MM-DD-report.md
│   │   └── ...
│   ├── weekly/                    # 周报告（永久保留）
│   │   ├── YYYY-WNN-weekly.md
│   │   └── ...
│   └── monthly/                   # 月报告（永久保留）
│       ├── YYYY-MM-monthly.md
│       └── ...
└── backups/
    ├── daily/                     # 每日备份（可配置保留期）
    ├── weekly/                    # 周备份（永久保留）
    └── monthly/                   # 月备份（永久保留）
```

**创建命令**：

```bash
mkdir -p $DATA_DIR/memory-audit/{state,records/{daily,weekly,monthly},backups/{daily,weekly,monthly}}
```

## 保留策略

| 内容 | 保留期 | 版本控制 |
|------|--------|----------|
| daily 报告/备份 | 可配置（默认 14 天） | 否（仅本地） |
| weekly 报告/备份 | 永久 | 是 |
| monthly 报告/备份 | 永久 | 是 |
| feedback.json | 动态（超时 60 天自动清理） | 是 |

具体数值在 `config.json` 的 `retention` 字段中配置，详见 [配置说明](#配置说明)。

## 模板说明

`templates/` 目录包含以下模板：

| 文件 | 用途 |
|------|------|
| `config.json` | 配置文件模板（保留策略、调度、通知） |
| `daily-report.md` | 每日审计报告模板 |
| `weekly-report.md` | 每周审计报告模板 |
| `monthly-report.md` | 每月审计报告模板 |
| `feedback.json` | 反馈状态文件模板 |
| `gitignore` | Git 忽略规则模板 |

**占位符格式**：模板使用 `{placeholder}` 格式，如 `{YYYY-MM-DD}`、`{描述}`、`{n}` 等。

## 配置说明

`templates/config.json` 是审计系统的核心配置文件。主要字段：

| 字段 | 说明 | 默认值 |
|------|------|--------|
| `data_dir` | 数据存储根目录 | `$DATA_DIR/memory-audit` |
| `retention.daily_reports` | 每日报告保留天数 | `14` |
| `retention.daily_backups` | 每日备份保留天数 | `14` |
| `retention.weekly_reports` | 周报告保留天数（-1 = 永久） | `-1` |
| `retention.monthly_reports` | 月报告保留天数（-1 = 永久） | `-1` |
| `retention.feedback_timeout_days` | 反馈条目超时天数 | `60` |
| `audit_schedule` | 各级审计的 cron 表达式 | 见模板 |
| `severity` | 严重度分组（auto_fix / require_approval / immediate_alert） | 见模板 |
| `notification` | 各级审计的通知目标 | 需配置 |

`retention` 控制文件清理周期，`severity` 决定问题的自动/手动处理路径，`audit_schedule` 定义审计触发时间，`notification` 配置报告推送渠道。

## 快速开始

**前提条件**：

- 持久记忆系统（Agent 需要跨会话保存信息）
- 定时调度能力（cron / systemd timer / 框架调度器）
- 通知渠道（邮件 / IM / webhook / 日志）

**步骤**：

1. 阅读 [ARCHITECTURE.md](ARCHITECTURE.md) 了解设计思想
2. 复制 `templates/` 中的模板到你的工作目录
3. 根据需求调整模板格式和字段
4. 配置定时任务
5. 设置通知渠道

## 适配指南

本框架与 agent 无关，可适配任何具有持久记忆的 AI agent：

| 组件 | 默认实现 | 可替换为 |
|------|----------|----------|
| 调度器 | cron | systemd timer, 框架内置 |
| 存储 | 文件系统 | 数据库, API |
| 通知 | 邮件/IM | webhook, 日志 |
| 反馈 | JSON 文件 | 数据库, 状态机 |
