---
paths:
  - "**/*.kt"
  - "**/*.kts"
---
# Kotlin セキュリティ (Kotlin Security)

> このファイルは [common/security.md](../common/security.md) を拡張し、Kotlin および Android/KMP 固有の内容を追加する。

## シークレット管理 (Secrets Management)

- API キー、トークン、認証情報をソースコードにハードコードしない
- ローカル開発のシークレットには `local.properties`（git 無視）を使用
- リリースビルドには CI シークレットから生成した `BuildConfig` フィールドを使用
- ランタイムのシークレット保存には `EncryptedSharedPreferences`（Android）または Keychain（iOS）を使用

```kotlin
// BAD
val apiKey = "sk-abc123..."

// GOOD — from BuildConfig (generated at build time)
val apiKey = BuildConfig.API_KEY

// GOOD — from secure storage at runtime
val token = secureStorage.get("auth_token")
```

## ネットワークセキュリティ (Network Security)

- HTTPS のみを使用 — `network_security_config.xml` で平文通信をブロック
- 機微なエンドポイントには OkHttp `CertificatePinner` または Ktor 相当で証明書ピンニング
- すべての HTTP クライアントにタイムアウトを設定 — デフォルト（無限の可能性あり）のままにしない
- 使用前にすべてのサーバーレスポンスを検証・サニタイズ

```xml
<!-- res/xml/network_security_config.xml -->
<network-security-config>
    <base-config cleartextTrafficPermitted="false" />
</network-security-config>
```

## 入力検証 (Input Validation)

- 処理または API 送信前にすべてのユーザー入力を検証
- Room/SQLDelight ではパラメータ化クエリを使用 — ユーザー入力を SQL に連結しない
- パストラバーサル防止のためユーザー入力のファイルパスをサニタイズ

```kotlin
// BAD — SQL injection
@Query("SELECT * FROM items WHERE name = '$input'")

// GOOD — parameterized
@Query("SELECT * FROM items WHERE name = :input")
fun findByName(input: String): List<ItemEntity>
```

## データ保護 (Data Protection)

- Android 上の機微なキー・値データには `EncryptedSharedPreferences` を使用
- `@Serializable` で明示的なフィールド名を使用 — 内部プロパティ名を漏らさない
- 不要になった機微データはメモリからクリア
- シリアライズクラスの名前マングリング防止に `@Keep` または ProGuard ルールを使用

## 認証 (Authentication)

- トークンは平文 SharedPreferences ではなくセキュアストレージに保存
- 適切な 401/403 処理でトークンリフレッシュを実装
- ログアウト時にすべての認証状態をクリア（トークン、キャッシュされたユーザーデータ、Cookie）
- 機微な操作には生体認証（`BiometricPrompt`）を使用

## ProGuard / R8

- すべてのシリアライズモデル（`@Serializable`、Gson、Moshi）の keep ルール
- リフレクションベースのライブラリ（Koin、Retrofit）の keep ルール
- リリースビルドをテスト — 難読化がシリアライズを静かに壊すことがある

## WebView セキュリティ (WebView Security)

- 明示的に必要でない限り JavaScript を無効: `settings.javaScriptEnabled = false`
- WebView に読み込む前に URL を検証
- 機微データにアクセスする `@JavascriptInterface` メソッドを公開しない
- ナビゲーション制御に `WebViewClient.shouldOverrideUrlLoading()` を使用
