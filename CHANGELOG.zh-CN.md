# 变更日志

本文件记录项目的所有重要变更。

格式遵循 [Keep a Changelog](https://keepachangelog.com/)。

## [1.2.0] - 2026-05-05

### 新增
- 报告模板（日/周/月）现提供 4 语言版本：EN、zh-CN、zh-TW、JA
- ARCHITECTURE.md 5.1 节补充 feedback.json 信封结构文档（version、updated、items）

### 修复
- 日语翻译：频度 → 頻度（ARCHITECTURE.ja.md 第 297 行）
- 页脚版本格式统一："v1.1" → "1.1.0"（与 frontmatter semver 一致）

## [1.1.0] - 2026-05-05

### 新增
- 所有 ARCHITECTURE.md 添加 YAML frontmatter（name、description、version、updated）
- 四语言支持：ARCHITECTURE.md 的 zh-CN、zh-TW、ja 翻译

### 变更
- 架构与实际实现对齐：审计与反馈合并为单一工作流
- Honest README 方式：README 只列出实际存在的内容
- 移除从未填充的空 docs/ 文件

## [1.0.0] - 2026-05-05

### 新增
- 记忆审计工作流初始发布
- ARCHITECTURE.md：完整设计文档
- README.md：快速开始和使用指南
- 模板：config.json、feedback.json、日/周/月报告骨架
