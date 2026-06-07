---
paths:
  - "**/*.rs"
---
# Rust コーディングスタイル (Rust Coding Style)

> このファイルは [common/coding-style.md](../common/coding-style.md) を拡張し、Rust 固有の内容を追加する。

## フォーマット (Formatting)

- 強制には **rustfmt** — コミット前に常に `cargo fmt` を実行
- lint には **clippy** — `cargo clippy -- -D warnings`（警告をエラー扱い）
- 4 スペースインデント（rustfmt デフォルト）
- 最大行幅: 100 文字（rustfmt デフォルト）

## 不変性 (Immutability)

Rust の変数はデフォルトで不変 — これを活用する:

- デフォルトは `let`、変更が必要なときだけ `let mut`
- インプレース変更より新しい値を返すことを優先
- 関数がアロケーションを要するかもしれない場合は `Cow<'_, T>` を使用

```rust
use std::borrow::Cow;

// GOOD — immutable by default, new value returned
fn normalize(input: &str) -> Cow<'_, str> {
    if input.contains(' ') {
        Cow::Owned(input.replace(' ', "_"))
    } else {
        Cow::Borrowed(input)
    }
}

// BAD — unnecessary mutation
fn normalize_bad(input: &mut String) {
    *input = input.replace(' ', "_");
}
```

## 命名 (Naming)

標準 Rust 規約に従う:
- 関数・メソッド・変数・モジュール・crate は `snake_case`
- 型・trait・enum・型パラメータは `PascalCase`（UpperCamelCase）
- 定数と static は `SCREAMING_SNAKE_CASE`
- ライフタイム: 短い小文字（`'a`、`'de`）— 複雑な場合は説明的な名前（`'input`）

## 所有権と借用 (Ownership and Borrowing)

- デフォルトは借用（`&T`）— 保持または消費が必要なときだけ所有権を取る
- 根本原因を理解せずに借用チェッカーを満たすために clone しない
- 関数パラメータでは `String` より `&str`、`Vec<T>` より `&[T]` を受け取る
- `String` を所有するコンストラクタには `impl Into<String>` を使用

```rust
// GOOD — borrows when ownership isn't needed
fn word_count(text: &str) -> usize {
    text.split_whitespace().count()
}

// GOOD — takes ownership in constructor via Into
fn new(name: impl Into<String>) -> Self {
    Self { name: name.into() }
}

// BAD — takes String when &str suffices
fn word_count_bad(text: String) -> usize {
    text.split_whitespace().count()
}
```

## エラーハンドリング (Error Handling)

- 伝播には `Result<T, E>` と `?` を使用 — 本番コードで `unwrap()` を使わない
- **ライブラリ**: `thiserror` で型付きエラーを定義
- **アプリケーション**: 柔軟なエラーコンテキストに `anyhow` を使用
- `.with_context(|| format!("failed to ..."))?` でコンテキストを追加
- `unwrap()` / `expect()` はテストと真に到達不能な状態に限定

```rust
// GOOD — library error with thiserror
#[derive(Debug, thiserror::Error)]
pub enum ConfigError {
    #[error("failed to read config: {0}")]
    Io(#[from] std::io::Error),
    #[error("invalid config format: {0}")]
    Parse(String),
}

// GOOD — application error with anyhow
use anyhow::Context;

fn load_config(path: &str) -> anyhow::Result<Config> {
    let content = std::fs::read_to_string(path)
        .with_context(|| format!("failed to read {path}"))?;
    toml::from_str(&content)
        .with_context(|| format!("failed to parse {path}"))
}
```

## ループよりイテレータ (Iterators Over Loops)

変換にはイテレータチェーンを優先。複雑な制御フローにはループを使用:

```rust
// GOOD — declarative and composable
let active_emails: Vec<&str> = users.iter()
    .filter(|u| u.is_active)
    .map(|u| u.email.as_str())
    .collect();

// GOOD — loop for complex logic with early returns
for user in &users {
    if let Some(verified) = verify_email(&user.email)? {
        send_welcome(&verified)?;
    }
}
```

## モジュール構成 (Module Organization)

型ではなくドメインで整理:

```text
src/
├── main.rs
├── lib.rs
├── auth/           # Domain module
│   ├── mod.rs
│   ├── token.rs
│   └── middleware.rs
├── orders/         # Domain module
│   ├── mod.rs
│   ├── model.rs
│   └── service.rs
└── db/             # Infrastructure
    ├── mod.rs
    └── pool.rs
```

## 可視性 (Visibility)

- デフォルトは private。内部共有には `pub(crate)` を使用
- crate の公開 API の一部だけ `pub` にする
- 公開 API は `lib.rs` から再エクスポート

## 参照 (References)

包括的な Rust イディオムとパターンは skill: `rust-patterns` を参照。
