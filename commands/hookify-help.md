---
description: hookify システムのヘルプを取得します。
---

hookify の包括的な documentation を表示します。

## フックシステムの概要 (Hook System Overview)

Hookify は、望ましくない動作を防ぐために、Claude Code の hook system と統合する rule file を作成します。

### イベントタイプ (Event Types)

- `bash`: Bash tool 使用時に trigger し、command pattern に match
- `file`: Write/Edit tool 使用時に trigger し、file path に match
- `stop`: session 終了時に trigger
- `prompt`: user message 送信時に trigger し、input pattern に match
- `all`: すべての event で trigger

### ルールファイル形式 (Rule File Format)

file は `.claude/hookify.{name}.local.md` として保存:

```yaml
---
name: descriptive-name
enabled: true
event: bash|file|stop|prompt|all
action: block|warn
pattern: "regex pattern to match"
---
Message to display when rule triggers.
Supports multiple lines.
```

### コマンド (Commands)

- `/hookify [description]` 新しい rule を作成し、description がない場合は会話を auto-analyze
- `/hookify-list` 設定済み rule を list
- `/hookify-configure` rule の on/off を toggle

### パターンのヒント (Pattern Tips)

- regex syntax を使用
- `bash` の場合、full command string に対して match
- `file` の場合、file path に対して match
- deploy 前に pattern を test
