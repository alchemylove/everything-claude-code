---
name: java-coding-standards
description: "Java coding standards for Spring Boot and Quarkus services: naming, immutability, Optional usage, streams, exceptions, generics, CDI, reactive patterns, and project layout. Automatically applies framework-specific conventions."
---

# Javaコーディング標準 (Java Coding Standards)

Spring Bootサービスにおける読みやすく保守可能なJava(17+)コードの標準。

## 核となる原則 (When to Use)

- 巧妙さよりも明確さを優先
- デフォルトで不変; 共有可変状態を最小化
- 意味のある例外で早期失敗
- 一貫した命名とパッケージ構造

## 命名 (How It Works)

```java
// PASS: クラス/レコード: PascalCase
public class MarketService {}
public record Money(BigDecimal amount, Currency currency) {}

// PASS: メソッド/フィールド: camelCase
private final MarketRepository marketRepository;
public Market findBySlug(String slug) {}

// PASS: 定数: UPPER_SNAKE_CASE
private static final int MAX_PAGE_SIZE = 100;
```

## 不変性 (Framework Detection)

```java
// PASS: recordとfinalフィールドを優先
public record MarketDto(Long id, String name, MarketStatus status) {}

public class Market {
  private final Long id;
  private final String name;
  // getterのみ、setterなし
}
```

## Optionalの使用 (Core Principles)

```java
// PASS: find*メソッドからOptionalを返す
Optional<Market> market = marketRepository.findBySlug(slug);

// PASS: get()の代わりにmap/flatMapを使用
return market
    .map(MarketResponse::from)
    .orElseThrow(() -> new EntityNotFoundException("Market not found"));
```

## ストリームのベストプラクティス (Examples)

```java
// PASS: 変換にストリームを使用し、パイプラインを短く保つ
List<String> names = markets.stream()
    .map(Market::name)
    .filter(Objects::nonNull)
    .toList();

// FAIL: 複雑なネストされたストリームを避ける; 明確性のためにループを優先
```

## 例外 (Naming)

- ドメインエラーには非チェック例外を使用; 技術的例外はコンテキストとともにラップ
- ドメイン固有の例外を作成(例: `MarketNotFoundException`)
- 広範な`catch (Exception ex)`を避ける(中央でリスロー/ログ記録する場合を除く)

```java
throw new MarketNotFoundException(slug);
```

## ジェネリクスと型安全性 (Immutability)

- 生の型を避ける; ジェネリックパラメータを宣言
- 再利用可能なユーティリティには境界付きジェネリクスを優先

```java
public <T extends Identifiable> Map<Long, T> indexById(Collection<T> items) { ... }
```

## プロジェクト構造 (Optional Usage)

```
src/main/java/com/example/app/
  config/
  controller/
  service/
  repository/
  domain/
  dto/
  util/
src/main/resources/
  application.yml
src/test/java/... (mainをミラー)
```

## フォーマットとスタイル (Streams Best Practices)

- 一貫して2または4スペースを使用(プロジェクト標準)
- ファイルごとに1つのpublicトップレベル型
- メソッドを短く集中的に保つ; ヘルパーを抽出
- メンバーの順序: 定数、フィールド、コンストラクタ、publicメソッド、protected、private

## 避けるべきコードの臭い (Dependency Injection)

- 長いパラメータリスト → DTO/ビルダーを使用
- 深いネスト → 早期リターン
- マジックナンバー → 名前付き定数
- 静的可変状態 → 依存性注入を優先
- サイレントなcatchブロック → ログを記録して行動、または再スロー

## ログ記録 (Reactive Patterns [QUARKUS])

```java
private static final Logger log = LoggerFactory.getLogger(MarketService.class);
log.info("fetch_market slug={}", slug);
log.error("failed_fetch_market slug={}", slug, ex);
```

## Null処理 (Exceptions)

- やむを得ない場合のみ`@Nullable`を受け入れる; それ以外は`@NonNull`を使用
- 入力にBean Validation(`@NotNull`、`@NotBlank`)を使用

## テストの期待 (Centralised Exception Handling)

- JUnit 5 + AssertJで流暢なアサーション
- モック用のMockito; 可能な限り部分モックを避ける
- 決定論的テストを優先; 隠れたsleepなし

**覚えておく**: コードを意図的、型付き、観察可能に保つ。必要性が証明されない限り、マイクロ最適化よりも保守性を最適化します。
