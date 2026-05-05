# 変更履歴

このファイルはプロジェクトの重要な変更を記録します。

フォーマットは [Keep a Changelog](https://keepachangelog.com/) に準拠。

## [1.2.0] - 2026-05-05

### 追加
- レポートテンプレート（日次/週次/月次）が4言語で利用可能に：EN、zh-CN、zh-TW、JA
- ARCHITECTURE.md 5.1節にfeedback.jsonエンベロープ構造のドキュメント追加（version、updated、items）

### 修正
- 日本語翻訳：频度 → 頻度（ARCHITECTURE.ja.md 297行目）
- フッターバージョン形式統一："v1.1" → "1.1.0"（frontmatterのsemverと一致）

## [1.1.0] - 2026-05-05

### 追加
- 全ARCHITECTURE.mdにYAML frontmatter追加（name、description、version、updated）
- 4言語対応：ARCHITECTURE.mdのzh-CN、zh-TW、ja翻訳

### 変更
- 実装に合わせたアーキテクチャ：監査＋フィードバックを単一ワークフローに統合
- Honest README方式：READMEには実際に存在するコンテンツのみ記載
- 未使用の空のdocs/ファイルを削除

## [1.0.0] - 2026-05-05

### 追加
- メモリ監査ワークフロー初期リリース
- ARCHITECTURE.md：完全な設計ドキュメント
- README.md：クイックスタートと使用ガイド
- テンプレート：config.json、feedback.json、日次/週次/月次レポートスケルトン
