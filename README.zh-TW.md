<p align="right">
<a href="README.md">English</a> |
<a href="README.zh-CN.md">简体中文</a> |
<a href="README.zh-TW.md">繁體中文</a> |
<a href="README.ja.md">日本語</a>
</p>
# Agent Lab Hermes

> AI Agent 運維知識庫 — 工作流架構設計與可複用 Skill 模板。

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## 概述

本倉庫記錄了 AI Agent 生產環境運維的實踐模式：如何組織工作流、管理持久記憶、
維護版本化知識庫，以及構建可複用的操作技能。

內容來源於運行 Hermes Agent 實例的實戰經驗，經過泛化處理，供其他 Agent 運營者
參考和適配。

## 架構

Agent 運維遵循四層設計：

```
工作流  →  技能  →  文件  →  配置
 (設計)    (執行)    (說明)    (參數)
```

| 層級 | 內容 | 受眾 | 變更頻率 |
|------|------|------|----------|
| **工作流** | 架構設計 — 系統做什麼、為什麼這樣做 | 架構師、Agent | 極少 |
| **技能** | 操作流程 — 具體任務如何執行 | Agent、運維人員 | 偶爾 |
| **文件** | 設計說明 — 為什麼這樣設計 | 人類、新 Agent | 隨技能變更 |
| **配置** | 實例配置 — 具體的參數和設置 | Agent、部署腳本 | 頻繁 |

每個工作流和技能都是自包含單元，攜帶自己的文件、參考資料和模板。

## 工作流

| 工作流 | 說明 |
|--------|------|
| `memory-audit` | 三級審計系統（日/週/月），帶反饋閉環 |

## 快速開始

1. 瀏覽 `workflows/` 目錄，找到與你場景相關的架構設計
2. 每個工作流包含獨立的 README、架構文件和模板

## 貢獻

歡迎貢獻。提交 Pull Request 前請先開 Issue 討論。

## 許可證

[MIT](LICENSE)
