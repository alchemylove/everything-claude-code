---
paths:
  - "**/*.go"
  - "**/go.mod"
  - "**/go.sum"
---
# Go パターン (Go Patterns)

> 本ファイルは [common/patterns.md](../common/patterns.md) を Go 固有の内容で拡張します。

## Functional Options

```go
type Option func(*Server)

func WithPort(port int) Option {
    return func(s *Server) { s.port = port }
}

func NewServer(opts ...Option) *Server {
    s := &Server{port: 8080}
    for _, opt := range opts {
        opt(s)
    }
    return s
}
```

## 小さな Interface (Small Interfaces)

interface は実装側ではなく、使用側で定義する。

## Dependency Injection

constructor 関数で依存関係を注入する:

```go
func NewUserService(repo UserRepository, logger Logger) *UserService {
    return &UserService{repo: repo, logger: logger}
}
```

## 参照 (Reference)

並行処理、エラーハンドリング、パッケージ構成を含む包括的 Go パターンは skill: `golang-patterns` を参照。
