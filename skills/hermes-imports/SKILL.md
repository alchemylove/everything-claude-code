---
name: hermes-imports
description: Convert local Hermes operator workflows into sanitized ECC skills and release-pack artifacts. Use when preparing a Hermes workflow for public ECC reuse without leaking private workspace state, credentials, or local-only paths.
origin: ECC
---

# Hermes インポート (Hermes Imports)

繰り返し Hermes ワークフローを ECC で安全に出荷できる形に変換するときにこのスキルを使用する。

Hermes はオペレーターシェル。ECC は再利用可能なワークフローレイヤー。インポートは private state を移さず、安定パターンを Hermes から ECC へ移す。

## 使用タイミング (When To Use)

- Hermes ワークフローが再利用可能になるほど繰り返された。
- ローカルオペレータープロンプトを公開 ECC スキルにしたい。
- ローンチ、コンテンツ、リサーチ、エンジニアリングワークフローにサニタイズ済み引き渡しドキュメントが必要。
- ワークフローに公開前に除去すべきローカルパス、認証情報、個人データセット、非公開アカウント名が含まれる。

## インポートルール (Import Rules)

- ローカルパスを repo-relative パスまたはプレースホルダーに変換する。
- ライブアカウント名を `operator`、`default profile`、`workspace owner` などのロールラベルに置換する。
- 認証情報要件はプロバイダー名のみで記述する。
- 例は狭く運用的に保つ。
- 生のワークスペースエクスポート、トークン、OAuth ファイル、健康データ、CRM データ、財務データを出荷しない。
- ワークフローが意味を持つために private state が必要ならローカルに留める。

## サニタイズチェックリスト (Sanitization Checklist)

インポートワークフローをコミットする前に次をスキャンする:

- `/Users/...` のような絶対パス
- ドキュメントが明示的にローカルセットアップを説明しない限り `~/.hermes` パス
- API キー、トークン、Cookie、OAuth ファイル、bearer 文字列
- 電話番号、非公開メールアドレス、個人コンタクトグラフ
- 既に公開されていないクライアント名、家族名、アカウント名
- 収益、健康、CRM 詳細
- 非公開システムからのツール出力を含む生ログ

## 変換パターン (Conversion Pattern)

1. 繰り返し可能なオペレーターループを特定する。
2. 非公開入力と出力を除去する。
3. ローカルパスを repo-relative 例として書き直す。
4. 一回限りの指示を `When To Use` セクションと短いプロセスに変える。
5. 具体的な出力要件を追加する。
6. PR を開く前に secret とローカルパススキャンを実行する。

## 例: ローンチ引き渡し (Example: Launch Handoff)

ローカル Hermes プロンプト:

```text
Read my local workspace files and finalize launch copy.
```

ECC 安全版:

```text
Use the public release pack under docs/releases/<version>/.
Return one X thread, one LinkedIn post, one recording checklist, and the missing assets list.
```

## 例: クワイエットアワーオペレータージョブ (Example: Quiet-Hours Operator Job)

ローカル Hermes ジョブ:

```text
Run my private inbox, finance, and content checks overnight.
```

ECC 安全版:

```text
Describe the scheduler policy, the quiet-hours window, the escalation rules, and the categories of checks. Do not include private data sources or credentials.
```

## 出力契約 (Output Contract)

返すもの:

- 候補 ECC スキル名
- サニタイズ済みワークフロー要約
- 必要な公開入力
- 除去した非公開入力
- 残存リスク
- 作成または更新すべきファイル
