---
paths:
  - "**/*.go"
  - "**/go.mod"
  - "**/go.sum"
---
# Go テスト (Go Testing)

> 本ファイルは [common/testing.md](../common/testing.md) を Go 固有の内容で拡張します。

## フレームワーク (Framework)

標準の `go test` と **table-driven tests** を使用する。

## Race 検出 (Race Detection)

常に `-race` フラグ付きで実行する:

```bash
go test -race ./...
```

## カバレッジ (Coverage)

```bash
go test -cover ./...
```

## 参照 (Reference)

詳細な Go テストパターンとヘルパーは skill: `golang-testing` を参照。
