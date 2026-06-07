---
description: ownership、lifetime、error handling、unsafe の使用、idiomatic pattern を含む包括的な Rust コードレビュー。rust-reviewer agent を起動する。
---

# Rust コードレビュー (Rust Code Review)

このコマンドは Rust 固有の包括的コードレビューのため **rust-reviewer** agent を起動する。

## このコマンドの内容 (What This Command Does)

1. **自動チェックの検証**: `cargo check`、`cargo clippy -- -D warnings`、`cargo fmt --check`、`cargo test` を実行 — いずれか失敗なら停止
2. **Rust 変更の特定**: `git diff HEAD~1`（PR なら `git diff main...HEAD`）で変更された `.rs` を検出
3. **セキュリティ監査の実行**: 利用可能なら `cargo audit` を実行
4. **セキュリティスキャン**: unsafe の使用、command injection、ハードコードされた secret を確認
5. **Ownership レビュー**: 不要な clone、lifetime issue、borrowing pattern を分析
6. **レポート生成**: 重大度で issue を分類

## 使用タイミング (When to Use)

`/rust-review` を使う場面:
- Rust コードを書いた・変更したあと
- Rust 変更を commit する前
- Rust コードを含む pull request をレビューするとき
- 新しい Rust コードベースのオンボーディング
- idiomatic Rust pattern の学習

## レビューカテゴリ (Review Categories)

### CRITICAL（必須修正） (CRITICAL (Must Fix))
- 本番コードパスでの未チェック `unwrap()`/`expect()`
- 不変条件を文書化する `// SAFETY:` コメントのない `unsafe`
- クエリ内の文字列補間による SQL injection
- `std::process::Command` への未検証入力による command injection
- ハードコードされた認証情報
- raw pointer による use-after-free

### HIGH（修正推奨） (HIGH (Should Fix))
- borrow checker 満足のための不要な `.clone()`
- `&str` または `impl AsRef<str>` で足りる箇所の `String` パラメータ
- async コンテキストでのブロッキング（`std::thread::sleep`、`std::fs`）
- 共有型に `Send`/`Sync` bound がない
- ビジネス上重要な enum での wildcard `_ =>` match
- 大きな関数（>50 行）

### MEDIUM（検討） (MEDIUM (Consider))
- ホットパスでの不要な allocation
- サイズが分かっているのに `with_capacity` がない
- 根拠なく抑制された clippy warning
- `///` ドキュメントのない public API
- 値の無視が bug になりやすい戻り値への `#[must_use]` の検討

## 実行する自動チェック (Automated Checks Run)

```bash
# Build gate (must pass before review)
cargo check

# Lints and suggestions
cargo clippy -- -D warnings

# Formatting
cargo fmt --check

# Tests
cargo test

# Security audit (if available)
if command -v cargo-audit >/dev/null; then cargo audit; else echo "cargo-audit not installed"; fi
```

## 使用例 (Example Usage)

````text
User: /rust-review

Agent:
# Rust Code Review Report

## Files Reviewed
- src/service/user.rs (modified)
- src/handler/api.rs (modified)

## Static Analysis Results
- Build: Successful
- Clippy: No warnings
- Formatting: Passed
- Tests: All passing

## Issues Found

[CRITICAL] Unchecked unwrap in Production Path
File: src/service/user.rs:28
Issue: Using `.unwrap()` on database query result
```rust
let user = db.find_by_id(id).unwrap();  // Panics on missing user
```
Fix: Propagate error with context
```rust
let user = db.find_by_id(id)
    .context("failed to fetch user")?;
```

[HIGH] Unnecessary Clone
File: src/handler/api.rs:45
Issue: Cloning String to satisfy borrow checker
```rust
let name = user.name.clone();
process(&user, &name);
```
Fix: Restructure to avoid clone
```rust
let result = process_name(&user.name);
use_user(&user, result);
```

## Summary
- CRITICAL: 1
- HIGH: 1
- MEDIUM: 0

Recommendation: Block merge until CRITICAL issue is fixed
````

## 承認基準 (Approval Criteria)

| Status | Condition |
|--------|-----------|
| Approve | No CRITICAL or HIGH issues |
| Warning | Only MEDIUM issues (merge with caution) |
| Block | CRITICAL or HIGH issues found |

## 他コマンドとの連携 (Integration with Other Commands)

- 先に `/rust-test` でテストが通ることを確認
- ビルドエラーがあれば `/rust-build` を使用
- commit 前に `/rust-review` を実行
- 非 Rust 固有の懸念には `/code-review` を使用

## 関連 (Related)

- Agent: `agents/rust-reviewer.md`
- Skills: `skills/rust-patterns/`, `skills/rust-testing/`
