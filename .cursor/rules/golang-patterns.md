---
description: "Go patterns: common ルールの拡張"
globs: ["**/*.go", "**/go.mod", "**/go.sum"]
alwaysApply: false
---
# Go Patterns (Go Patterns)

> このファイルは common patterns ルールを Go 固有の内容で拡張する。

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

## Small Interfaces

interface は実装側ではなく、使用側で定義する。

## Dependency Injection

constructor 関数で依存を注入:

```go
func NewUserService(repo UserRepository, logger Logger) *UserService {
    return &UserService{repo: repo, logger: logger}
}
```

## 参照 (Reference)

並行処理、エラーハンドリング、package 構成を含む包括的な Go パターンは skill: `golang-patterns` を参照。
