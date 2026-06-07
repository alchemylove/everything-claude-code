---
paths:
  - "**/*.go"
  - "**/go.mod"
  - "**/go.sum"
---
# Go コーディングスタイル (Go Coding Style)

> 本ファイルは [common/coding-style.md](../common/coding-style.md) を Go 固有の内容で拡張します。

## フォーマット (Formatting)

- **gofmt** と **goimports** は必須 — スタイル議論は不要

## 設計原則 (Design Principles)

- interface を受け取り、struct を返す
- interface は小さく保つ（1–3 メソッド）

## エラーハンドリング (Error Handling)

常にコンテキスト付きでエラーを wrap する:

```go
if err != nil {
    return fmt.Errorf("failed to create user: %w", err)
}
```

## 参照 (Reference)

包括的な Go イディオムとパターンは skill: `golang-patterns` を参照。
