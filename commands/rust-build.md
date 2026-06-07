---
description: Rust のビルドエラー、borrow checker の issue、依存関係の問題を段階的に修正する。最小限の surgical fix のため rust-build-resolver agent を起動する。
---

# Rust ビルドと修正 (Rust Build and Fix)

このコマンドは **rust-build-resolver** agent を起動し、最小限の変更で Rust のビルドエラーを段階的に修正する。

## このコマンドの内容 (What This Command Does)

1. **診断の実行**: `cargo check`、`cargo clippy`、`cargo fmt --check` を実行
2. **エラーの解析**: error code と影響ファイルを特定
3. **段階的な修正**: 1 件ずつ修正
4. **各修正の検証**: 変更のたびに `cargo check` を再実行
5. **サマリーの報告**: 修正内容と残件を表示

## 使用タイミング (When to Use)

`/rust-build` を使う場面:
- `cargo build` または `cargo check` がエラーで失敗するとき
- `cargo clippy` が warning を報告するとき
- borrow checker または lifetime エラーでコンパイルが止まるとき
- Cargo の依存関係解決が失敗するとき
- pull 後にビルドが壊れたとき

## 実行する診断コマンド (Diagnostic Commands Run)

```bash
# Primary build check
cargo check 2>&1

# Lints and suggestions
cargo clippy -- -D warnings 2>&1

# Formatting check
cargo fmt --check 2>&1

# Dependency issues
cargo tree --duplicates

# Security audit (if available)
if command -v cargo-audit >/dev/null; then cargo audit; else echo "cargo-audit not installed"; fi
```

## セッション例 (Example Session)

````text
User: /rust-build

Agent:
# Rust Build Resolution

## Initial Diagnostics

```bash
$ cargo check
error[E0502]: cannot borrow `map` as mutable because it is also borrowed as immutable
  --> src/service/cache.rs:25:5
error[E0308]: mismatched types
  --> src/handler/api.rs:42:12
error[E0425]: cannot find value `db` in this scope
  --> src/handler/api.rs:58:9
```

Errors found: 3

## Fix 1: Borrow Checker Error

File: src/service/cache.rs:25
Error: E0502 — cannot borrow `map` as mutable because also borrowed as immutable
Cause: Immutable borrow still active during mutable insert

```rust
// Changed: restructured to end immutable borrow before mutable access
if !map.contains_key("key") {
    map.insert("key".into(), default);
}
```

```bash
$ cargo check
# 2 errors remaining
```

## Fix 2: Type Mismatch

File: src/handler/api.rs:42
Error: E0308 — mismatched types: expected `usize`, found `&str`

```rust
// Changed
let count = params.get("count");
// To
let count: usize = params.get("count")
    .and_then(|s| s.parse().ok())
    .unwrap_or(0);
```

```bash
$ cargo check
# 1 error remaining
```

## Fix 3: Unresolved Name

File: src/handler/api.rs:58
Error: E0425 — cannot find value `db` in this scope
Cause: Missing import

```rust
// Added
use crate::db;
```

```bash
$ cargo check
# Build successful!
```

## Final Verification

```bash
$ cargo clippy -- -D warnings
# No warnings

$ cargo test
running 6 tests
test service::cache::tests::test_insert ... ok
test service::cache::tests::test_get ... ok
test handler::api::tests::test_parse_count ... ok
test handler::api::tests::test_missing_count ... ok
test handler::api::tests::test_db_import ... ok
test handler::api::tests::test_response ... ok

test result: ok. 6 passed; 0 failed; 0 ignored
```

## Summary

| Metric | Count |
|--------|-------|
| Build errors fixed | 3 |
| Clippy warnings fixed | 0 |
| Files modified | 2 |
| Remaining issues | 0 |

Build Status: SUCCESS
````

## よくあるエラーと修正 (Common Errors Fixed)

| Error | Typical Fix |
|-------|-------------|
| `cannot borrow as mutable` | Restructure to end immutable borrow first; clone only if justified |
| `does not live long enough` | Use owned type or add lifetime annotation |
| `cannot move out of` | Restructure to take ownership; clone only as last resort |
| `mismatched types` | Add `.into()`, `as`, or explicit conversion |
| `trait X not implemented` | Add `#[derive(Trait)]` or implement manually |
| `unresolved import` | Add to Cargo.toml or fix `use` path |
| `cannot find value` | Add import or fix path |

## 修正戦略 (Fix Strategy)

1. **ビルドエラーを最優先** - コードはコンパイルできる必要がある
2. **Clippy warning を次に** - 疑わしい構文を修正
3. **フォーマットをその次** - `cargo fmt` 準拠
4. **1 件ずつ修正** - 各変更を検証
5. **最小限の変更** - リファクタではなく修正のみ

## 停止条件 (Stop Conditions)

agent は次の場合に停止して報告する:
- 同じエラーが 3 回試行後も残る
- 修正がより多くのエラーを導入した
- アーキテクチャ変更が必要
- borrow checker エラーが data ownership の再設計を要求する

## 関連コマンド (Related Commands)

- `/rust-test` - ビルド成功後にテストを実行
- `/rust-review` - コード品質をレビュー
- `verification-loop` skill - フル verification loop

## 関連 (Related)

- Agent: `agents/rust-build-resolver.md`
- Skill: `skills/rust-patterns/`
