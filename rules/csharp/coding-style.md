---
paths:
  - "**/*.cs"
  - "**/*.csx"
---
# C# コーディングスタイル (C# Coding Style)

> このファイルは [common/coding-style.md](../common/coding-style.md) を拡張し、C# 固有の内容を追加する。

## 標準 (Standards)

- 現行 .NET 規約に従い、nullable reference types を有効にする
- public および internal API では明示的な access modifier を優先する
- ファイルは定義する主要型に揃える

## 型とモデル (Types and Models)

- 不変の value 的モデルには `record` または `record struct` を優先する
- identity とライフサイクルを持つ型には `class` を使用する
- サービス境界と抽象化には `interface` を使用する
- アプリケーションコードでは `dynamic` を避け、generics または明示的モデルを優先する

```csharp
public sealed record UserDto(Guid Id, string Email);

public interface IUserRepository
{
    Task<UserDto?> FindByIdAsync(Guid id, CancellationToken cancellationToken);
}
```

## 不変性 (Immutability)

- 共有状態には `init` setter、コンストラクタパラメータ、不変コレクションを優先する
- 更新状態を生成するとき、入力モデルを in-place で変更しない

```csharp
public sealed record UserProfile(string Name, string Email);

public static UserProfile Rename(UserProfile profile, string name) =>
    profile with { Name = name };
```

## async とエラーハンドリング (Async and Error Handling)

- `.Result` や `.Wait()` などの blocking 呼び出しより `async`/`await` を優先する
- public async API には `CancellationToken` を渡す
- 具体的な例外を投げ、structured properties でログを記録する

```csharp
public async Task<Order> LoadOrderAsync(
    Guid orderId,
    CancellationToken cancellationToken)
{
    try
    {
        return await repository.FindAsync(orderId, cancellationToken)
            ?? throw new InvalidOperationException($"Order {orderId} was not found.");
    }
    catch (Exception ex)
    {
        logger.LogError(ex, "Failed to load order {OrderId}", orderId);
        throw;
    }
}
```

## フォーマット (Formatting)

- フォーマットと analyzer 修正には `dotnet format` を使用する
- `using` ディレクティブを整理し、未使用 import を削除する
- 読みやすさを保てる場合のみ expression-bodied member を優先する
