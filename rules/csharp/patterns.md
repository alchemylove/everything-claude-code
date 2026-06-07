---
paths:
  - "**/*.cs"
  - "**/*.csx"
---
# C# パターン (C# Patterns)

> このファイルは [common/patterns.md](../common/patterns.md) を拡張し、C# 固有の内容を追加する。

## API レスポンスパターン (API Response Pattern)

```csharp
public sealed record ApiResponse<T>(
    bool Success,
    T? Data = default,
    string? Error = null,
    object? Meta = null);
```

## リポジトリパターン (Repository Pattern)

```csharp
public interface IRepository<T>
{
    Task<IReadOnlyList<T>> FindAllAsync(CancellationToken cancellationToken);
    Task<T?> FindByIdAsync(Guid id, CancellationToken cancellationToken);
    Task<T> CreateAsync(T entity, CancellationToken cancellationToken);
    Task<T> UpdateAsync(T entity, CancellationToken cancellationToken);
    Task DeleteAsync(Guid id, CancellationToken cancellationToken);
}
```

## Options パターン (Options Pattern)

コードベース全体で生文字列を読む代わりに、設定には strongly typed options を使用する。

```csharp
public sealed class PaymentsOptions
{
    public const string SectionName = "Payments";
    public required string BaseUrl { get; init; }
    public required string ApiKeySecretName { get; init; }
}
```

## 依存性注入 (Dependency Injection)

- サービス境界では interface に依存する
- コンストラクタを焦点化する。依存が多すぎる場合は責務を分割する
- lifetime を意図的に登録する: stateless/共有サービスは singleton、リクエストデータは scoped、軽量な pure worker は transient
