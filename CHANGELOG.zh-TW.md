# 變更日誌

本檔案記錄專案的所有重要變更。

格式遵循 [Keep a Changelog](https://keepachangelog.com/)。

## [1.2.0] - 2026-05-05

### 新增
- 報告模板（日/週/月）現提供 4 語言版本：EN、zh-CN、zh-TW、JA
- ARCHITECTURE.md 5.1 節補充 feedback.json 信封結構文件（version、updated、items）

### 修復
- 日語翻譯：频度 → 頻度（ARCHITECTURE.ja.md 第 297 行）
- 頁尾版本格式統一："v1.1" → "1.1.0"（與 frontmatter semver 一致）

## [1.1.0] - 2026-05-05

### 新增
- 所有 ARCHITECTURE.md 添加 YAML frontmatter（name、description、version、updated）
- 四語言支持：ARCHITECTURE.md 的 zh-CN、zh-TW、ja 翻譯

### 變更
- 架構與實際實現對齊：審計與回饋合併為單一工作流
- Honest README 方式：README 只列出實際存在的內容
- 移除從未填充的空 docs/ 檔案

## [1.0.0] - 2026-05-05

### 新增
- 記憶審計工作流初始發布
- ARCHITECTURE.md：完整設計文件
- README.md：快速開始和使用指南
- 模板：config.json、feedback.json、日/週/月報告骨架
