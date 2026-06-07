---
paths:
  - "**/*.go"
  - "**/go.mod"
  - "**/go.sum"
---
# Go セキュリティ (Go Security)

> 本ファイルは [common/security.md](../common/security.md) を Go 固有の内容で拡張します。

## Secret 管理 (Secret Management)

```go
apiKey := os.Getenv("OPENAI_API_KEY")
if apiKey == "" {
    log.Fatal("OPENAI_API_KEY not configured")
}
```

## セキュリティスキャン (Security Scanning)

- 静的セキュリティ分析に **gosec** を使用する:
  ```bash
  gosec ./...
  ```

## Context とタイムアウト (Context & Timeouts)

タイムアウト制御には常に `context.Context` を使用する:

```go
ctx, cancel := context.WithTimeout(ctx, 5*time.Second)
defer cancel()
```
