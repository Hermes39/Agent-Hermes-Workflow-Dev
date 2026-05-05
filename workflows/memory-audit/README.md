---
name: memory-audit
description: "Three-tier audit system with feedback loop — module overview."
version: 1.0.0
updated: 2026-05-05
---

<p align="right">
<a href="README.md">English</a> |
<a href="README.zh-CN.md">简体中文</a> |
<a href="README.zh-TW.md">繁體中文</a> |
<a href="README.ja.md">日本語</a>
</p>

# Memory Audit Workflow

Memory Audit System — An automated memory quality management framework.

## Problem

The persistent memory of AI Agents degrades over time: redundant entries accumulate, old information becomes stale, and classification becomes disorganized. An Agent full of erroneous memories is more dangerous than having no memory at all — it will make decisions based on wrong premises.

Memory Audit maintains memory quality through periodic review, tiered response, and feedback tracking.

## Features

- **Three-tier Audit** — Daily (lightweight scan) / Weekly (medium review) / Monthly (deep analysis)
- **Severity Classification** — trivial → critical, from auto-fix to manual approval
- **Feedback Lifecycle** — pending → decided → archived, fully tracking every discovery
- **Smart Cleanup** — Determines whether to auto-delete based on pending status

## Audited Memory Model

The audit system is designed for a layered persistent memory architecture. Reference model (five layers):

| Layer | Content | Change Frequency |
|-------|---------|-----------------|
| Config | Platform credentials, service configuration | Rare |
| Profile | Interaction preferences, communication style | Occasional |
| Memory | Short fact indexes | Moderate |
| Skills | Complete workflows, deep knowledge | Moderate |
| Session | Current conversation context | Very high |

> This is a reference model, not a hard requirement. Your Agent may have a different layer structure. See [ARCHITECTURE.md](ARCHITECTURE.md) for details.

## Directory Structure

```
memory-audit/
├── ARCHITECTURE.md          # Design philosophy document
├── README.md                # This file
└── templates/               # Template files
    ├── config.json          # Configuration template
    ├── daily-report.md      # Daily report template
    ├── weekly-report.md     # Weekly report template
    ├── monthly-report.md    # Monthly report template
    ├── feedback.json        # Feedback status template
    └── gitignore            # Git ignore rules
```

## Runtime Directory

Data directory structure at runtime:

```
$DATA_DIR/memory-audit/
├── state/
│   └── feedback.json              # Feedback status tracking
├── records/
│   ├── daily/                     # Daily reports (configurable retention)
│   │   ├── YYYY-MM-DD-report.md
│   │   └── ...
│   ├── weekly/                    # Weekly reports (permanent retention)
│   │   ├── YYYY-WNN-weekly.md
│   │   └── ...
│   └── monthly/                   # Monthly reports (permanent retention)
│       ├── YYYY-MM-monthly.md
│       └── ...
└── backups/
    ├── daily/                     # Daily backups (configurable retention)
    ├── weekly/                    # Weekly backups (permanent retention)
    └── monthly/                   # Monthly backups (permanent retention)
```

**Creation command**:

```bash
mkdir -p $DATA_DIR/memory-audit/{state,records/{daily,weekly,monthly},backups/{daily,weekly,monthly}}
```

## Retention Policy

| Content | Retention | Version Control |
|---------|-----------|----------------|
| Daily reports/backups | Configurable (default 14 days) | No (local only) |
| Weekly reports/backups | Permanent | Yes |
| Monthly reports/backups | Permanent | Yes |
| feedback.json | Dynamic (auto-cleanup after 60-day timeout) | Yes |

Specific values are configured in the `retention` field of `config.json`. See [Configuration](#configuration) for details.

## Template Descriptions

The `templates/` directory contains the following templates:

| File | Purpose |
|------|---------|
| `config.json` | Configuration file template (retention policy, scheduling, notifications) |
| `daily-report.md` | Daily audit report template |
| `weekly-report.md` | Weekly audit report template |
| `monthly-report.md` | Monthly audit report template |
| `feedback.json` | Feedback status file template |
| `gitignore` | Git ignore rules template |

**Placeholder format**: Templates use `{placeholder}` format, such as `{YYYY-MM-DD}`, `{description}`, `{n}`, etc.

## Configuration

`templates/config.json` is the core configuration file for the audit system. Main fields:

| Field | Description | Default |
|-------|------------|---------|
| `data_dir` | Data storage root directory | `$DATA_DIR/memory-audit` |
| `retention.daily_reports` | Days to keep daily reports | `14` |
| `retention.daily_backups` | Days to keep daily backups | `14` |
| `retention.weekly_reports` | Days to keep weekly reports (-1 = permanent) | `-1` |
| `retention.monthly_reports` | Days to keep monthly reports (-1 = permanent) | `-1` |
| `retention.feedback_timeout_days` | Feedback entry timeout in days | `60` |
| `audit_schedule` | Cron expressions for each audit tier | See template |
| `severity` | Severity groupings (auto_fix / require_approval / immediate_alert) | See template |
| `notification` | Notification targets for each audit tier | Needs configuration |

`retention` controls file cleanup cycles, `severity` determines the auto/manual handling path for issues, `audit_schedule` defines audit trigger times, and `notification` configures report delivery channels.

## Quick Start

**Prerequisites**:

- Persistent memory system (Agent needs to save information across sessions)
- Scheduled triggering capability (cron / systemd timer / framework scheduler)
- Notification channel (email / IM / webhook / log)

**Steps**:

1. Read [ARCHITECTURE.md](ARCHITECTURE.md) to understand the design philosophy
2. Copy templates from `templates/` to your working directory
3. Adjust template formats and fields as needed
4. Configure scheduled tasks
5. Set up notification channels

## Adaptation Guide

This framework is agent-agnostic and can be adapted for any AI agent with persistent memory:

| Component | Default Implementation | Replaceable With |
|-----------|----------------------|-----------------|
| Scheduler | cron | systemd timer, framework built-in |
| Storage | File system | Database, API |
| Notification | Email/IM | Webhook, log |
| Feedback | JSON file | Database, state machine |