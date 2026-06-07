---
paths:
  - "**/*.py"
  - "**/*.pyi"
---
# Python Hooks

> 本ファイルは [common/hooks.md](../common/hooks.md) を Python 固有の内容で拡張します。

## PostToolUse Hooks

`~/.claude/settings.json` で設定する:

- **black/ruff**: 編集後に `.py` ファイルを自動フォーマット
- **mypy/pyright**: `.py` ファイル編集後に型チェックを実行

## 警告 (Warnings)

- 編集ファイル内の `print()` 文について警告する（代わりに `logging` モジュールを使用）
