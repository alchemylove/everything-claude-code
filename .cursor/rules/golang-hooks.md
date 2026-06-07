---
description: "Go hooks: common ルールの拡張"
globs: ["**/*.go", "**/go.mod", "**/go.sum"]
alwaysApply: false
---
# Go Hooks (Go Hooks)

> このファイルは common hooks ルールを Go 固有の内容で拡張する。

## PostToolUse Hooks

`~/.claude/settings.json` で設定:

- **gofmt/goimports**: 編集後に `.go` ファイルを auto-format
- **go vet**: `.go` ファイル編集後に static analysis を実行
- **staticcheck**: 変更された package で拡張 static check を実行
