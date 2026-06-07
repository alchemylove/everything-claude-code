---
paths:
  - "**/*.cs"
  - "**/*.csx"
  - "**/*.csproj"
  - "**/appsettings*.json"
---
# C# セキュリティ (C# Security)

> このファイルは [common/security.md](../common/security.md) を拡張し、C# 固有の内容を追加する。

## シークレット管理 (Secret Management)

- API キー、トークン、接続文字列をソースコードにハードコードしない
- 環境変数、ローカル開発用 user secrets、本番用 secret manager を使用する
- `appsettings.*.json` に実際の認証情報を含めない

```csharp
// BAD
const string ApiKey = "sk-live-123";

// GOOD
var apiKey = builder.Configuration["OpenAI:ApiKey"]
    ?? throw new InvalidOperationException("OpenAI:ApiKey is not configured.");
```

## SQL インジェクション防止 (SQL Injection Prevention)

- ADO.NET、Dapper、EF Core では常に parameterized query を使用する
- ユーザー入力を SQL 文字列に連結しない
- 動的クエリ合成を使う前に sort フィールドと filter 演算子を検証する

```csharp
const string sql = "SELECT * FROM Orders WHERE CustomerId = @customerId";
await connection.QueryAsync<Order>(sql, new { customerId });
```

## 入力検証 (Input Validation)

- アプリケーション境界で DTO を検証する
- data annotations、FluentValidation、または明示的 guard clause を使用する
- ビジネスロジック実行前に無効な model state を拒否する

## 認証と認可 (Authentication and Authorization)

- カスタム token 解析より framework auth handler を優先する
- エンドポイントまたは handler 境界で authorization policy を強制する
- 生トークン、パスワード、PII をログに記録しない

## エラーハンドリング (Error Handling)

- クライアント向けには安全なメッセージを返す
- サーバー側では structured context 付きで詳細な例外をログに記録する
- API レスポンスに stack trace、SQL テキスト、filesystem パスを露出しない

## 参照 (References)

より広いアプリケーションセキュリティレビューチェックリストは skill: `security-review` を参照。
