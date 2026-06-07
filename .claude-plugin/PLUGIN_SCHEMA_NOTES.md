# プラグインマニフェストスキーマノート (Plugin Manifest Schema Notes)

このドキュメントは、Claude Code プラグインマニフェストバリデーターが**文書化されていないが強制する制約**をまとめたものです。

これらのルールは、実際のインストール失敗、バリデーターの挙動、既知の動作するプラグインとの比較に基づいています。サイレントな破損と繰り返しの回帰を防ぐために存在します。

`.claude-plugin/plugin.json` を編集する場合は、まずこれを読んでください。

---

## サマリー（まずここを読む）(Summary (Read This First))

Claude プラグインマニフェストバリデーターは**厳格で意見が強い**です。公開スキーマリファレンスに完全には記載されていないルールを適用します。

最も一般的な失敗モードは次のとおりです：

> マニフェストは妥当に見えるが、バリデーターが `agents: Invalid input` のような曖昧なエラーで拒否する

このドキュメントがその理由を説明します。

---

## 必須フィールド (Required Fields)

### `version`（必須）(version (MANDATORY))

`version` フィールドは、一部の例では省略されていても、バリデーターによって必須です。

欠落していると、マーケットプレイスインストールや CLI 検証時にインストールが失敗することがあります。

例：

```json
{
  "version": "1.1.0"
}
```

---

## フィールド形状ルール (Field Shape Rules)

次のフィールドは**常に配列**である必要があります：

* `commands`
* `skills`
* `hooks`（存在する場合）

エントリが 1 つだけでも、**文字列は受け付けられません**。

これはすべてのコンポーネントパスフィールドに一貫して適用されます。

---

## `agents` フィールド: 追加しないこと (The `agents` Field: DO NOT ADD)

> WARNING: **重要:** `plugin.json` に `"agents"` フィールドを追加しないでください。Claude Code プラグインバリデーターはそれを完全に拒否します。

### なぜ重要か (Why This Matters)

`agents` フィールドは Claude Code プラグインマニフェストスキームの一部ではありません。文字列パス、パス配列、ディレクトリ配列のいずれの形式でも検証エラーになります：

```
agents: Invalid input
```

`agents/` 配下のエージェント `.md` ファイルは（hooks と同様に）規約により自動検出されます。manifest に宣言する必要はありません。

### 経緯 (History)

以前このリポジトリは `plugin.json` にエージェントをファイルパス配列として明示列挙していました。リポジトリ自身のスキーマでは通りましたが、フィールドを認識しない Claude Code の実バリデーターでは失敗しました。#1459 で削除されました。

---

## パス解決ルール (Path Resolution Rules)

### コマンドとスキル (Commands and Skills)

* `commands` と `skills` は**配列でラップされた場合のみ**ディレクトリパスを受け付けます
* 明示的なファイルパスが最も安全で将来性があります

---

## バリデーター挙動ノート (Validator Behavior Notes)

* `claude plugin validate` は一部のマーケットプレイスプレビューより厳格です
* ローカルでは通っても、パスが曖昧だとインストール時に失敗することがあります
* エラーはしばしば汎用的（`Invalid input`）で根本原因を示しません
* クロスプラットフォームインストール（特に Windows）はパス前提に寛容ではありません

バリデーターは敵対的でリテラルであると想定してください。

---

## `hooks` フィールド: 追加しないこと (The `hooks` Field: DO NOT ADD)

> WARNING: **重要:** `plugin.json` に `"hooks"` フィールドを追加しないでください。回帰テストで強制されています。

### なぜ重要か (Why This Matters)

Claude Code v2.1+ は、インストール済みプラグインから `hooks/hooks.json` を**規約により自動ロード**します。`plugin.json` でも宣言すると次のエラーになります：

```
Duplicate hooks file detected: ./hooks/hooks.json resolves to already-loaded file.
The standard hooks/hooks.json is loaded automatically, so manifest.hooks should
only reference additional hook files.
```

### 行ったり来たりの経緯 (The Flip-Flop History)

このリポジトリでは繰り返し修正/リバートのサイクルがありました：

| コミット | アクション | きっかけ |
|--------|--------|---------|
| `22ad036` | hooks を追加 | 「フックが読み込まれない」報告 |
| `a7bc5f2` | hooks を削除 | 「重複フックエラー」報告 (#52) |
| `779085e` | hooks を追加 | 「エージェントが読み込まれない」報告 (#88) |
| `e3a1306` | hooks を削除 | 「重複フックエラー」報告 (#103) |

**根本原因:** Claude Code CLI のバージョン間で挙動が変わりました：
- v2.1 以前: 明示的な `hooks` 宣言が必要
- v2.1 以降: 規約で自動ロード、重複でエラー

### 現在のルール（テストで強制）(Current Rule (Enforced by Test))

`tests/hooks/hooks.test.js` の `plugin.json does NOT have explicit hooks declaration` テストが再導入を防ぎます。

**追加のフックファイル**（`hooks/hooks.json` 以外）を追加する場合は宣言できます。ただし標準の `hooks/hooks.json` は宣言してはいけません。

---

## `mcpServers` フィールド: 空のオプトアウトを維持 (The `mcpServers` Field: Keep the Empty Opt-Out)

ECC は Codex プラグインインストールと手動 MCP セットアップのためにリポジトリルートに `.mcp.json` を保持しています。Claude Code もプラグインルートの `.mcp.json` を規約で自動検出するため、同じ MCP サーバーが Claude プラグインインストールに同梱される可能性があります。Claude プラグイン slug は意図的に短く（`ecc`）していますが、レガシーインストールと厳格なプロバイダーゲートウェイでは、長いプラグイン識別子から生成された名前で失敗したため、このオプトアウトは依然として必要です。

`.claude-plugin/plugin.json` に次を維持してください：

```json
{
  "mcpServers": {}
}
```

この明示的な空オブジェクトにより、Claude プラグインインストールが ECC のルート MCP 定義を自動ロードするのを防ぎます。オプトアウトがないと、厳格な OpenAI 互換ゲートウェイが `mcp__plugin_everything-claude-code_github__create_pull_request_review` のようなプラグイン MCP ツール名を 64 文字超過で拒否することがあります。

同梱 MCP サーバーを使いたいユーザーは、`.mcp.json` または `mcp-configs/mcp-servers.json` から手動設定してください。

---

## 既知のアンチパターン (Known Anti-Patterns)

正しく見えて却下される例：

* 配列の代わりに文字列値
* **`"agents"` をいかなる形でも追加** — 認識されない manifest フィールドで `Invalid input` の原因
* `version` の欠落
* 推論パスへの依存
* マーケットプレイスの挙動がローカル検証と一致すると仮定
* **`"hooks": "./hooks/hooks.json"` の追加** — 規約で自動ロードされ、重複エラーの原因
* `"mcpServers": {}` の削除 — Claude プラグインインストールでルート `.mcp.json` の自動検出を再有効化し、過長の MCP ツール名を生む可能性

賢さより明示性を選んでください。

---

## 最小の既知良好例 (Minimal Known-Good Example)

```json
{
  "version": "1.1.0",
  "commands": ["./commands/"],
  "skills": ["./skills/"]
}
```

この構造は Claude プラグインバリデーターに対して検証済みです。

**重要:** `"hooks"` も `"agents"` も**ありません**。どちらも規約で自動ロードされます。明示的に追加するとエラーになります。

---

## コントリビューター向け推奨 (Recommendation for Contributors)

`plugin.json` に触れる変更を提出する前に：

1. すべてのコンポーネントフィールドが配列であることを確認
2. `version` を含める
3. `agents` や `hooks` フィールドを追加しない（どちらも規約で自動ロード）
4. Claude プラグイン MCP 同梱挙動を意図的に変えない限り `"mcpServers": {}` を維持
5. 実行：

```bash
claude plugin validate .claude-plugin/plugin.json
```

迷ったら、便利さより冗長さを選んでください。

---

## このファイルが存在する理由 (Why This File Exists)

このリポジトリは広くフォークされ、リファレンス実装として使われています。

バリデーターの癖をここに文書化することで：

* 繰り返しの issue を防ぐ
* コントリビューターのフラストレーションを減らす
* エコシステムの進化に伴いプラグインの安定性を保つ

バリデーターが変わったら、まずこのドキュメントを更新してください。
