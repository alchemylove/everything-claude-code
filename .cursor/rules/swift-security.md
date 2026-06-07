---
description: "Swift security: common ルールの拡張"
globs: ["**/*.swift", "**/Package.swift"]
alwaysApply: false
---
# Swift Security (Swift Security)

> このファイルは common security ルールを Swift 固有の内容で拡張する。

## Secret 管理 (Secret Management)

- 機微データ（token、password、key）には **Keychain Services** を使用 — `UserDefaults` は使わない
- build 時 secret には環境変数または `.xcconfig` ファイルを使用
- ソースに secret をハードコードしない — decompilation tool で容易に抽出される

```swift
let apiKey = ProcessInfo.processInfo.environment["API_KEY"]
guard let apiKey, !apiKey.isEmpty else {
    fatalError("API_KEY not configured")
}
```

## Transport Security

- App Transport Security (ATS) はデフォルトで強制 — 無効化しない
- 重要な endpoint には certificate pinning を使用
- すべての server certificate をバリデーション

## 入力バリデーション (Input Validation)

- injection 防止のため表示前にすべてのユーザー入力を sanitize
- force-unwrap ではなくバリデーション付き `URL(string:)` を使用
- 処理前に外部ソース（API、deep link、pasteboard）のデータをバリデーション
