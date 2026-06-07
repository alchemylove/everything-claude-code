---
description: "Swift coding style: common ルールの拡張"
globs: ["**/*.swift", "**/Package.swift"]
alwaysApply: false
---
# Swift Coding Style (Swift Coding Style)

> このファイルは common coding style ルールを Swift 固有の内容で拡張する。

## フォーマット (Formatting)

- auto-format に **SwiftFormat**、スタイル強制に **SwiftLint**
- Xcode 16+ では代替として `swift-format` が同梱

## 不変性 (Immutability)

- `var` より `let` を優先 — すべて `let` で定義し、compiler が要求する場合のみ `var` に変更
- デフォルトは value semantics の `struct`。identity または reference semantics が必要な場合のみ `class`

## 命名 (Naming)

[Apple API Design Guidelines](https://www.swift.org/documentation/api-design-guidelines/) に従う:

- 使用箇所での明瞭さ — 不要な語を省略
- 型ではなく役割に基づいてメソッドと property に命名
- グローバル定数より `static let` を使用

## エラーハンドリング (Error Handling)

typed throws（Swift 6+）と pattern matching を使用:

```swift
func load(id: String) throws(LoadError) -> Item {
    guard let data = try? read(from: path) else {
        throw .fileNotFound(id)
    }
    return try decode(data)
}
```

## 並行処理 (Concurrency)

Swift 6 strict concurrency checking を有効化。優先:

- isolation 境界を越えるデータには `Sendable` value type
- 共有 mutable state には actor
- 非構造化 `Task {}` より structured concurrency（`async let`, `TaskGroup`）
