<p align="right">
<a href="CHANGELOG.md">English</a> |
<a href="CHANGELOG.zh-CN.md">简体中文</a> |
<a href="CHANGELOG.zh-TW.md">繁體中文</a> |
<a href="CHANGELOG.ja.md">日本語</a>
</p>

# 更新日誌

本項目的所有重要變更將記錄在此檔案。

格式：[Keep a Changelog](https://keepachangelog.com/)，版本：[語意化版本](https://semver.org/)。

## [1.1.0] - 2026-05-05

### 新增

- `workflows/memory-audit/` ARCHITECTURE.md v1.1.0 — 擴展設計哲學，新增設計決策紀錄、常見陷阱、配置結構文件
- 所有製品檔案添加 YAML frontmatter（name、description、version、updated）
- 版本控制系統：三層方案（製品、倉庫、同步）

## [1.0.0] - 2026-05-05

### 新增

- `workflows/memory-audit/` — 三級審計系統（日/週/月），帶反饋閉環
  - ARCHITECTURE.md：設計哲學文件（英、簡中、繁中、日）
  - README.md：模組概述（英、簡中、繁中、日）
  - templates/：config.json、feedback.json、日/週/月報告範本、gitignore
- 四層架構文件（工作流 → 技能 → 文件 → 配置）
- 專案 README 與架構概述
- MIT 授權條款
