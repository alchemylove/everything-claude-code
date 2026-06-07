---
paths:
  - "**/*.go"
  - "**/go.mod"
  - "**/go.sum"
---
# Go Hooks

> 本ファイルは [common/hooks.md](../common/hooks.md) を Go 固有の内容で拡張します。

## PostToolUse Hooks

`~/.claude/settings.json` で設定する:

- **gofmt/goimports**: 編集後に `.go` ファイルを自動フォーマット
- **go vet**: `.go` ファイル編集後に静的解析を実行
- **staticcheck**: 変更されたパッケージで拡張静的チェックを実行
