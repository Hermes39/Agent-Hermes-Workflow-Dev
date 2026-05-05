<p align="right">
<a href="README.md">English</a> |
<a href="README.zh-CN.md">简体中文</a> |
<a href="README.zh-TW.md">繁體中文</a> |
<a href="README.ja.md">日本語</a>
</p>

# Memory Audit Workflow

記憶審計系統 — 自動化記憶品質管理框架。

## 問題

AI Agent 的持久記憶會隨時間退化：冗餘條目累積、舊資訊過時、分類混亂。一個充滿錯誤記憶的 Agent 比沒有記憶更危險——它會基於錯誤前提做決策。

Memory Audit 透過定期審查、分級回應、反饋追蹤來維持記憶品質。

## 功能

- **三級審計** — Daily（輕量掃描）/ Weekly（中等審查）/ Monthly（深度分析）
- **嚴重度分級** — trivial → critical，從自動修復到人工審批
- **反饋生命週期** — pending → decided → archived，完整追蹤每個發現
- **智慧清理** — 根據待決狀態決定是否自動刪除

## 被審計的記憶模型

審計系統適用於分層持久記憶架構。參考模型（五層）：

| 層級 | 內容 | 變化頻率 |
|------|------|----------|
| Config | 平台憑證、服務配置 | 極少 |
| Profile | 互動偏好、溝通風格 | 偶爾 |
| Memory | 短事實索引 | 中等 |
| Skills | 完整流程、深度知識 | 中等 |
| Session | 當前對話上下文 | 極高 |

> 這是參考模型，不是硬性要求。你的 Agent 可能有不同的層級結構。詳見 [ARCHITECTURE.md](ARCHITECTURE.md)。

## 目錄結構

```
memory-audit/
├── ARCHITECTURE.md          # 設計思想文件
├── README.md                # 本檔案
└── templates/               # 範本檔案
    ├── config.json          # 配置範本
    ├── daily-report.md      # 每日報告範本
    ├── weekly-report.md     # 週報告範本
    ├── monthly-report.md    # 月報告範本
    ├── feedback.json        # 反饋狀態範本
    └── gitignore            # Git 忽略規則
```

## 運行時目錄

審計系統運行時的資料目錄結構：

```
$DATA_DIR/memory-audit/
├── state/
│   └── feedback.json              # 反饋狀態追蹤
├── records/
│   ├── daily/                     # 每日報告（可配置保留期）
│   │   ├── YYYY-MM-DD-report.md
│   │   └── ...
│   ├── weekly/                    # 週報告（永久保留）
│   │   ├── YYYY-WNN-weekly.md
│   │   └── ...
│   └── monthly/                   # 月報告（永久保留）
│       ├── YYYY-MM-monthly.md
│       └── ...
└── backups/
    ├── daily/                     # 每日備份（可配置保留期）
    ├── weekly/                    # 週備份（永久保留）
    └── monthly/                   # 月備份（永久保留）
```

**建立命令**：

```bash
mkdir -p $DATA_DIR/memory-audit/{state,records/{daily,weekly,monthly},backups/{daily,weekly,monthly}}
```

## 保留策略

| 內容 | 保留期 | 版本控制 |
|------|--------|----------|
| daily 報告/備份 | 可配置（預設 14 天） | 否（僅本地） |
| weekly 報告/備份 | 永久 | 是 |
| monthly 報告/備份 | 永久 | 是 |
| feedback.json | 動態（逾時 60 天自動清理） | 是 |

具體數值在 `config.json` 的 `retention` 欄位中配置，詳見 [配置說明](#配置說明)。

## 範本說明

`templates/` 目錄包含以下範本：

| 檔案 | 用途 |
|------|------|
| `config.json` | 配置檔案範本（保留策略、排程、通知） |
| `daily-report.md` | 每日審計報告範本 |
| `weekly-report.md` | 每週審計報告範本 |
| `monthly-report.md` | 每月審計報告範本 |
| `feedback.json` | 反饋狀態檔案範本 |
| `gitignore` | Git 忽略規則範本 |

**佔位符格式**：範本使用 `{placeholder}` 格式，如 `{YYYY-MM-DD}`、`{描述}`、`{n}` 等。

## 配置說明

`templates/config.json` 是審計系統的核心配置檔案。主要欄位：

| 欄位 | 說明 | 預設值 |
|------|------|--------|
| `data_dir` | 資料儲存根目錄 | `$DATA_DIR/memory-audit` |
| `retention.daily_reports` | 每日報告保留天數 | `14` |
| `retention.daily_backups` | 每日備份保留天數 | `14` |
| `retention.weekly_reports` | 週報告保留天數（-1 = 永久） | `-1` |
| `retention.monthly_reports` | 月報告保留天數（-1 = 永久） | `-1` |
| `retention.feedback_timeout_days` | 反饋條目逾時天數 | `60` |
| `audit_schedule` | 各級審計的 cron 表達式 | 見範本 |
| `severity` | 嚴重度分組（auto_fix / require_approval / immediate_alert） | 見範本 |
| `notification` | 各級審計的通知目標 | 需配置 |

`retention` 控制檔案清理週期，`severity` 決定問題的自動/手動處理路徑，`audit_schedule` 定義審計觸發時間，`notification` 配置報告推送頻道。

## 快速開始

**前提條件**：

- 持久記憶系統（Agent 需要跨會話儲存資訊）
- 定時排程能力（cron / systemd timer / 框架排程器）
- 通知頻道（郵件 / IM / webhook / 日誌）

**步驟**：

1. 閱讀 [ARCHITECTURE.md](ARCHITECTURE.md) 了解設計思想
2. 複製 `templates/` 中的範本到你的工作目錄
3. 根據需求調整範本格式和欄位
4. 配置定時任務
5. 設定通知頻道

## 適配指南

本框架與 agent 無關，可適配任何具有持久記憶的 AI agent：

| 元件 | 預設實作 | 可替換為 |
|------|----------|----------|
| 排程器 | cron | systemd timer, 框架內建 |
| 儲存 | 檔案系統 | 資料庫, API |
| 通知 | 郵件/IM | webhook, 日誌 |
| 反饋 | JSON 檔案 | 資料庫, 狀態機 |
