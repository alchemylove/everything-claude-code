---
paths:
  - "**/*.java"
---
# Java セキュリティ (Java Security)

> このファイルは [common/security.md](../common/security.md) を拡張し、Java 固有の内容を追加する。

## シークレット管理 (Secrets Management)

- API キー、トークン、認証情報をソースコードにハードコードしない
- 環境変数を使用: `System.getenv("API_KEY")`
- 本番シークレットにはシークレットマネージャー（Vault、AWS Secrets Manager）を使用
- シークレットを含むローカル設定ファイルは `.gitignore` に入れる

```java
// BAD
private static final String API_KEY = "sk-abc123...";

// GOOD — environment variable
String apiKey = System.getenv("PAYMENT_API_KEY");
Objects.requireNonNull(apiKey, "PAYMENT_API_KEY must be set");
```

## SQL インジェクション防止 (SQL Injection Prevention)

- 常にパラメータ化クエリを使用 — ユーザー入力を SQL に連結しない
- `PreparedStatement` またはフレームワークのパラメータ化クエリ API を使用
- ネイティブクエリで使う入力は検証・サニタイズ

```java
// BAD — SQL injection via string concatenation
Statement stmt = conn.createStatement();
String sql = "SELECT * FROM orders WHERE name = '" + name + "'";
stmt.executeQuery(sql);

// GOOD — PreparedStatement with parameterized query
PreparedStatement ps = conn.prepareStatement("SELECT * FROM orders WHERE name = ?");
ps.setString(1, name);

// GOOD — JDBC template
jdbcTemplate.query("SELECT * FROM orders WHERE name = ?", mapper, name);
```

## 入力検証 (Input Validation)

- 処理前にシステム境界ですべてのユーザー入力を検証
- 検証フレームワーク使用時は DTO に Bean Validation（`@NotNull`、`@NotBlank`、`@Size`）を適用
- 使用前にファイルパスとユーザー提供文字列をサニタイズ
- 検証失敗の入力は明確なエラーメッセージで拒否

```java
// Validate manually in plain Java
public Order createOrder(String customerName, BigDecimal amount) {
    if (customerName == null || customerName.isBlank()) {
        throw new IllegalArgumentException("Customer name is required");
    }
    if (amount == null || amount.compareTo(BigDecimal.ZERO) <= 0) {
        throw new IllegalArgumentException("Amount must be positive");
    }
    return new Order(customerName, amount);
}
```

## 認証と認可 (Authentication and Authorization)

- カスタム認証暗号を実装しない — 確立されたライブラリを使用
- パスワードは MD5/SHA1 ではなく bcrypt または Argon2 で保存
- service 境界で認可チェックを強制
- ログから機微データを除外 — パスワード、トークン、PII をログに出さない

## 依存関係セキュリティ (Dependency Security)

- `mvn dependency:tree` または `./gradlew dependencies` で推移的依存を監査
- OWASP Dependency-Check または Snyk で既知の CVE をスキャン
- 依存関係を更新 — Dependabot または Renovate を設定

## エラーメッセージ (Error Messages)

- API レスポンスにスタックトレース、内部パス、SQL エラーを露出しない
- ハンドラ境界で例外を安全な汎用クライアントメッセージにマップ
- 詳細エラーはサーバー側でログ、クライアントには汎用メッセージを返す

```java
// Log the detail, return a generic message
try {
    return orderService.findById(id);
} catch (OrderNotFoundException ex) {
    log.warn("Order not found: id={}", id);
    return ApiResponse.error("Resource not found");  // generic, no internals
} catch (Exception ex) {
    log.error("Unexpected error processing order id={}", id, ex);
    return ApiResponse.error("Internal server error");  // never expose ex.getMessage()
}
```

## 参照 (References)

Spring Security の認証・認可パターンは skill: `springboot-security` を参照。
JWT/OIDC、RBAC、CDI を含む Quarkus セキュリティは skill: `quarkus-security` を参照。
一般的なセキュリティチェックリストは skill: `security-review` を参照。
