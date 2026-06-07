# Hermes x ECC セットアップ (Hermes x ECC Setup)

Hermes は operator shell です。ECC はその背後の再利用可能システムです。

このガイドは、1つのターミナルネイティブ面からコンテンツ、アウトリーチ、リサーチ、セールス ops、ファイナンスチェック、エンジニアリングワークフローを走らせるために使う Hermes スタックの公開・サニタイズ版です。

## 公開で出荷するもの (What Ships Publicly)

- このリポジトリの ECC skills、agents、commands、hooks、MCP configs
- 再利用に十分安定した Hermes 生成ワークフロー skills
- chat、cron、ワークスペース memory、配布フロー向けの文書化された operator トポロジー
- スタックを公開共有するためのローンチコラテラル

このガイドにはプライベート secret、ライブ token、個人データ、生の `~/.hermes` エクスポートは含みません。

## アーキテクチャ (Architecture)

Hermes を正面玄関、ECC を再利用可能 workflow substrate として使う。

```text
Telegram / CLI / TUI
        ↓
      Hermes
        ↓
 ECC skills + hooks + MCPs + generated workflow packs
        ↓
 Google Drive / GitHub / browser automation / research APIs / media tools / finance tools
```

## 公開ワークスペースマップ (Public Workspace Map)

プライベート状態を漏らさずセットアップを再現する最小面として使う。

- `~/.hermes/config.yaml`
  - model routing
  - MCP server 登録
  - plugin 読み込み
- `~/.hermes/skills/ecc-imports/`
  - Hermes ネイティブ利用向けにコピーされた ECC skills
- `skills/hermes-generated/`
  - 繰り返し Hermes セッションから蒸留された operator パターン
- `~/.hermes/plugins/`
  - hooks、reminder、ワークフロー固有 tool glue 向け bridge plugin
- `~/.hermes/cron/jobs.json`
  - 明示的プロンプトとチャネル付きスケジュール automation run
- `~/.hermes/workspace/`
  - ビジネス、ops、health、コンテンツ、memory artifact

## 推奨 Capability スタック (Recommended Capability Stack)

### コア (Core)

- chat、cron、オーケストレーション、ワークスペース状態向け Hermes
- skills、rules、prompt、cross-harness 規約向け ECC
- ベースライン MCP レイヤーとして GitHub + Context7 + Exa + Firecrawl + Playwright

### コンテンツ (Content)

- ローカル編集・組み立て向け FFmpeg
- プログラム可能クリップ向け Remotion
- 画像/動画生成向け fal.ai
- 音声、cleanup、オーディオ packaging 向け ElevenLabs
- 最終ソーシャルネイティブ仕上げ向け CapCut または VectCutAPI

### ビジネス Ops (Business Ops)

- docs、sheet、deck、research dump の system of record として Google Drive
- 収益と支払い運用向け Stripe
- エンジニアリング実行向け GitHub
- 緊急 nudge と承認向け Telegram と iMessage スタイルチャネル

## ローカル Auth がまだ必要なもの (What Still Requires Local Auth)

これらはローカルに残し、operator ごとに設定すべき：

- Drive / Docs / Sheets / Slides 向け Google OAuth token
- X / LinkedIn / アウトバウンド配布認証情報
- Stripe key
- browser automation 認証情報と stealth/proxy 設定
- Linear や Apollo など CRM またはプロジェクトシステム認証情報
- health automation を有効にする場合の Apple Health エクスポートまたは ingest path

## 推奨ブリングアップ順序 (Suggested Bring-Up Order)

0. まず `ecc migrate audit --source ~/.hermes` を実行し、レガシーワークスペースを棚卸し、どの部分がすでに ECC2 にマップするか確認。
0.5. 何かを import する前に migration artifact を計画・scaffold：
   - `ecc migrate plan` と `ecc migrate scaffold` でレビュー可能プランを生成
   - `ecc migrate import-skills --output-dir migration-artifacts/skills` で再利用可能レガシー skill を scaffold
   - `ecc migrate import-tools --output-dir migration-artifacts/tools` で tool 翻訳テンプレートを scaffold
   - `ecc migrate import-plugins --output-dir migration-artifacts/plugins` で bridge plugin テンプレートを scaffold
   - `ecc migrate import-schedules --dry-run` で定期 job をプレビュー
   - `ecc migrate import-remote --dry-run` で gateway dispatch をプレビュー
   - `ecc migrate import-env --dry-run` で安全な env/service context をプレビュー
   - `ecc migrate import-memory` でサニタイズされたワークスペース memory を import
1. ECC をインストールし `node tests/run-all.js` でベースライン harness セットアップを検証；期待結果は失敗ゼロのテストサマリー。
2. Hermes をインストールし ECC import 済み skills を指す。
3. 実際に毎日使う MCP server を登録。
4. まず Google Drive を認証し、次に GitHub、次に配布チャネル。
5. 小さな cron 面から始める：readiness check、content accountability、inbox triage、revenue monitor。
6. その後で health、relationship graphing、アウトバウンド sequencing など重い個人ワークフローを追加。

## 関連 Docs (Related Docs)

- [Hermes/OpenClaw migration guide](HERMES-OPENCLAW-MIGRATION.md)
- [Cross-harness architecture](architecture/cross-harness.md)

## なぜ Hermes x ECC (Why Hermes x ECC)

このスタックは次が欲しいときに有用：

- ビジネスとエンジニアリング運用を走らせる1つのターミナルネイティブ場所
- 使い捨てプロンプトではなく再利用可能 skills
- nudge、監査、エスカレーションできる automation
- プライベート operator 状態を露出せずシステム形状を示す公開リポジトリ

## 公開 Release Candidate スコープ (Public Release Candidate Scope)

ECC v2.0.0-rc.1 は Hermes 面を文書化し、ローンチコラテラルを今出荷します。

残りのプライベート部分は後から重ねられる：

- 追加のサニタイズテンプレート
- より豊富な公開例
- より多くの生成ワークフローパック
- より密な CRM と Google Workspace 統合
