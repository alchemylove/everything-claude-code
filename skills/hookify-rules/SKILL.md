---
name: hookify-rules
description: This skill should be used when the user asks to create a hookify rule, write a hook rule, configure hookify, add a hookify rule, or needs guidance on hookify rule syntax and patterns.
---

# Hookify ルールの記述 (Writing Hookify Rules)

## 概要 (Overview)

Hookify ルールは、監視パターンとマッチ時に表示するメッセージを定義する YAML frontmatter 付き Markdown ファイル。ルールは `.claude/hookify.{rule-name}.local.md` に保存される。

## ルールファイル形式 (Rule File Format)

### 基本構造 (Basic Structure)

```markdown
---
name: rule-identifier
enabled: true
event: bash|file|stop|prompt|all
pattern: regex-pattern-here
---

Message to show Claude when this rule triggers.
Can include markdown formatting, warnings, suggestions, etc.
```

### Frontmatter フィールド (Frontmatter Fields)

| Field | Required | Values | Description |
|-------|----------|--------|-------------|
| name | Yes | kebab-case string | Unique identifier (verb-first: warn-*, block-*, require-*) |
| enabled | Yes | true/false | Toggle without deleting |
| event | Yes | bash/file/stop/prompt/all | Which hook event triggers this |
| action | No | warn/block | warn (default) shows message; block prevents operation |
| pattern | Yes* | regex string | Pattern to match (*or use conditions for complex rules) |

### 高度な形式 (Advanced Format)

```markdown
---
name: warn-env-api-keys
enabled: true
event: file
conditions:
  - field: file_path
    operator: regex_match
    pattern: \.env$
  - field: new_text
    operator: contains
    pattern: API_KEY
---

You're adding an API key to a .env file. Ensure this file is in .gitignore!
```

**イベント別 condition フィールド:**
- bash: `command`
- file: `file_path`, `new_text`, `old_text`, `content`
- prompt: `user_prompt`

**演算子:** `regex_match`, `contains`, `equals`, `not_contains`, `starts_with`, `ends_with`

すべての condition が一致したときのみルール発火。

## イベントタイプガイド (Event Type Guide)

### bash イベント (bash Events)
Bash コマンドパターンにマッチ:
- 危険コマンド: `rm\s+-rf`, `dd\s+if=`, `mkfs`
- 権限昇格: `sudo\s+`, `su\s+`
- 権限問題: `chmod\s+777`

### file イベント (file Events)
Edit/Write/MultiEdit 操作にマッチ:
- デバッグコード: `console\.log\(`, `debugger`
- セキュリティリスク: `eval\(`, `innerHTML\s*=`
- 機微ファイル: `\.env$`, `credentials`, `\.pem$`

### stop イベント (stop Events)
完了チェックとリマインダー。パターン `.*` は常にマッチ。

### prompt イベント (prompt Events)
ワークフロー強制のためユーザープロンプト内容にマッチ。

## パターン記述のヒント (Pattern Writing Tips)

### 正規表現の基礎 (Regex Basics)
- 特殊文字をエスケープ: `.` → `\.`, `(` → `\(`
- `\s` 空白, `\d` 数字, `\w` 単語文字
- `+` 1 回以上, `*` 0 回以上, `?` 任意
- `|` OR 演算子

### よくある落とし穴 (Common Pitfalls)
- **広すぎ**: `log` は "login", "dialog" にマッチ — `console\.log\(` を使う
- **狭すぎ**: `rm -rf /tmp` — `rm\s+-rf` を使う
- **YAML エスケープ**: パターンは非引用; 引用文字列では `\\s` が必要

### テスト (Testing)
```bash
python3 -c "import re; print(re.search(r'your_pattern', 'test text'))"
```

## ファイル構成 (File Organization)

- **場所**: プロジェクトルートの `.claude/` ディレクトリ
- **命名**: `.claude/hookify.{descriptive-name}.local.md`
- **Gitignore**: `.claude/*.local.md` を `.gitignore` に追加

## コマンド (Commands)

- `/hookify [description]` - 新規ルール作成（引数なしで会話を自動分析）
- `/hookify-list` - 表形式ですべてのルール表示
- `/hookify-configure` - ルールのオン/オフを対話的に切替
- `/hookify-help` - 完全ドキュメント

## クイックリファレンス (Quick Reference)

最小限のルール:
```markdown
---
name: my-rule
enabled: true
event: bash
pattern: dangerous_command
---
Warning message here
```
