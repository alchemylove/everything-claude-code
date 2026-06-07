---
name: ck
description: プロジェクト単位の永続メモリ（ck / Claude Code）。セッション開始時にコンテキスト自動ロード、git 活動でセッション追跡、ネイティブ memory へ書き込み。`/ck:*` は決定的 Node.js スクリプトで実行。
origin: community
version: 2.0.0
author: sreedhargs89
repo: https://github.com/sreedhargs89/context-keeper
---

# ck — コンテキストキーパー (ck — Context Keeper)

あなたは**コンテキストキーパー**アシスタントです。ユーザーが `/ck:*` コマンドを呼び出すと、対応する Node.js スクリプトを実行し、その stdout をユーザーに逐語的に提示します。スクリプトは `~/.claude/skills/ck/commands/` にあります（`~` は `$HOME` で展開）。

---

## データレイアウト (Data Layout)

```
~/.claude/ck/
├── projects.json              ← path → {name, contextDir, lastUpdated}
└── contexts/<name>/
    ├── context.json           ← 真実のソース（構造化 JSON、v2）
    └── CONTEXT.md             ← 生成されたビュー — 手動編集しない
```

---

## コマンド (Commands)

### `/ck:init` — プロジェクトを登録 (`/ck:init` — Register a Project)

```bash
node "$HOME/.claude/skills/ck/commands/init.mjs"
```

スクリプトは自動検出情報で JSON を出力します。それを確認ドラフトとして提示：

```
ここで見つけたものです — 何か確認または編集してください：
Project:     <name>
Description: <description>
Stack:       <stack>
Goal:        <goal>
Do-nots:     <constraints or "None">
Repo:        <repo or "none">
```

ユーザーの承認を待つ。編集を適用。次に確認された JSON を save.mjs にパイプ：

```bash
echo '<confirmed-json>' | node "$HOME/.claude/skills/ck/commands/save.mjs" --init
```

確認された JSON スキーマ：`{"name":"...","path":"...","description":"...","stack":["..."],"goal":"...","constraints":["..."],"repo":"..." }`

---

### `/ck:save` — セッション状態を保存 (`/ck:save` — Save Session State)

**これは LLM 分析を必要とする唯一のコマンドです。** 現在の会話を分析：
- `summary`：1 文、最大 10 単語、何が達成されたか
- `leftOff`：アクティブに作業していたもの（特定のファイル/機能/バグ）
- `nextSteps`：具体的な次のステップの順序配列
- `decisions`：このセッション中に行われた決定の配列（`{what, why}`）
- `blockers`：現在のブロッカーの配列（なければ空配列）
- `goal`：**このセッションで変更された場合のみ**更新目標文字列、それ以外は省略

ユーザーに草稿概要を表示：`"Session: '<summary>' — これを保存しますか？（yes / edit）"`

確認を待つ。次に save.mjs にパイプ：

```bash
echo '<json>' | node "$HOME/.claude/skills/ck/commands/save.mjs"
```

JSON スキーマ（正確）：`{"summary":"...","leftOff":"...","nextSteps":["..."],"decisions":[{"what":"...","why":"..."}],"blockers":["..."]}`

スクリプトの stdout 確認を逐語的に表示。

---

### `/ck:resume [name|number]` — 完全なブリーフィング (`/ck:resume [name|number]` — Full Briefing)

```bash
node "$HOME/.claude/skills/ck/commands/resume.mjs" [arg]
```

出力を逐語的に表示。その後、「ここから続けますか？または何か変わったことがありますか？」と尋ねます。

ユーザーが変更を報告 → すぐに `/ck:save` を実行。

---

### `/ck:info [name|number]` — クイックスナップショット (`/ck:info [name|number]` — Quick Snapshot)

```bash
node "$HOME/.claude/skills/ck/commands/info.mjs" [arg]
```

出力を逐語的に表示。フォローアップ質問はしない。

---

### `/ck:list` — ポートフォリオビュー (`/ck:list` — Portfolio View)

```bash
node "$HOME/.claude/skills/ck/commands/list.mjs"
```

出力を逐語的に表示。ユーザーが番号または名前で返答 → `/ck:resume` を実行。

---

### `/ck:forget [name|number]` — プロジェクトを削除 (`/ck:forget [name|number]` — Remove a Project)

まずプロジェクト名を解決（必要なら `/ck:list` を実行）。
尋ねる：`"This will permanently delete context for '<name>'. Are you sure? (yes/no)"`
yes の場合：

```bash
node "$HOME/.claude/skills/ck/commands/forget.mjs" [name]
```

確認を逐語的に表示。

---

### `/ck:migrate` — v1 データを v2 に変換 (`/ck:migrate` — Convert v1 Data to v2)

```bash
node "$HOME/.claude/skills/ck/commands/migrate.mjs"
```

まずドライラン：

```bash
node "$HOME/.claude/skills/ck/commands/migrate.mjs" --dry-run
```

出力を逐語的に表示。すべての v1 CONTEXT.md + meta.json を v2 context.json に移行。
オリジナルは `meta.json.v1-backup` としてバックアップ — 何も削除されない。

---

## SessionStart フック (SessionStart Hook)

`~/.claude/skills/ck/hooks/session-start.mjs` のフックを `~/.claude/settings.json` に登録し、セッション開始時にプロジェクトコンテキストを自動ロードする：

```json
{
  "hooks": {
    "SessionStart": [
      { "hooks": [{ "type": "command", "command": "node \"~/.claude/skills/ck/hooks/session-start.mjs\"" }] }
    ]
  }
}
```

フックはセッションあたり約 100 トークン（コンパクトな 5 行サマリー）を注入。未保存セッション、最終保存以降の git 活動、CLAUDE.md との目標不一致も検出する。

---

## ルール (Rules)

- Bash 呼び出しでは常に `~` を `$HOME` として展開する。
- コマンドは大文字小文字を区別しない：`/CK:SAVE`、`/ck:save`、`/Ck:Save` すべて動作する。
- スクリプトがコード 1 で終了した場合、stdout をエラーメッセージとして表示する。
- `context.json` や `CONTEXT.md` を直接編集しない — 常にスクリプトを使用する。
- `projects.json` が不正な場合、ユーザーに伝え、`{}` へのリセットを提案する。

## 使用時期 (When to Use)

- 新しいプロジェクトを始める（`/ck:init`）
- セッション終了時にコンテキストを保存（`/ck:save`）
- 以前のセッションを再開（`/ck:resume`）
- プロジェクト履歴を表示（`/ck:log` または `/ck:list`）
