---
description: "Swift testing: common ルールの拡張"
globs: ["**/*.swift", "**/Package.swift"]
alwaysApply: false
---
# Swift Testing (Swift Testing)

> このファイルは common testing ルールを Swift 固有の内容で拡張する。

## Framework

新規 test には **Swift Testing**（`import Testing`）を使用。`@Test` と `#expect` を使用:

```swift
@Test("User creation validates email")
func userCreationValidatesEmail() throws {
    #expect(throws: ValidationError.invalidEmail) {
        try User(email: "not-an-email")
    }
}
```

## Test Isolation

各 test は新しいインスタンスを取得 — `init` で set up、`deinit` で tear down。test 間で共有 mutable state を持たない。

## Parameterized Tests

```swift
@Test("Validates formats", arguments: ["json", "xml", "csv"])
func validatesFormat(format: String) throws {
    let parser = try Parser(format: format)
    #expect(parser.isValid)
}
```

## Coverage

```bash
swift test --enable-code-coverage
```

## 参照 (Reference)

Swift Testing による protocol ベースの dependency injection と mock パターンは skill: `swift-protocol-di-testing` を参照。
