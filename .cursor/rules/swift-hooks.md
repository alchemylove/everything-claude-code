---
description: "Swift hooks: common ルールの拡張"
globs: ["**/*.swift", "**/Package.swift"]
alwaysApply: false
---
# Swift Hooks (Swift Hooks)

> このファイルは common hooks ルールを Swift 固有の内容で拡張する。

## PostToolUse Hooks

`~/.claude/settings.json` で設定:

- **SwiftFormat**: 編集後に `.swift` ファイルを auto-format
- **SwiftLint**: `.swift` ファイル編集後に lint check を実行
- **swift build**: 編集後に変更された package を型チェック

## 警告 (Warning)

`print()` 文をフラグ — 本番コードでは `os.Logger` または structured logging を使用。
