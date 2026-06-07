---
description: Jira チケットを取得し、要件を分析し、ステータスを更新し、コメントを追加する。jira-integration skill と MCP または REST API を使用する。
---

# Jira コマンド (Jira Command)

ワークフローから直接 Jira チケットと対話する — チケットの取得、要件の分析、コメントの追加、ステータスの遷移。

## 使い方 (Usage)

```
/jira get <TICKET-KEY>          # チケットを取得して分析
/jira comment <TICKET-KEY>      # 進捗コメントを追加
/jira transition <TICKET-KEY>   # チケットステータスを変更
/jira search <JQL>              # JQL でイシューを検索
```

## このコマンドの動作 (What This Command Does)

1. **取得と分析 (Get & Analyze)** — Jira チケットを取得し、要件、受け入れ基準、テストシナリオ、依存関係を抽出する
2. **コメント (Comment)** — チケットに構造化された進捗更新を追加する
3. **遷移 (Transition)** — ワークフローステート間でチケットを移動する（To Do → In Progress → Done）
4. **検索 (Search)** — JQL クエリでイシューを検索する

## 動作方法 (How It Works)

### `/jira get <TICKET-KEY>`

1. Jira からチケットを取得する（MCP `jira_get_issue` または REST API 経由）
2. すべてのフィールドを抽出する: summary、description、acceptance criteria、priority、labels、linked issues
3. 必要に応じてコメントを取得し、追加コンテキストを得る
4. 構造化された分析を出力する:

```
Ticket: PROJ-1234
Summary: [title]
Status: [status]
Priority: [priority]
Type: [Story/Bug/Task]

Requirements:
1. [extracted requirement]
2. [extracted requirement]

Acceptance Criteria:
- [ ] [criterion from ticket]

Test Scenarios:
- Happy Path: [description]
- Error Case: [description]
- Edge Case: [description]

Dependencies:
- [linked issues, APIs, services]

Recommended Next Steps:
- /plan to create implementation plan
- `tdd-workflow` skill to implement with tests first
```

### `/jira comment <TICKET-KEY>`

1. 現在のセッション進捗を要約する（何をビルド、テスト、コミットしたか）
2. 構造化されたコメントとしてフォーマットする
3. Jira チケットに投稿する

### `/jira transition <TICKET-KEY>`

1. チケットの利用可能な遷移を取得する
2. ユーザーにオプションを表示する
3. 選択された遷移を実行する

### `/jira search <JQL>`

1. Jira に対して JQL クエリを実行する
2. マッチするイシューのサマリーテーブルを返す

## 前提条件 (Prerequisites)

このコマンドには Jira 認証情報が必要です。いずれかを選択してください:

**オプション A — MCP Server（推奨）:**
`mcpServers` 設定に `jira` を追加する（テンプレートは `mcp-configs/mcp-servers.json` を参照）。

**オプション B — 環境変数:**
```bash
export JIRA_URL="https://yourorg.atlassian.net"
export JIRA_EMAIL="your.email@example.com"
export JIRA_API_TOKEN="your-api-token"
```

認証情報がない場合は停止し、ユーザーにセットアップを案内してください。

## 他コマンドとの統合 (Integration with Other Commands)

チケット分析後:
- `/plan` で要件から実装計画を作成する
- `tdd-workflow` skill で test-driven development により実装する
- 実装後に `/code-review` を使用する
- `/jira comment` で進捗をチケットに投稿する
- 作業完了時に `/jira transition` でチケットを移動する

## 関連 (Related)

- **Skill:** `skills/jira-integration/`
- **MCP config:** `mcp-configs/mcp-servers.json` → `jira`
