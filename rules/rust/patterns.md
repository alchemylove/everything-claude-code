---
paths:
  - "**/*.rs"
---
# Rust パターン (Rust Patterns)

> このファイルは [common/patterns.md](../common/patterns.md) を拡張し、Rust 固有の内容を追加する。

## Trait による Repository パターン (Repository Pattern with Traits)

データアクセスを trait の背後にカプセル化:

```rust
pub trait OrderRepository: Send + Sync {
    fn find_by_id(&self, id: u64) -> Result<Option<Order>, StorageError>;
    fn find_all(&self) -> Result<Vec<Order>, StorageError>;
    fn save(&self, order: &Order) -> Result<Order, StorageError>;
    fn delete(&self, id: u64) -> Result<(), StorageError>;
}
```

具象実装がストレージの詳細（Postgres、SQLite、テスト用インメモリ）を扱う。

## Service レイヤー (Service Layer)

ビジネスロジックは service struct に。コンストラクタで依存を注入:

```rust
pub struct OrderService {
    repo: Box<dyn OrderRepository>,
    payment: Box<dyn PaymentGateway>,
}

impl OrderService {
    pub fn new(repo: Box<dyn OrderRepository>, payment: Box<dyn PaymentGateway>) -> Self {
        Self { repo, payment }
    }

    pub fn place_order(&self, request: CreateOrderRequest) -> anyhow::Result<OrderSummary> {
        let order = Order::from(request);
        self.payment.charge(order.total())?;
        let saved = self.repo.save(&order)?;
        Ok(OrderSummary::from(saved))
    }
}
```

## 型安全性のための Newtype パターン (Newtype Pattern for Type Safety)

別々のラッパー型で引数の取り違えを防ぐ:

```rust
struct UserId(u64);
struct OrderId(u64);

fn get_order(user: UserId, order: OrderId) -> anyhow::Result<Order> {
    // Can't accidentally swap user and order IDs at call sites
    todo!()
}
```

## Enum 状態マシン (Enum State Machines)

状態を enum でモデル化 — 不正な状態を表現不能にする:

```rust
enum ConnectionState {
    Disconnected,
    Connecting { attempt: u32 },
    Connected { session_id: String },
    Failed { reason: String, retries: u32 },
}

fn handle(state: &ConnectionState) {
    match state {
        ConnectionState::Disconnected => connect(),
        ConnectionState::Connecting { attempt } if *attempt > 3 => abort(),
        ConnectionState::Connecting { .. } => wait(),
        ConnectionState::Connected { session_id } => use_session(session_id),
        ConnectionState::Failed { retries, .. } if *retries < 5 => retry(),
        ConnectionState::Failed { reason, .. } => log_failure(reason),
    }
}
```

ビジネスクリティカルな enum は常に網羅的に match — ワイルドカード `_` は使わない。

## Builder パターン (Builder Pattern)

多くのオプションパラメータを持つ struct に使用:

```rust
pub struct ServerConfig {
    host: String,
    port: u16,
    max_connections: usize,
}

impl ServerConfig {
    pub fn builder(host: impl Into<String>, port: u16) -> ServerConfigBuilder {
        ServerConfigBuilder {
            host: host.into(),
            port,
            max_connections: 100,
        }
    }
}

pub struct ServerConfigBuilder {
    host: String,
    port: u16,
    max_connections: usize,
}

impl ServerConfigBuilder {
    pub fn max_connections(mut self, n: usize) -> Self {
        self.max_connections = n;
        self
    }

    pub fn build(self) -> ServerConfig {
        ServerConfig {
            host: self.host,
            port: self.port,
            max_connections: self.max_connections,
        }
    }
}
```

## 拡張性制御のための Sealed Trait (Sealed Traits for Extensibility Control)

private モジュールで trait を seal し、外部実装を防ぐ:

```rust
mod private {
    pub trait Sealed {}
}

pub trait Format: private::Sealed {
    fn encode(&self, data: &[u8]) -> Vec<u8>;
}

pub struct Json;
impl private::Sealed for Json {}
impl Format for Json {
    fn encode(&self, data: &[u8]) -> Vec<u8> { todo!() }
}
```

## API レスポンスエンベロープ (API Response Envelope)

汎用 enum による一貫した API レスポンス:

```rust
#[derive(Debug, serde::Serialize)]
#[serde(tag = "status")]
pub enum ApiResponse<T: serde::Serialize> {
    #[serde(rename = "ok")]
    Ok { data: T },
    #[serde(rename = "error")]
    Error { message: String },
}
```

## 参照 (References)

所有権、trait、ジェネリクス、並行性、async を含む包括的パターンは skill: `rust-patterns` を参照。
