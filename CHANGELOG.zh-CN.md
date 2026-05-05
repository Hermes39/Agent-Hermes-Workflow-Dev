<p align="right">
<a href="CHANGELOG.md">English</a> |
<a href="CHANGELOG.zh-CN.md">简体中文</a> |
<a href="CHANGELOG.zh-TW.md">繁體中文</a> |
<a href="CHANGELOG.ja.md">日本語</a>
</p>

# 更新日志

本项目的所有重要变更将记录在此文件。

格式：[Keep a Changelog](https://keepachangelog.com/)，版本：[语义化版本](https://semver.org/)。

## [1.1.0] - 2026-05-04

### 新增

- `workflows/memory-audit/` ARCHITECTURE.md v1.1.0 — 扩展设计哲学，新增设计决策记录、常见陷阱、配置结构文档
- 所有制品文件添加 YAML frontmatter（name、description、version、updated）
- 版本控制系统：三层方案（制品、仓库、同步）

## [1.0.0] - 2026-05-04

### 新增

- `workflows/memory-audit/` — 三级审计系统（日/周/月），带反馈闭环
  - ARCHITECTURE.md：设计哲学文档（英、简中、繁中、日）
  - README.md：模块概述（英、简中、繁中、日）
  - templates/：config.json、feedback.json、日/周/月报告模板、gitignore
- 四层架构文档（工作流 → 技能 → 文档 → 配置）
- 项目 README 与架构概述
- MIT 许可证
