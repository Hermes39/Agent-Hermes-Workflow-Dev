<p align="right">
<a href="README.md">English</a> |
<a href="README.zh-CN.md">简体中文</a> |
<a href="README.zh-TW.md">繁體中文</a> |
<a href="README.ja.md">日本語</a>
</p>

# Memory Audit Workflow

メモリ監査システム — 自動化されたメモリ品質管理フレームワークです。

## 問題

AI Agent の永続メモリは時間とともに劣化します：冗長なエントリが蓄積し、古い情報が陳腐化し、分類が混乱します。誤ったメモリで満たされた Agent はメモリがない場合よりも危険です——誤った前提に基づいて判断を行うためです。

Memory Audit は定期的な監査、段階的な応答、フィードバックの追跡を通じてメモリ品質を維持します。

## 機能

- **3段階監査** — Daily（軽量スキャン）/ Weekly（中程度のレビュー）/ Monthly（深度分析）
- **重要度分類** — trivial → critical、自動修復から手動承認まで
- **フィードバックライフサイクル** — pending → decided → archived、各発見を完全に追跡
- **スマートクリーンアップ** — 保留状態に応じて自動削除の可否を決定

## 監査対象のメモリモデル

監査システムは階層的な永続メモリアーキテクチャに適用されます。参考モデル（5レイヤ）：

| レイヤ | 内容 | 変化頻度 |
|--------|------|---------|
| Config | プラットフォーム認証情報、サービス設定 | 極めて少ない |
| Profile | インタラクションの好み、コミュニケーションスタイル | 時折 |
| Memory | 短い事実インデックス | 中程度 |
| Skills | 完全なワークフロー、深度知識 | 中程度 |
| Session | 現在の会話コンテキスト | 非常に高い |

> これは参考モデルであり、厳格な要件ではありません。あなたの Agent は異なるレイヤ構造を持つかもしれません。詳細は [ARCHITECTURE.md](ARCHITECTURE.md) をご覧ください。

## ディレクトリ構造

```
memory-audit/
├── ARCHITECTURE.md          # 設計思想ドキュメント
├── README.md                # 本ファイル
└── templates/               # テンプレートファイル
    ├── config.json          # 設定テンプレート
    ├── daily-report.md      # 毎日レポートテンプレート
    ├── weekly-report.md     # 週次レポートテンプレート
    ├── monthly-report.md    # 月次レポートテンプレート
    ├── feedback.json        # フィードバック状態テンプレート
    └── gitignore            # Git 無視ルール
```

## ランタイムディレクトリ

監査システムのランタイムデータディレクトリ構造：

```
$DATA_DIR/memory-audit/
├── state/
│   └── feedback.json              # フィードバック状態追跡
├── records/
│   ├── daily/                     # 毎日レポート（設定可能な保持期間）
│   │   ├── YYYY-MM-DD-report.md
│   │   └── ...
│   ├── weekly/                    # 週次レポート（永久保持）
│   │   ├── YYYY-WNN-weekly.md
│   │   └── ...
│   └── monthly/                   # 月次レポート（永久保持）
│       ├── YYYY-MM-monthly.md
│       └── ...
└── backups/
    ├── daily/                     # 毎日バックアップ（設定可能な保持期間）
    ├── weekly/                    # 週次バックアップ（永久保持）
    └── monthly/                   # 月次バックアップ（永久保持）
```

**作成コマンド**：

```bash
mkdir -p $DATA_DIR/memory-audit/{state,records/{daily,weekly,monthly},backups/{daily,weekly,monthly}}
```

## 保持ポリシー

| 内容 | 保持期間 | バージョン管理 |
|------|---------|--------------|
| daily レポート/バックアップ | 設定可能（デフォルト 14 日） | いいえ（ローカルのみ） |
| weekly レポート/バックアップ | 永久 | はい |
| monthly レポート/バックアップ | 永久 | はい |
| feedback.json | 動的（タイムアウト 60 日で自動クリーンアップ） | はい |

具体的な数値は `config.json` の `retention` フィールドで設定します。詳細は [設定説明](#設定説明) をご覧ください。

## テンプレート説明

`templates/` ディレクトリには以下のテンプレートが含まれています：

| ファイル | 用途 |
|---------|------|
| `config.json` | 設定ファイルテンプレート（保持ポリシー、スケジューリング、通知） |
| `daily-report.md` | 毎日監査レポートテンプレート |
| `weekly-report.md` | 毎週監査レポートテンプレート |
| `monthly-report.md` | 毎月監査レポートテンプレート |
| `feedback.json` | フィードバック状態ファイルテンプレート |
| `gitignore` | Git 無視ルールテンプレート |

**プレースホルダー形式**：テンプレートは `{placeholder}` 形式を使用します。例：`{YYYY-MM-DD}`、`{description}`、`{n}` など。

## 設定説明

`templates/config.json` は監査システムのコア設定ファイルです。主要フィールド：

| フィールド | 説明 | デフォルト値 |
|-----------|------|------------|
| `data_dir` | データ保存ルートディレクトリ | `$DATA_DIR/memory-audit` |
| `retention.daily_reports` | 毎日レポートの保持日数 | `14` |
| `retention.daily_backups` | 毎日バックアップの保持日数 | `14` |
| `retention.weekly_reports` | 週次レポートの保持日数（-1 = 永久） | `-1` |
| `retention.monthly_reports` | 月次レポートの保持日数（-1 = 永久） | `-1` |
| `retention.feedback_timeout_days` | フィードバックエントリのタイムアウト日数 | `60` |
| `audit_schedule` | 各監査の cron 式 | テンプレート参照 |
| `severity` | 重要度グループ（auto_fix / require_approval / immediate_alert） | テンプレート参照 |
| `notification` | 各監査の通知先 | 設定が必要 |

`retention` はファイルのクリーンアップ周期を制御し、`severity` は問題の自動/手動処理パスを決定し、`audit_schedule` は監査トリガーの時刻を定義し、`notification` はレポートの配信チャネルを設定します。

## クイックスタート

**前提条件**：

- 永続メモリシステム（Agent がセッション間で情報を保存する必要がある）
- 定時スケジューリング能力（cron / systemd timer / フレームワークスケジューラ）
- 通知チャネル（メール / IM / webhook / ログ）

**手順**：

1. [ARCHITECTURE.md](ARCHITECTURE.md) を読んで設計思想を理解する
2. `templates/` のテンプレートをワークディレクトリにコピーする
3. ニーズに合わせてテンプレートのフォーマットとフィールドを調整する
4. 定時タスクを設定する
5. 通知チャネルを設定する

## 適応ガイド

本フレームワークは agent に依存せず、永続メモリを持つ任意の AI agent に適応できます：

| コンポーネント | デフォルト実装 | 置換可能 |
|---------------|-------------|---------|
| スケジューラ | cron | systemd timer、フレームワーク内蔵 |
| ストレージ | ファイルシステム | データベース、API |
| 通知 | メール/IM | webhook、ログ |
| フィードバック | JSON ファイル | データベース、ステートマシン |
