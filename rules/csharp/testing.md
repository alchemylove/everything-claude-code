---
paths:
  - "**/*.cs"
  - "**/*.csx"
  - "**/*.csproj"
---
# C# テスト (C# Testing)

> このファイルは [common/testing.md](../common/testing.md) を拡張し、C# 固有の内容を追加する。

## テストフレームワーク (Test Framework)

- unit テストと integration テストには **xUnit** を優先する
- 読みやすい assertion には **FluentAssertions** を使用する
- 依存の mock には **Moq** または **NSubstitute** を使用する
- integration テストで実インフラが必要な場合は **Testcontainers** を使用する

## テスト構成 (Test Organization)

- `src/` 構造を `tests/` 配下にミラーする
- unit、integration、end-to-end カバレッジを明確に分離する
- 実装詳細ではなく挙動でテストに名前を付ける

```csharp
public sealed class OrderServiceTests
{
    [Fact]
    public async Task FindByIdAsync_ReturnsOrder_WhenOrderExists()
    {
        // Arrange
        // Act
        // Assert
    }
}
```

## ASP.NET Core integration テスト (ASP.NET Core Integration Tests)

- API integration カバレッジには `WebApplicationFactory<TEntryPoint>` を使用する
- middleware をバイパスせず、HTTP 経由で auth、バリデーション、シリアライズをテストする

## カバレッジ (Coverage)

- 行カバレッジ 80%+ を目標とする
- カバレッジはドメインロジック、バリデーション、auth、失敗パスに集中する
- 可能な場合は CI でカバレッジ収集を有効にした `dotnet test` を実行する
