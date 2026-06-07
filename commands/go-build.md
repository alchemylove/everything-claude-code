---
description: Go ビルドエラー、go vet 警告、linter 問題を段階的に修正します。最小限の外科的修正のために go-build-resolver エージェントを呼び出します。
---

# Go ビルドと修正 (Go Build and Fix)

このコマンドは **go-build-resolver** エージェントを呼び出し、最小限の変更で Go ビルドエラーを段階的に修正します。

## このコマンドの機能 (What This Command Does)

1. **診断の実行**: `go build`、`go vet`、`staticcheck` を実行
2. **エラーの解析**: ファイル別にグループ化し、重要度順にソート
3. **段階的修正**: 一度に 1 つのエラーを修正
4. **各修正の検証**: 各変更後にビルドを再実行
5. **サマリーのレポート**: 修正されたものと残っているものを表示

## 使用するタイミング (When to Use)

以下の場合に `/go-build` を使用します:
- `go build ./...` がエラーで失敗する場合
- `go vet ./...` が問題を報告する場合
- `golangci-lint run` が警告を表示する場合
- module 依存関係が壊れている場合
- ビルドを壊す変更を pull した後

## 実行される診断コマンド (Diagnostic Commands Run)

```bash
# Primary build check
go build ./...

# Static analysis
go vet ./...

# Extended linting (if available)
staticcheck ./...
golangci-lint run

# Module issues
go mod verify
go mod tidy -v
```

## セッション例 (Example Session)

```text
User: /go-build

Agent:
# Go Build Resolution

## Initial Diagnostics

```bash
$ go build ./...
internal/service/user.go:25:15: undefined: UserRepository
internal/handler/api.go:42:9: cannot use x (type string) as type int
internal/handler/api.go:58:2: missing return at end of function
```

Errors found: 3

## Fix 1: Undefined Identifier

File: internal/service/user.go:25
Error: undefined: UserRepository
Cause: Missing import

```go
// Added import
import "project/internal/repository"

// Changed
var repo UserRepository
// To
var repo repository.UserRepository
```

```bash
$ go build ./...
# 2 errors remaining
```

## Fix 2: Type Mismatch

File: internal/handler/api.go:42
Error: cannot use x (type string) as type int

```go
// Changed
count := params.Get("count")
// To
countStr := params.Get("count")
count, _ := strconv.Atoi(countStr)
```

```bash
$ go build ./...
# 1 error remaining
```

## Fix 3: Missing Return

File: internal/handler/api.go:58
Error: missing return at end of function

```go
func GetUser(id string) (*User, error) {
    if id == "" {
        return nil, ErrInvalidID
    }
    user := findUser(id)
    // Added missing return
    return user, nil
}
```

```bash
$ go build ./...
# Build successful!
```

## Final Verification

```bash
$ go vet ./...
# No issues

$ go test ./...
ok      project/internal/service   0.015s
ok      project/internal/handler   0.023s
```

## Summary

| Metric | Count |
|--------|-------|
| Build errors fixed | 3 |
| Vet warnings fixed | 0 |
| Files modified | 2 |
| Remaining issues | 0 |

Build Status: PASS: SUCCESS
```

## 修正される一般的なエラー (Common Errors Fixed)

| Error | Typical Fix |
|-------|-------------|
| `undefined: X` | Add import or fix typo |
| `cannot use X as Y` | Type conversion or fix assignment |
| `missing return` | Add return statement |
| `X does not implement Y` | Add missing method |
| `import cycle` | Restructure packages |
| `declared but not used` | Remove or use variable |
| `cannot find package` | `go get` or `go mod tidy` |

## 修正戦略 (Fix Strategy)

1. **Build errors first** - Code must compile
2. **Vet warnings second** - Fix suspicious constructs
3. **Lint warnings third** - Style and best practices
4. **One fix at a time** - Verify each change
5. **Minimal changes** - Don't refactor, just fix

## 停止条件 (Stop Conditions)

以下の場合、エージェントは停止してレポートします:
- 同じエラーが 3 回の試行後も持続
- 修正がさらなるエラーを引き起こす
- アーキテクチャの変更が必要
- 外部依存関係が欠落

## 関連コマンド (Related Commands)

- `/go-test` - ビルド成功後にテストを実行
- `/go-review` - コード品質をレビュー
- `verification-loop` skill - 完全な verification loop

## 関連 (Related)

- Agent: `agents/go-build-resolver.md`
- Skill: `skills/golang-patterns/`
