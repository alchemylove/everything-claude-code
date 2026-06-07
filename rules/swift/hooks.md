---
paths:
  - "**/*.swift"
  - "**/Package.swift"
---
# Swift フック (Swift Hooks)

> このファイルは [common/hooks.md](../common/hooks.md) を拡張し、Swift 固有の内容を追加する。

## PostToolUse フック (PostToolUse Hooks)

`~/.claude/settings.json` で設定:

- **SwiftFormat**: 編集後に `.swift` ファイルを自動フォーマット
- **SwiftLint**: `.swift` ファイル編集後に lint チェックを実行
- **swift build**: 編集後に変更された package の型チェックを実行

## 警告 (Warning)

`print()` 文をフラグする — 本番コードでは `os.Logger` または structured logging を使用する。
