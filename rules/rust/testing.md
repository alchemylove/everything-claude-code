---
paths:
  - "**/*.rs"
---
# Rust テスト (Rust Testing)

> このファイルは [common/testing.md](../common/testing.md) を拡張し、Rust 固有の内容を追加する。

## テストフレームワーク (Test Framework)

- ユニットテストには **`#[test]`** と `#[cfg(test)]` モジュール
- パラメータ化テストと fixture には **rstest**
- プロパティベーステストには **proptest**
- trait ベースのモックには **mockall**
- async テストには **`#[tokio::test]`**

## テスト構成 (Test Organization)

```text
my_crate/
├── src/
│   ├── lib.rs           # Unit tests in #[cfg(test)] modules
│   ├── auth/
│   │   └── mod.rs       # #[cfg(test)] mod tests { ... }
│   └── orders/
│       └── service.rs   # #[cfg(test)] mod tests { ... }
├── tests/               # Integration tests (each file = separate binary)
│   ├── api_test.rs
│   ├── db_test.rs
│   └── common/          # Shared test utilities
│       └── mod.rs
└── benches/             # Criterion benchmarks
    └── benchmark.rs
```

ユニットテストは同じファイル内の `#[cfg(test)]` モジュールに。統合テストは `tests/` に配置。

## ユニットテストパターン (Unit Test Pattern)

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn creates_user_with_valid_email() {
        let user = User::new("Alice", "alice@example.com").unwrap();
        assert_eq!(user.name, "Alice");
    }

    #[test]
    fn rejects_invalid_email() {
        let result = User::new("Bob", "not-an-email");
        assert!(result.is_err());
        assert!(result.unwrap_err().to_string().contains("invalid email"));
    }
}
```

## パラメータ化テスト (Parameterized Tests)

```rust
use rstest::rstest;

#[rstest]
#[case("hello", 5)]
#[case("", 0)]
#[case("rust", 4)]
fn test_string_length(#[case] input: &str, #[case] expected: usize) {
    assert_eq!(input.len(), expected);
}
```

## Async テスト (Async Tests)

```rust
#[tokio::test]
async fn fetches_data_successfully() {
    let client = TestClient::new().await;
    let result = client.get("/data").await;
    assert!(result.is_ok());
}
```

## mockall によるモック (Mocking with mockall)

本番コードで trait を定義。テストモジュールでモックを生成:

```rust
// Production trait — pub so integration tests can import it
pub trait UserRepository {
    fn find_by_id(&self, id: u64) -> Option<User>;
}

#[cfg(test)]
mod tests {
    use super::*;
    use mockall::predicate::eq;

    mockall::mock! {
        pub Repo {}
        impl UserRepository for Repo {
            fn find_by_id(&self, id: u64) -> Option<User>;
        }
    }

    #[test]
    fn service_returns_user_when_found() {
        let mut mock = MockRepo::new();
        mock.expect_find_by_id()
            .with(eq(42))
            .times(1)
            .returning(|_| Some(User { id: 42, name: "Alice".into() }));

        let service = UserService::new(Box::new(mock));
        let user = service.get_user(42).unwrap();
        assert_eq!(user.name, "Alice");
    }
}
```

## テスト命名 (Test Naming)

シナリオを説明する説明的な名前を使用:
- `creates_user_with_valid_email()`
- `rejects_order_when_insufficient_stock()`
- `returns_none_when_not_found()`

## カバレッジ (Coverage)

- 行カバレッジ 80%+ を目標
- カバレッジレポートに **cargo-llvm-cov** を使用
- ビジネスロジックに集中 — 生成コードと FFI バインディングは除外

```bash
cargo llvm-cov                       # Summary
cargo llvm-cov --html                # HTML report
cargo llvm-cov --fail-under-lines 80 # Fail if below threshold
```

## テストコマンド (Testing Commands)

```bash
cargo test                       # Run all tests
cargo test -- --nocapture        # Show println output
cargo test test_name             # Run tests matching pattern
cargo test --lib                 # Unit tests only
cargo test --test api_test       # Specific integration test (tests/api_test.rs)
cargo test --doc                 # Doc tests only
```

## 参照 (References)

プロパティベーステスト、fixture、Criterion ベンチマークを含む包括的テストパターンは skill: `rust-testing` を参照。
