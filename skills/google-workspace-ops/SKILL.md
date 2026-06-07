---
name: google-workspace-ops
description: Operate across Google Drive, Docs, Sheets, and Slides as one workflow surface for plans, trackers, decks, and shared documents. Use when the user needs to find, summarize, edit, migrate, or clean up Google Workspace assets without dropping to raw tool calls.
origin: ECC
---

# Google Workspace 運用 (Google Workspace Ops)

このスキルは、単一ファイルの孤立編集ではなく、共有ドキュメント、スプレッドシート、デッキを動くシステムとして運用するためのもの。

## 使用タイミング (When to Use)

- ユーザーが doc、sheet、deck を見つけてその場で更新する必要がある
- Google Drive に保存された計画、トラッカー、メモ、顧客リストの統合
- 共有スプレッドシートのクリーンアップまたは再構造化
- Google Slides デッキのインポート、修復、再フォーマット
- 意思決定のための Docs、Sheets、Slides からの要約作成

## 推奨ツールサーフェス (Preferred Tool Surface)

Google Drive をエントリポイントにし、適切なスペシャリストへ切り替える:

- テキスト中心ドキュメントは Google Docs
- 表形式作業、数式、チャートは Google Sheets
- デッキ、インポート、テンプレート移行、クリーンアップは Google Slides

ファイル名だけから構造を推測しない。先に検査する。

## ワークフロー (Workflow)

### 1. アセットを見つける (1. Find the asset)

Drive 検索サーフェスから次を特定して開始する:

- 正確なファイル
- 兄弟アセット
- 重複の可能性
- 最近更新されたバージョン

複数ドキュメントが似ている場合、タイトル、所有者、更新時刻、フォルダで確認する。

### 2. 編集前に検査する (2. Inspect before editing)

変更前に:

- 現在の構造を要約する
- タブ、見出し、スライド数を特定する
- タスクがローカルクリーンアップか構造手術かを検出する

安全に作業できる最小ツールを選ぶ。

### 3. 精密に編集する (3. Edit with precision)

- Docs: 曖昧な書き換えではなく index  aware 編集
- Sheets: 明示的タブと範囲で操作
- Slides: コンテンツ編集とビジュアルクリーンアップ/テンプレート移行を区別

要求作業がビジュアルまたはレイアウト敏感なら、一度の巨大な盲目更新ではなく検査と検証で反復する。

### 4. 動くシステムをきれいに保つ (4. Keep the working system clean)

ファイルがより大きなワークフローの一部なら次も表面化する:

- 重複トラッカー
- 古いデッキ
- 正規ドキュメント対古いドキュメント
- アーカイブ、マージ、リネームすべきか

## 出力形式 (Output Format)

次を使用する:

```text
ASSET
- file name
- type
- why this is the right file

CURRENT STATE
- structure summary
- key problems or blockers

ACTION
- edits made or recommended

FOLLOW-UPS
- archive / merge / duplicate cleanup / next file to update
```

## 適したユースケース (Good Use Cases)

- "Find the active planning doc and condense it"
- "Clean up this customer spreadsheet and show me the churn-risk rows"
- "Import this deck into Slides and make it presentable"
- "Find the current tracker, not the stale duplicate"
