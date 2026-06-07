---
paths:
  - "**/*.java"
---
# Java パターン (Java Patterns)

> このファイルは [common/patterns.md](../common/patterns.md) を拡張し、Java 固有の内容を追加する。

## Repository パターン (Repository Pattern)

データアクセスをインターフェースの背後にカプセル化:

```java
public interface OrderRepository {
    Optional<Order> findById(Long id);
    List<Order> findAll();
    Order save(Order order);
    void deleteById(Long id);
}
```

具象実装がストレージの詳細（JPA、JDBC、テスト用インメモリ）を扱う。

## Service レイヤー (Service Layer)

ビジネスロジックは service クラスに。controller と repository は薄く保つ:

```java
public class OrderService {
    private final OrderRepository orderRepository;
    private final PaymentGateway paymentGateway;

    public OrderService(OrderRepository orderRepository, PaymentGateway paymentGateway) {
        this.orderRepository = orderRepository;
        this.paymentGateway = paymentGateway;
    }

    public OrderSummary placeOrder(CreateOrderRequest request) {
        var order = Order.from(request);
        paymentGateway.charge(order.total());
        var saved = orderRepository.save(order);
        return OrderSummary.from(saved);
    }
}
```

## コンストラクタ注入 (Constructor Injection)

常にコンストラクタ注入を使用 — フィールド注入は使わない:

```java
// GOOD — constructor injection (testable, immutable)
public class NotificationService {
    private final EmailSender emailSender;

    public NotificationService(EmailSender emailSender) {
        this.emailSender = emailSender;
    }
}

// BAD — field injection (untestable without reflection, requires framework magic)
public class NotificationService {
    @Inject // or @Autowired
    private EmailSender emailSender;
}
```

## DTO マッピング (DTO Mapping)

DTO には record を使用。service/controller 境界でマッピング:

```java
public record OrderResponse(Long id, String customer, BigDecimal total) {
    public static OrderResponse from(Order order) {
        return new OrderResponse(order.getId(), order.getCustomerName(), order.getTotal());
    }
}
```

## Builder パターン (Builder Pattern)

多くのオプションパラメータを持つオブジェクトに使用:

```java
public class SearchCriteria {
    private final String query;
    private final int page;
    private final int size;
    private final String sortBy;

    private SearchCriteria(Builder builder) {
        this.query = builder.query;
        this.page = builder.page;
        this.size = builder.size;
        this.sortBy = builder.sortBy;
    }

    public static class Builder {
        private String query = "";
        private int page = 0;
        private int size = 20;
        private String sortBy = "id";

        public Builder query(String query) { this.query = query; return this; }
        public Builder page(int page) { this.page = page; return this; }
        public Builder size(int size) { this.size = size; return this; }
        public Builder sortBy(String sortBy) { this.sortBy = sortBy; return this; }
        public SearchCriteria build() { return new SearchCriteria(this); }
    }
}
```

## ドメインモデルの Sealed 型 (Sealed Types for Domain Models)

```java
public sealed interface PaymentResult permits PaymentSuccess, PaymentFailure {
    record PaymentSuccess(String transactionId, BigDecimal amount) implements PaymentResult {}
    record PaymentFailure(String errorCode, String message) implements PaymentResult {}
}

// Exhaustive handling (Java 21+)
String message = switch (result) {
    case PaymentSuccess s -> "Paid: " + s.transactionId();
    case PaymentFailure f -> "Failed: " + f.errorCode();
};
```

## API レスポンスエンベロープ (API Response Envelope)

一貫した API レスポンス:

```java
public record ApiResponse<T>(boolean success, T data, String error) {
    public static <T> ApiResponse<T> ok(T data) {
        return new ApiResponse<>(true, data, null);
    }
    public static <T> ApiResponse<T> error(String message) {
        return new ApiResponse<>(false, null, message);
    }
}
```

## 参照 (References)

Spring Boot アーキテクチャパターンは skill: `springboot-patterns` を参照。
REST、Panache、メッセージングを含む Quarkus アーキテクチャパターンは skill: `quarkus-patterns` を参照。
エンティティ設計とクエリ最適化は skill: `jpa-patterns` を参照。
