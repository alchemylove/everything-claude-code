---
description: "Go testing: common ルールの拡張"
globs: ["**/*.go", "**/go.mod", "**/go.sum"]
alwaysApply: false
---
# Go Testing (Go Testing)

> このファイルは common testing ルールを Go 固有の内容で拡張する。

## Framework

標準の `go test` と **table-driven tests** を使用。

## Race Detection

常に `-race` フラグで実行:

```bash
go test -race ./...
```

## Coverage

```bash
go test -cover ./...
```

## 参照 (Reference)

詳細な Go testing パターンと helper は skill: `golang-testing` を参照。
