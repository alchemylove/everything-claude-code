---
description: Go の TDD ワークフローを適用します。table-driven test を最初に記述し、その後実装します。`go test -cover` で 80%+ coverage を確認します。
---

# Go TDD コマンド (Go TDD Command)

このコマンドは、Go のイディオム的なテストパターンを使用した test-driven development 手法を適用します。

## このコマンドの機能 (What This Command Does)

1. **型/インターフェースの定義**: function signature を最初に scaffold
2. **Table-Driven Test の作成**: 包括的な test case を作成（RED）
3. **テストの実行**: テストが正しい理由で fail することを確認
4. **コードの実装**: テストを pass するための最小限のコードを記述（GREEN）
5. **リファクタリング**: テストを green に保ちながら改善
6. **Coverage の確認**: 80%+ coverage を保証

## 使用するタイミング (When to Use)

`/go-test` は次の場合に使用します:
- 新しい Go function の実装時
- 既存コードへの test coverage 追加時
- bug 修正時（失敗する test を最初に作成）
- critical business logic の構築時
- Go での TDD ワークフローの学習時

## TDD サイクル (TDD Cycle)

```
RED     → Write failing table-driven test
GREEN   → Implement minimal code to pass
REFACTOR → Improve code, tests stay green
REPEAT  → Next test case
```

## セッション例 (Example Session)

````
User: /go-test I need a function to validate email addresses

Agent:
# TDD Session: Email Validator

## Step 1: Define Interface

```go
// validator/email.go
package validator

// ValidateEmail checks if the given string is a valid email address.
// Returns nil if valid, error describing the issue otherwise.
func ValidateEmail(email string) error {
    panic("not implemented")
}
```

## Step 2: Write Table-Driven Tests (RED)

```go
// validator/email_test.go
package validator

import (
    "testing"
)

func TestValidateEmail(t *testing.T) {
    tests := []struct {
        name    string
        email   string
        wantErr bool
    }{
        // Valid emails
        {"simple email", "user@example.com", false},
        {"with subdomain", "user@mail.example.com", false},
        {"with plus", "user+tag@example.com", false},
        {"with dots", "first.last@example.com", false},

        // Invalid emails
        {"empty string", "", true},
        {"no at sign", "userexample.com", true},
        {"no domain", "user@", true},
        {"no local part", "@example.com", true},
        {"double at", "user@@example.com", true},
        {"spaces", "user @example.com", true},
        {"no tld", "user@example", true},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            err := ValidateEmail(tt.email)
            if tt.wantErr && err == nil {
                t.Errorf("ValidateEmail(%q) = nil; want error", tt.email)
            }
            if !tt.wantErr && err != nil {
                t.Errorf("ValidateEmail(%q) = %v; want nil", tt.email, err)
            }
        })
    }
}
```

## Step 3: Run Tests - Verify FAIL

```bash
$ go test ./validator/...

--- FAIL: TestValidateEmail (0.00s)
    --- FAIL: TestValidateEmail/simple_email (0.00s)
        panic: not implemented

FAIL
```

✓ Tests fail as expected (panic).

## Step 4: Implement Minimal Code (GREEN)

```go
// validator/email.go
package validator

import (
    "errors"
    "regexp"
)

var emailRegex = regexp.MustCompile(`^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$`)

var (
    ErrEmailEmpty   = errors.New("email cannot be empty")
    ErrEmailInvalid = errors.New("email format is invalid")
)

func ValidateEmail(email string) error {
    if email == "" {
        return ErrEmailEmpty
    }
    if !emailRegex.MatchString(email) {
        return ErrEmailInvalid
    }
    return nil
}
```

## Step 5: Run Tests - Verify PASS

```bash
$ go test ./validator/...

PASS
ok      project/validator    0.003s
```

✓ All tests passing!

## Step 6: Check Coverage

```bash
$ go test -cover ./validator/...

PASS
coverage: 100.0% of statements
ok      project/validator    0.003s
```

✓ Coverage: 100%

## TDD Complete!
````

## テストパターン (Test Patterns)

### Table-Driven Test (Table-Driven Tests)
```go
tests := []struct {
    name     string
    input    InputType
    want     OutputType
    wantErr  bool
}{
    {"case 1", input1, want1, false},
    {"case 2", input2, want2, true},
}

for _, tt := range tests {
    t.Run(tt.name, func(t *testing.T) {
        got, err := Function(tt.input)
        // assertions
    })
}
```

### 並列テスト (Parallel Tests)
```go
for _, tt := range tests {
    tt := tt // Capture
    t.Run(tt.name, func(t *testing.T) {
        t.Parallel()
        // test body
    })
}
```

### テストヘルパー (Test Helpers)
```go
func setupTestDB(t *testing.T) *sql.DB {
    t.Helper()
    db := createDB()
    t.Cleanup(func() { db.Close() })
    return db
}
```

## Coverage コマンド (Coverage Commands)

```bash
# Basic coverage
go test -cover ./...

# Coverage profile
go test -coverprofile=coverage.out ./...

# View in browser
go tool cover -html=coverage.out

# Coverage by function
go tool cover -func=coverage.out

# With race detection
go test -race -cover ./...
```

## Coverage 目標 (Coverage Targets)

| Code Type | Target |
|-----------|--------|
| Critical business logic | 100% |
| Public APIs | 90%+ |
| General code | 80%+ |
| Generated code | Exclude |

## TDD ベストプラクティス (TDD Best Practices)

**DO:**
- Write test FIRST, before any implementation
- Run tests after each change
- Use table-driven tests for comprehensive coverage
- Test behavior, not implementation details
- Include edge cases (empty, nil, max values)

**DON'T:**
- Write implementation before tests
- Skip the RED phase
- Test private functions directly
- Use `time.Sleep` in tests
- Ignore flaky tests

## 関連コマンド (Related Commands)

- `/go-build` - build error の修正
- `/go-review` - 実装後のコードレビュー
- `verification-loop` skill - 完全な verification loop の実行

## 関連 (Related)

- Skill: `skills/golang-testing/`
- Skill: `skills/tdd-workflow/`
