---
paths:
  - "**/*.swift"
  - "**/Package.swift"
---
# Swift テスト (Swift Testing)

> このファイルは [common/testing.md](../common/testing.md) を拡張し、Swift 固有の内容を追加する。

## フレームワーク (Framework)

新規テストには **Swift Testing**（`import Testing`）を使用する。`@Test` と `#expect` を使う:

```swift
@Test("User creation validates email")
func userCreationValidatesEmail() throws {
    #expect(throws: ValidationError.invalidEmail) {
        try User(email: "not-an-email")
    }
}
```

## テスト分離 (Test Isolation)

各テストは新しいインスタンスを得る — `init` でセットアップ、`deinit` で tear down。テスト間で共有 mutable 状態を持たない。

## パラメータ化テスト (Parameterized Tests)

```swift
@Test("Validates formats", arguments: ["json", "xml", "csv"])
func validatesFormat(format: String) throws {
    let parser = try Parser(format: format)
    #expect(parser.isValid)
}
```

## カバレッジ (Coverage)

```bash
swift test --enable-code-coverage
```

## 参照 (Reference)

protocol ベースの依存性注入と Swift Testing による mock パターンは skill: `swift-protocol-di-testing` を参照。
