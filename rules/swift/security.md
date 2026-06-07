---
paths:
  - "**/*.swift"
  - "**/Package.swift"
---
# Swift セキュリティ (Swift Security)

> このファイルは [common/security.md](../common/security.md) を拡張し、Swift 固有の内容を追加する。

## シークレット管理 (Secret Management)

- 機微データ（トークン、パスワード、鍵）には **Keychain Services** を使用する — `UserDefaults` は使わない
- ビルド時シークレットには環境変数または `.xcconfig` ファイルを使用する
- ソースにシークレットをハードコードしない — 逆コンパイルツールで容易に抽出される

```swift
let apiKey = ProcessInfo.processInfo.environment["API_KEY"]
guard let apiKey, !apiKey.isEmpty else {
    fatalError("API_KEY not configured")
}
```

## transport セキュリティ (Transport Security)

- App Transport Security (ATS) はデフォルトで強制される — 無効化しない
- 重要なエンドポイントには certificate pinning を使用する
- すべてのサーバー証明書を検証する

## 入力検証 (Input Validation)

- インジェクション防止のため、表示前にすべてのユーザー入力をサニタイズする
- force unwrap ではなく検証付き `URL(string:)` を使用する
- 処理前に外部ソース（API、deep link、pasteboard）からのデータを検証する
