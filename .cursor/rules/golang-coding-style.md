---
description: "Go coding style: common ルールの拡張"
globs: ["**/*.go", "**/go.mod", "**/go.sum"]
alwaysApply: false
---
# Go Coding Style (Go Coding Style)

> このファイルは common coding style ルールを Go 固有の内容で拡張する。

## フォーマット (Formatting)

- **gofmt** と **goimports** は必須 — スタイル議論なし

## 設計原則 (Design Principles)

- interface を受け入れ、struct を返す
- interface は小さく保つ（1-3 メソッド）

## エラーハンドリング (Error Handling)

常に context 付きで error を wrap:

```go
if err != nil {
    return fmt.Errorf("failed to create user: %w", err)
}
```

## 参照 (Reference)

包括的な Go イディオムとパターンは skill: `golang-patterns` を参照。
