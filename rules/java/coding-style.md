---
paths:
  - "**/*.java"
---
# Java コーディングスタイル (Java Coding Style)

> このファイルは [common/coding-style.md](../common/coding-style.md) を拡張し、Java 固有の内容を追加する。

## フォーマット (Formatting)

- 強制には **google-java-format** または **Checkstyle**（Google または Sun スタイル）
- ファイルあたり 1 つの public トップレベル型
- インデントは一貫して 2 または 4 スペース（プロジェクト標準に合わせる）
- メンバー順: 定数、フィールド、コンストラクタ、public メソッド、protected、private

## 不変性 (Immutability)

- 値型には `record` を優先（Java 16+）
- フィールドはデフォルトで `final` — 必要なときだけ可変状態を使用
- 公開 API からは防御的コピーを返す: `List.copyOf()`、`Map.copyOf()`、`Set.copyOf()`
- コピーオンライト: 既存を変更せず新しいインスタンスを返す

```java
// GOOD — immutable value type
public record OrderSummary(Long id, String customerName, BigDecimal total) {}

// GOOD — final fields, no setters
public class Order {
    private final Long id;
    private final List<LineItem> items;

    public List<LineItem> getItems() {
        return List.copyOf(items);
    }
}
```

## 命名 (Naming)

標準 Java 規約に従う:
- クラス・インターフェース・record・enum は `PascalCase`
- メソッド・フィールド・パラメータ・ローカル変数は `camelCase`
- `static final` 定数は `SCREAMING_SNAKE_CASE`
- パッケージ: すべて小文字、逆ドメイン（`com.example.app.service`）

## モダン Java 機能 (Modern Java Features)

明確さが向上する場合はモダン言語機能を使用:
- 値型・DTO には **Records**（Java 16+）
- 閉じた型階層には **Sealed classes**（Java 17+）
- `instanceof` の **パターンマッチング** — 明示的キャスト不要（Java 16+）
- 複数行文字列には **Text blocks** — SQL、JSON テンプレート（Java 15+）
- アロー構文の **Switch 式**（Java 14+）
- switch の **パターンマッチング** — 網羅的 sealed 型処理（Java 21+）

```java
// Pattern matching instanceof
if (shape instanceof Circle c) {
    return Math.PI * c.radius() * c.radius();
}

// Sealed type hierarchy
public sealed interface PaymentMethod permits CreditCard, BankTransfer, Wallet {}

// Switch expression
String label = switch (status) {
    case ACTIVE -> "Active";
    case SUSPENDED -> "Suspended";
    case CLOSED -> "Closed";
};
```

## Optional の使用 (Optional Usage)

- 結果がない可能性がある finder メソッドは `Optional<T>` を返す
- `map()`、`flatMap()`、`orElseThrow()` を使用 — `isPresent()` なしで `get()` を呼ばない
- フィールド型やメソッドパラメータに `Optional` を使わない

```java
// GOOD
return repository.findById(id)
    .map(ResponseDto::from)
    .orElseThrow(() -> new OrderNotFoundException(id));

// BAD — Optional as parameter
public void process(Optional<String> name) {}
```

## エラーハンドリング (Error Handling)

- ドメインエラーには非チェック例外を優先
- `RuntimeException` を継承したドメイン固有の例外を作成
- トップレベルハンドラ以外で広い `catch (Exception e)` を避ける
- 例外メッセージにコンテキストを含める

```java
public class OrderNotFoundException extends RuntimeException {
    public OrderNotFoundException(Long id) {
        super("Order not found: id=" + id);
    }
}
```

## Streams

- 変換には stream を使用。パイプラインは短く（最大 3〜4 操作）
- 読みやすい場合はメソッド参照を優先: `.map(Order::getTotal)`
- stream 操作で副作用を避ける
- 複雑なロジックは入り組んだ stream よりループを優先

## 参照 (References)

例付きの完全なコーディング標準は skill: `java-coding-standards` を参照。
JPA/Hibernate エンティティ設計パターンは skill: `jpa-patterns` を参照。
