---
paths:
  - "**/*.java"
---
# Java テスト (Java Testing)

> このファイルは [common/testing.md](../common/testing.md) を拡張し、Java 固有の内容を追加する。

## テストフレームワーク (Test Framework)

- **JUnit 5**（`@Test`、`@ParameterizedTest`、`@Nested`、`@DisplayName`）
- 流暢なアサーションには **AssertJ**（`assertThat(result).isEqualTo(expected)`）
- 依存のモックには **Mockito**
- DB やサービスが必要な統合テストには **Testcontainers**

## テスト構成 (Test Organization)

```
src/test/java/com/example/app/
  service/           # Unit tests for service layer
  controller/        # Web layer / API tests
  repository/        # Data access tests
  integration/       # Cross-layer integration tests
```

`src/main/java` のパッケージ構成を `src/test/java` にミラーする。

## ユニットテストパターン (Unit Test Pattern)

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

    @Mock
    private OrderRepository orderRepository;

    private OrderService orderService;

    @BeforeEach
    void setUp() {
        orderService = new OrderService(orderRepository);
    }

    @Test
    @DisplayName("findById returns order when exists")
    void findById_existingOrder_returnsOrder() {
        var order = new Order(1L, "Alice", BigDecimal.TEN);
        when(orderRepository.findById(1L)).thenReturn(Optional.of(order));

        var result = orderService.findById(1L);

        assertThat(result.customerName()).isEqualTo("Alice");
        verify(orderRepository).findById(1L);
    }

    @Test
    @DisplayName("findById throws when order not found")
    void findById_missingOrder_throws() {
        when(orderRepository.findById(99L)).thenReturn(Optional.empty());

        assertThatThrownBy(() -> orderService.findById(99L))
            .isInstanceOf(OrderNotFoundException.class)
            .hasMessageContaining("99");
    }
}
```

## パラメータ化テスト (Parameterized Tests)

```java
@ParameterizedTest
@CsvSource({
    "100.00, 10, 90.00",
    "50.00, 0, 50.00",
    "200.00, 25, 150.00"
})
@DisplayName("discount applied correctly")
void applyDiscount(BigDecimal price, int pct, BigDecimal expected) {
    assertThat(PricingUtils.discount(price, pct)).isEqualByComparingTo(expected);
}
```

## 統合テスト (Integration Tests)

実 DB 統合には Testcontainers を使用:

```java
@Testcontainers
class OrderRepositoryIT {

    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16");

    private OrderRepository repository;

    @BeforeEach
    void setUp() {
        var dataSource = new PGSimpleDataSource();
        dataSource.setUrl(postgres.getJdbcUrl());
        dataSource.setUser(postgres.getUsername());
        dataSource.setPassword(postgres.getPassword());
        repository = new JdbcOrderRepository(dataSource);
    }

    @Test
    void save_and_findById() {
        var saved = repository.save(new Order(null, "Bob", BigDecimal.ONE));
        var found = repository.findById(saved.getId());
        assertThat(found).isPresent();
    }
}
```

Spring Boot 統合テストは skill: `springboot-tdd` を参照。
Quarkus 統合テストは skill: `quarkus-tdd` を参照。

## テスト命名 (Test Naming)

`@DisplayName` で説明的な名前を使用:
- メソッド名は `methodName_scenario_expectedBehavior()`
- レポート用に `@DisplayName("human-readable description")`

## カバレッジ (Coverage)

- 行カバレッジ 80%+ を目標
- カバレッジレポートに JaCoCo を使用
- service とドメインロジックに集中 — 自明な getter/設定クラスはスキップ

## 参照 (References)

MockMvc と Testcontainers を使った Spring Boot TDD パターンは skill: `springboot-tdd` を参照。
REST Assured と Dev Services を使った Quarkus TDD パターンは skill: `quarkus-tdd` を参照。
テスト期待値は skill: `java-coding-standards` を参照。
