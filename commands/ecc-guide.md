---
description: ライブリポジトリ表面から ECC の現在の agents、skills、commands、hooks、install profiles、docs を案内する。
---

# /ecc-guide

Everything Claude Code の会話型マップとしてこのコマンドを使う。README 全体や古いカタログ件数をダンプせず、ユーザーのタスクに合った ECC surface を発見できるようにする。

## 使い方 (Usage)

```text
/ecc-guide
/ecc-guide setup
/ecc-guide skills
/ecc-guide commands
/ecc-guide hooks
/ecc-guide install
/ecc-guide find: <query>
/ecc-guide <feature-or-file-name>
```

## 運用ルール (Operating Rules)

1. チェックアウトが利用可能なら、回答前に現在のリポジトリファイルを読む。
2. ハードコードされた件数より、現在の filesystem/catalog データを優先する。
3. 最初の回答は短くし、具体的な掘り下げパスを提案する。
4. 長いセクションをコピーせず、正規ファイルへリンクする。
5. 存在しない commands、skills、agents、install profiles を捏造しない。

## 確認すべきもの (What To Inspect)

正規マップとして次のファイルを使う:

- `README.md` — install パス、reset/uninstall ガイダンス、高レベルな位置づけ
- `AGENTS.md` — コントリビューターとプロジェクト構造のガイダンス
- `agent.yaml` — エクスポートされた agent と command surface
- `commands/` — 維持されている slash-command shim
- `skills/*/SKILL.md` — 再利用可能な skill workflow
- `agents/*.md` — 委譲 agent の役割
- `hooks/README.md` と `hooks/hooks.json` — hook の挙動
- `manifests/install-*.json` — 選択的 install の module、component、profile
- `scripts/ci/catalog.js --json` — ECC 内実行時のライブカタログ件数

## 応答パターン (Response Patterns)

### 引数なし (No Arguments)

コンパクトなメニューを提示する:

- setup と install
- skill の選び方
- command 互換 shim
- agent と委譲
- hook と安全性
- install のトラブルシューティング
- 特定機能の検索

その後、次に何をしたいか尋ねる。

### トピック検索 (Topic Lookup)

`skills`、`commands`、`hooks`、`install`、`agents` などのトピックでは:

1. 現在の surface を3〜6 bullet で要約する。
2. 正規のディレクトリ/ファイルを指す。
3. 状態を確認できる command を1〜2個提案する。
4. ユーザーが求めない限り網羅的な一覧は避ける。

### 検索モード (Search Mode)

`find: <query>` では:

1. 関連ファイルを `rg` で検索する。
2. surface 別にグループ化: skills、commands、agents、rules、docs、hooks。
3. 最も強い一致をファイルパス付きで先に返す。
4. 各一致に対する次のアクションを推奨する。

### 機能検索 (Feature Lookup)

特定の機能名では:

1. まず正確なパスを確認する。例: `skills/<name>/SKILL.md`、`commands/<name>.md`、`agents/<name>.md`。
2. 完全一致がなければ `rg` で検索する。
3. 機能の説明、使用タイミング、正規ファイルを説明する。
4. 混乱を減らす場合のみ隣接機能に言及する。

## 関連コマンド (Related Commands)

- `/project-init` — 対象プロジェクトへの stack 対応 ECC オンボーディング
- `/harness-audit` — 決定的なリポジトリ readiness スコアリング
- `/skill-health` — skill 品質チェック
- `/skill-create` — ローカル git 履歴から新しい skill を抽出
- `/security-scan` — Claude/OpenCode 設定のセキュリティレビュー
