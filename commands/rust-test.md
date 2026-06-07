---
description: Rust 向けに TDD workflow を強制する。テストを先に書き、その後実装する。cargo-llvm-cov で 80%+ coverage を検証する。
---

# Rust TDD コマンド (Rust TDD Command)

このコマンドは `#[test]`、rstest、proptest、mockall を用いた Rust の test-driven development を強制する。

## このコマンドの内容 (What This Command Does)

1. **型/Trait の定義**: `todo!()` 付きの関数シグネチャを scaffold
2. **テストの記述**: 包括的な test module を作成（RED）
3. **テストの実行**: 正しい理由で失敗することを確認
4. **コードの実装**: 通過に足る最小コード（GREEN）
5. **リファクタ**: テストを green のまま改善
6. **Coverage の確認**: cargo-llvm-cov で 80%+ を確保

## 使用タイミング (When to Use)

`/rust-test` を使う場面:
- 新しい Rust 関数、メソッド、trait を実装するとき
- 既存 Rust コードに test coverage を追加するとき
- bug 修正（先に失敗テストを書く）
- 重要なビジネスロジックの構築
- Rust での TDD workflow の学習

## TDD サイクル (TDD Cycle)

```
RED     -> Write failing test first
GREEN   -> Implement minimal code to pass
REFACTOR -> Improve code, tests stay green
REPEAT  -> Next test case
```

## セッション例 (Example Session)

````text
User: /rust-test I need a function to validate user registration

Agent:
# TDD Session: Registration Validator

## Step 1: Define Interface

```rust
// src/validator.rs
pub struct RegistrationRequest {
    pub name: String,
    pub email: String,
    pub password: String,
}

pub enum ValidationResult {
    Valid,
    Invalid(Vec<String>),
}

pub fn validate_registration(request: &RegistrationRequest) -> ValidationResult {
    todo!()
}
```

## Step 2: Write Tests (RED)

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn valid_registration_returns_valid() {
        let request = RegistrationRequest {
            name: "Alice".into(),
            email: "alice@example.com".into(),
            password: "SecureP@ss1".into(),
        };
        assert!(matches!(validate_registration(&request), ValidationResult::Valid));
    }

    #[test]
    fn blank_name_returns_invalid() {
        let request = RegistrationRequest {
            name: "".into(),
            email: "alice@example.com".into(),
            password: "SecureP@ss1".into(),
        };
        match validate_registration(&request) {
            ValidationResult::Invalid(errors) => {
                assert!(errors.contains(&"Name is required".to_string()));
            }
            ValidationResult::Valid => panic!("expected Invalid"),
        }
    }

    #[test]
    fn invalid_email_returns_invalid() {
        let request = RegistrationRequest {
            name: "Alice".into(),
            email: "not-an-email".into(),
            password: "SecureP@ss1".into(),
        };
        assert!(matches!(
            validate_registration(&request),
            ValidationResult::Invalid(_)
        ));
    }

    #[test]
    fn short_password_returns_invalid() {
        let request = RegistrationRequest {
            name: "Alice".into(),
            email: "alice@example.com".into(),
            password: "short".into(),
        };
        assert!(matches!(
            validate_registration(&request),
            ValidationResult::Invalid(_)
        ));
    }
}
```

## Step 3: Run Tests — Verify FAIL

```bash
$ cargo test
running 4 tests
test tests::valid_registration_returns_valid ... FAILED (not yet implemented)
test tests::blank_name_returns_invalid ... FAILED
test tests::invalid_email_returns_invalid ... FAILED
test tests::short_password_returns_invalid ... FAILED
```

Tests fail as expected (todo! panic).

## Step 4: Implement Minimal Code (GREEN)

```rust
pub fn validate_registration(request: &RegistrationRequest) -> ValidationResult {
    let mut errors = Vec::new();

    if request.name.trim().is_empty() {
        errors.push("Name is required".into());
    }
    if !request.email.contains('@') {
        errors.push("Invalid email format".into());
    }
    if request.password.len() < 8 {
        errors.push("Password must be at least 8 characters".into());
    }

    if errors.is_empty() {
        ValidationResult::Valid
    } else {
        ValidationResult::Invalid(errors)
    }
}
```

## Step 5: Run Tests — Verify PASS

```bash
$ cargo test
running 4 tests
test tests::valid_registration_returns_valid ... ok
test tests::blank_name_returns_invalid ... ok
test tests::invalid_email_returns_invalid ... ok
test tests::short_password_returns_invalid ... ok

test result: ok. 4 passed; 0 failed
```

All tests passing!

## Step 6: Check Coverage

```bash
$ cargo llvm-cov
Coverage: 100.0% of lines
```

Coverage: 100%

## TDD Complete!
````

## テストパターン (Test Patterns)

### Unit Tests

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn adds_two_numbers() {
        assert_eq!(add(2, 3), 5);
    }

    #[test]
    fn handles_error() -> Result<(), Box<dyn std::error::Error>> {
        let result = parse_config(r#"port = 8080"#)?;
        assert_eq!(result.port, 8080);
        Ok(())
    }
}
```

### rstest によるパラメータ化テスト (Parameterized Tests with rstest)

```rust
use rstest::{rstest, fixture};

#[rstest]
#[case("hello", 5)]
#[case("", 0)]
#[case("rust", 4)]
fn test_string_length(#[case] input: &str, #[case] expected: usize) {
    assert_eq!(input.len(), expected);
}
```

### Async Tests

```rust
#[tokio::test]
async fn fetches_data_successfully() {
    let client = TestClient::new().await;
    let result = client.get("/data").await;
    assert!(result.is_ok());
}
```

### Property-Based Tests

```rust
use proptest::prelude::*;

proptest! {
    #[test]
    fn encode_decode_roundtrip(input in ".*") {
        let encoded = encode(&input);
        let decoded = decode(&encoded).unwrap();
        assert_eq!(input, decoded);
    }
}
```

## Coverage コマンド (Coverage Commands)

```bash
# Summary report
cargo llvm-cov

# HTML report
cargo llvm-cov --html

# Fail if below threshold
cargo llvm-cov --fail-under-lines 80

# Run specific test
cargo test test_name

# Run with output
cargo test -- --nocapture

# Run without stopping on first failure
cargo test --no-fail-fast
```

## Coverage 目標 (Coverage Targets)

| Code Type | Target |
|-----------|--------|
| Critical business logic | 100% |
| Public API | 90%+ |
| General code | 80%+ |
| Generated / FFI bindings | Exclude |

## TDD ベストプラクティス (TDD Best Practices)

**DO:**
- 実装より先にテストを書く
- 変更のたびにテストを実行
- より良いエラーメッセージのため `assert!` より `assert_eq!` を使う
- 出力をきれいにするため `Result` を返すテストでは `?` を使う
- implementation ではなく behavior をテスト
- エッジケース（空、境界、エラーパス）を含める

**DON'T:**
- テストより先に実装を書く
- RED フェーズをスキップ
- `Result::is_err()` で足りるときに `#[should_panic]` を使う
- テストで `sleep()` を使う — channel または `tokio::time::pause()` を使う
- すべてをモック — 可能なら integration test を優先

## 関連コマンド (Related Commands)

- `/rust-build` - ビルドエラーを修正
- `/rust-review` - 実装後にコードをレビュー
- `verification-loop` skill - フル verification loop を実行

## 関連 (Related)

- Skill: `skills/rust-testing/`
- Skill: `skills/rust-patterns/`
