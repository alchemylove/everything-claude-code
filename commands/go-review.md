---
description: 慣用的なパターン、並行性の安全性、エラーハンドリング、セキュリティについての包括的な Go コードレビュー。go-reviewer エージェントを呼び出します。
---

# Go コードレビュー (Go Code Review)

このコマンドは、Go 固有の包括的なコードレビューのために **go-reviewer** エージェントを呼び出します。

## このコマンドの機能 (What This Command Does)

1. **Go 変更の特定**: `git diff` で変更された `.go` ファイルを検出
2. **静的解析の実行**: `go vet`、`staticcheck`、`golangci-lint` を実行
3. **セキュリティスキャン**: SQL injection、command injection、race condition をチェック
4. **並行性のレビュー**: goroutine の安全性、channel 使用、mutex パターンを分析
5. **慣用的な Go チェック**: コードが Go の慣習とベストプラクティスに従っていることを確認
6. **レポート生成**: 問題を重要度別に分類

## 使用するタイミング (When to Use)

以下の場合に `/go-review` を使用します:
- Go コードを作成または変更した後
- Go 変更を commit する前
- Go コードを含む pull request のレビュー時
- 新しい Go codebase へのオンボーディング時
- 慣用的な Go パターンの学習時

## レビューカテゴリ (Review Categories)

### CRITICAL（必須修正）(CRITICAL (Must Fix))
- SQL/Command injection の脆弱性
- 同期化なしの race condition
- goroutine leak
- hardcoded credentials
- unsafe pointer 使用
- critical path での ignored errors

### HIGH（修正推奨）(HIGH (Should Fix))
- context を含まない error wrapping の欠落
- error return の代わりに panic
- context が伝播されていない
- deadlock を引き起こす unbuffered channel
- interface not satisfied errors
- mutex による保護の欠落

### MEDIUM（検討）(MEDIUM (Consider))
- 非慣用的なコードパターン
- export 時の godoc comment の欠落
- 非効率的な string concatenation
- 事前割り当てされていない slice
- table-driven test が使用されていない

## 実行される自動チェック (Automated Checks Run)

```bash
# Static analysis
go vet ./...

# Advanced checks (if installed)
staticcheck ./...
golangci-lint run

# Race detection
go build -race ./...

# Security vulnerabilities
govulncheck ./...
```

## 使用例 (Example Usage)

```text
User: /go-review

Agent:
# Go Code Review Report

## Files Reviewed
- internal/handler/user.go (modified)
- internal/service/auth.go (modified)

## Static Analysis Results
✓ go vet: No issues
✓ staticcheck: No issues

## Issues Found

[CRITICAL] Race Condition
File: internal/service/auth.go:45
Issue: Shared map accessed without synchronization
```go
var cache = map[string]*Session{}  // Concurrent access!

func GetSession(id string) *Session {
    return cache[id]  // Race condition
}
```
Fix: Use sync.RWMutex or sync.Map
```go
var (
    cache   = map[string]*Session{}
    cacheMu sync.RWMutex
)

func GetSession(id string) *Session {
    cacheMu.RLock()
    defer cacheMu.RUnlock()
    return cache[id]
}
```

[HIGH] Missing Error Context
File: internal/handler/user.go:28
Issue: Error returned without context
```go
return err  // No context
```
Fix: Wrap with context
```go
return fmt.Errorf("get user %s: %w", userID, err)
```

## Summary
- CRITICAL: 1
- HIGH: 1
- MEDIUM: 0

Recommendation: FAIL: Block merge until CRITICAL issue is fixed
```

## 承認基準 (Approval Criteria)

| Status | Condition |
|--------|-----------|
| PASS: Approve | No CRITICAL or HIGH issues |
| WARNING: Warning | Only MEDIUM issues (merge with caution) |
| FAIL: Block | CRITICAL or HIGH issues found |

## 他のコマンドとの統合 (Integration with Other Commands)

- まず `/go-test` を使用してテストが pass することを確認
- `/go-build` を build error 発生時に使用
- `/go-review` を commit 前に使用
- `/code-review` を Go 固有でない問題に使用

## 関連 (Related)

- Agent: `agents/go-reviewer.md`
- Skills: `skills/golang-patterns/`, `skills/golang-testing/`
