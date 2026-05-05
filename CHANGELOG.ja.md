<p align="right">
<a href="CHANGELOG.md">English</a> |
<a href="CHANGELOG.zh-CN.md">简体中文</a> |
<a href="CHANGELOG.zh-TW.md">繁體中文</a> |
<a href="CHANGELOG.ja.md">日本語</a>
</p>

# 変更履歴

このプロジェクトのすべての重要な変更はこのファイルに記録されます。

フォーマット: [Keep a Changelog](https://keepachangelog.com/)、バージョニング: [セマンティックバージョニング](https://semver.org/)。

## [1.0.0] - 2026-05-05

### 追加

- `workflows/memory-audit/` — 3層監査システム（日次/週次/月次）、フィードバックループ付き
  - ARCHITECTURE.md: 設計哲学ドキュメント（英語、簡中、繁中、日本語）
  - README.md: モジュール概要（英語、簡中、繁中、日本語）
  - templates/: config.json、feedback.json、日次/週次/月次レポート、gitignore
- 4層アーキテクチャドキュメント（ワークフロー → スキル → ドキュメント → 設定）
- プロジェクト README とアーキテクチャ概要
- MIT ライセンス
