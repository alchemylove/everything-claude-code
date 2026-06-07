---
description: "Swift patterns: common ルールの拡張"
globs: ["**/*.swift", "**/Package.swift"]
alwaysApply: false
---
# Swift Patterns (Swift Patterns)

> このファイルは common patterns ルールを Swift 固有の内容で拡張する。

## Protocol-Oriented Design

小さく焦点を絞った protocol を定義。共有デフォルトには protocol extension を使用:

```swift
protocol Repository: Sendable {
    associatedtype Item: Identifiable & Sendable
    func find(by id: Item.ID) async throws -> Item?
    func save(_ item: Item) async throws
}
```

## Value Types

- data transfer object と model には struct を使用
- 異なる状態をモデル化するには associated value 付き enum を使用:

```swift
enum LoadState<T: Sendable>: Sendable {
    case idle
    case loading
    case loaded(T)
    case failed(Error)
}
```

## Actor Pattern

lock や dispatch queue の代わりに共有 mutable state には actor を使用:

```swift
actor Cache<Key: Hashable & Sendable, Value: Sendable> {
    private var storage: [Key: Value] = [:]

    func get(_ key: Key) -> Value? { storage[key] }
    func set(_ key: Key, value: Value) { storage[key] = value }
}
```

## Dependency Injection

default parameter で protocol を注入 — 本番はデフォルト、test は mock を注入:

```swift
struct UserService {
    private let repository: any UserRepository

    init(repository: any UserRepository = DefaultUserRepository()) {
        self.repository = repository
    }
}
```

## 参照 (References)

actor ベースの persistence パターンは skill: `swift-actor-persistence` を参照。
protocol ベースの DI と testing は skill: `swift-protocol-di-testing` を参照。
