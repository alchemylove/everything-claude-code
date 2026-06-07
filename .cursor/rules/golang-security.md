---
description: "Go security: common ルールの拡張"
globs: ["**/*.go", "**/go.mod", "**/go.sum"]
alwaysApply: false
---
# Go Security (Go Security)

> このファイルは common security ルールを Go 固有の内容で拡張する。

## Secret 管理 (Secret Management)

```go
apiKey := os.Getenv("OPENAI_API_KEY")
if apiKey == "" {
    log.Fatal("OPENAI_API_KEY not configured")
}
```

## セキュリティスキャン (Security Scanning)

- static セキュリティ分析に **gosec** を使用:
  ```bash
  gosec ./...
  ```

## Context & Timeouts

timeout 制御には常に `context.Context` を使用:

```go
ctx, cancel := context.WithTimeout(ctx, 5*time.Second)
defer cancel()
```
