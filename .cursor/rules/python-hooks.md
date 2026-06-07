---
description: "Python hooks: common ルールの拡張"
globs: ["**/*.py", "**/*.pyi"]
alwaysApply: false
---
# Python Hooks (Python Hooks)

> このファイルは common hooks ルールを Python 固有の内容で拡張する。

## PostToolUse Hooks

`~/.claude/settings.json` で設定:

- **black/ruff**: 編集後に `.py` ファイルを auto-format
- **mypy/pyright**: `.py` ファイル編集後に型チェックを実行

## 警告 (Warnings)

- 編集ファイルの `print()` 文を警告（代わりに `logging` モジュールを使用）
