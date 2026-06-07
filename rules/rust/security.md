---
paths:
  - "**/*.rs"
---
# Rust セキュリティ (Rust Security)

> このファイルは [common/security.md](../common/security.md) を拡張し、Rust 固有の内容を追加する。

## シークレット管理 (Secrets Management)

- API キー、トークン、認証情報をソースコードにハードコードしない
- 環境変数を使用: `std::env::var("API_KEY")`
- 起動時に必須シークレットが欠けていれば fail fast
- `.env` ファイルは `.gitignore` に入れる

```rust
// BAD
const API_KEY: &str = "sk-abc123...";

// GOOD — environment variable with early validation
fn load_api_key() -> anyhow::Result<String> {
    std::env::var("PAYMENT_API_KEY")
        .context("PAYMENT_API_KEY must be set")
}
```

## SQL インジェクション防止 (SQL Injection Prevention)

- 常にパラメータ化クエリを使用 — ユーザー入力を SQL 文字列に format しない
- バインドパラメータ付きのクエリビルダーまたは ORM（sqlx、diesel、sea-orm）を使用

```rust
// BAD — SQL injection via format string
let query = format!("SELECT * FROM users WHERE name = '{name}'");
sqlx::query(&query).fetch_one(&pool).await?;

// GOOD — parameterized query with sqlx
// Placeholder syntax varies by backend: Postgres: $1  |  MySQL: ?  |  SQLite: $1
sqlx::query("SELECT * FROM users WHERE name = $1")
    .bind(&name)
    .fetch_one(&pool)
    .await?;
```

## 入力検証 (Input Validation)

- 処理前にシステム境界ですべてのユーザー入力を検証
- 型システムで不変条件を強制（newtype パターン）
- parse, don't validate — 境界で非構造化データを型付き struct に変換
- 無効な入力は明確なエラーメッセージで拒否

```rust
// Parse, don't validate — invalid states are unrepresentable
pub struct Email(String);

impl Email {
    pub fn parse(input: &str) -> Result<Self, ValidationError> {
        let trimmed = input.trim();
        let at_pos = trimmed.find('@')
            .filter(|&p| p > 0 && p < trimmed.len() - 1)
            .ok_or_else(|| ValidationError::InvalidEmail(input.to_string()))?;
        let domain = &trimmed[at_pos + 1..];
        if trimmed.len() > 254 || !domain.contains('.') {
            return Err(ValidationError::InvalidEmail(input.to_string()));
        }
        // For production use, prefer a validated email crate (e.g., `email_address`)
        Ok(Self(trimmed.to_string()))
    }

    pub fn as_str(&self) -> &str {
        &self.0
    }
}
```

## Unsafe コード (Unsafe Code)

- `unsafe` ブロックを最小化 — 安全な抽象化を優先
- すべての `unsafe` ブロックに不変条件を説明する `// SAFETY:` コメントが必要
- 便宜のために借用チェッカーを回避する `unsafe` は使わない
- レビュー時にすべての `unsafe` を監査 — 正当化なしは red flag
- C ライブラリ周りは `safe` FFI ラッパーを優先

```rust
// GOOD — safety comment documents ALL required invariants
let widget: &Widget = {
    // SAFETY: `ptr` is non-null, aligned, points to an initialized Widget,
    // and no mutable references or mutations exist for its lifetime.
    unsafe { &*ptr }
};

// BAD — no safety justification
unsafe { &*ptr }
```

## 依存関係セキュリティ (Dependency Security)

- `cargo audit` で依存の既知 CVE をスキャン
- `cargo deny check` でライセンスとアドバイザリ準拠を確認
- `cargo tree` で推移的依存を監査
- 依存関係を更新 — Dependabot または Renovate を設定
- 依存数を最小化 — 新 crate 追加前に評価

```bash
# Security audit
cargo audit

# Deny advisories, duplicate versions, and restricted licenses
cargo deny check

# Inspect dependency tree
cargo tree
cargo tree -d  # Show duplicates only
```

## エラーメッセージ (Error Messages)

- API レスポンスに内部パス、スタックトレース、DB エラーを露出しない
- 詳細エラーはサーバー側でログ、クライアントには汎用メッセージを返す
- 構造化サーバー側ログに `tracing` または `log` を使用

```rust
// Map errors to appropriate status codes and generic messages
// (Example uses axum; adapt the response type to your framework)
match order_service.find_by_id(id) {
    Ok(order) => Ok((StatusCode::OK, Json(order))),
    Err(ServiceError::NotFound(_)) => {
        tracing::info!(order_id = id, "order not found");
        Err((StatusCode::NOT_FOUND, "Resource not found"))
    }
    Err(e) => {
        tracing::error!(order_id = id, error = %e, "unexpected error");
        Err((StatusCode::INTERNAL_SERVER_ERROR, "Internal server error"))
    }
}
```

## 参照 (References)

unsafe コードガイドラインと所有権パターンは skill: `rust-patterns` を参照。
一般的なセキュリティチェックリストは skill: `security-review` を参照。
