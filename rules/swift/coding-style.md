---
paths:
  - "**/*.swift"
  - "**/Package.swift"
---
# Swift コーディングスタイル (Swift Coding Style)

> このファイルは [common/coding-style.md](../common/coding-style.md) を拡張し、Swift 固有の内容を追加する。

## フォーマット (Formatting)

- 自動フォーマットには **SwiftFormat**、スタイル強制には **SwiftLint**
- Xcode 16+ では代替として `swift-format` が同梱されている

## 不変性 (Immutability)

- `var` より `let` を優先 — すべて `let` で定義し、コンパイラが要求するときだけ `var` に変更する
- デフォルトは value semantics の `struct`。identity または参照 semantics が必要なときだけ `class` を使用する

## 命名 (Naming)

[Apple API Design Guidelines](https://www.swift.org/documentation/api-design-guidelines/) に従う:

- 使用箇所での明確さ — 不要な語を省略する
- 型ではなく役割に基づいてメソッドとプロパティに名前を付ける
- グローバル定数より `static let` を定数に使用する

## エラーハンドリング (Error Handling)

型付き throws（Swift 6+）とパターンマッチングを使用する:

```swift
func load(id: String) throws(LoadError) -> Item {
    guard let data = try? read(from: path) else {
        throw .fileNotFound(id)
    }
    return try decode(data)
}
```

## 並行性 (Concurrency)

Swift 6 の strict concurrency checking を有効にする。以下を優先する:

- isolation 境界を跨ぐデータには `Sendable` value type
- 共有 mutable 状態には actor
- 非構造化 `Task {}` より structured concurrency（`async let`、`TaskGroup`）
