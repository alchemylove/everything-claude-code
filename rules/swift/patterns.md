---
paths:
  - "**/*.swift"
  - "**/Package.swift"
---
# Swift パターン (Swift Patterns)

> このファイルは [common/patterns.md](../common/patterns.md) を拡張し、Swift 固有の内容を追加する。

## プロトコル指向設計 (Protocol-Oriented Design)

小さく焦点を絞った protocol を定義する。共有デフォルトには protocol extension を使用する:

```swift
protocol Repository: Sendable {
    associatedtype Item: Identifiable & Sendable
    func find(by id: Item.ID) async throws -> Item?
    func save(_ item: Item) async throws
}
```

## value type (Value Types)

- データ転送オブジェクトとモデルには struct を使用する
- 関連値付き enum で distinct な状態をモデル化する:

```swift
enum LoadState<T: Sendable>: Sendable {
    case idle
    case loading
    case loaded(T)
    case failed(Error)
}
```

## actor パターン (Actor Pattern)

lock や dispatch queue の代わりに、共有 mutable 状態には actor を使用する:

```swift
actor Cache<Key: Hashable & Sendable, Value: Sendable> {
    private var storage: [Key: Value] = [:]

    func get(_ key: Key) -> Value? { storage[key] }
    func set(_ key: Key, value: Value) { storage[key] = value }
}
```

## 依存性注入 (Dependency Injection)

デフォルト引数付きで protocol を注入する — 本番はデフォルト、テストは mock を注入する:

```swift
struct UserService {
    private let repository: any UserRepository

    init(repository: any UserRepository = DefaultUserRepository()) {
        self.repository = repository
    }
}
```

## 参照 (References)

actor ベースの永続化パターンは skill: `swift-actor-persistence` を参照。
protocol ベース DI とテストは skill: `swift-protocol-di-testing` を参照。
