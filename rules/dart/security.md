---
paths:
  - "**/*.dart"
  - "**/pubspec.yaml"
  - "**/AndroidManifest.xml"
  - "**/Info.plist"
---
# Dart/Flutter セキュリティ (Dart/Flutter Security)

> このファイルは [common/security.md](../common/security.md) を拡張し、Dart、Flutter、およびモバイル固有の内容を追加する。

## シークレット管理 (Secrets Management)

- API key、token、credential を Dart ソースにハードコードしない
- コンパイル時設定には `--dart-define` または `--dart-define-from-file` を使う（値は真の secret ではない — サーバー側 secret には backend proxy を使う）
- `flutter_dotenv` または同等の手段を使い、`.env` ファイルは `.gitignore` に載せる
- 実行時 secret は platform セキュアストレージに保存する: `flutter_secure_storage`（iOS では Keychain、Android では EncryptedSharedPreferences）

```dart
// BAD
const apiKey = 'sk-abc123...';

// GOOD — compile-time config (not secret, just configurable)
const apiKey = String.fromEnvironment('API_KEY');

// GOOD — runtime secret from secure storage
final token = await secureStorage.read(key: 'auth_token');
```

## ネットワークセキュリティ (Network Security)

- HTTPS を強制する — 本番で `http://` 呼び出しはしない
- Android の `network_security_config.xml` で cleartext traffic をブロックする
- `Info.plist` の `NSAppTransportSecurity` で arbitrary loads を禁止する
- すべての HTTP client に request timeout を設定する — デフォルトのままにしない
- 高セキュリティ endpoint では certificate pinning を検討する

```dart
// Dio with timeout and HTTPS enforcement
final dio = Dio(BaseOptions(
  baseUrl: 'https://api.example.com',
  connectTimeout: const Duration(seconds: 10),
  receiveTimeout: const Duration(seconds: 30),
));
```

## 入力検証 (Input Validation)

- API や storage に送る前に、すべての user input を検証・サニタイズする
- 未サニタイズの input を SQL query に渡さない — parameterized query を使う（sqflite、drift）
- ナビゲーション前に deep link URL をサニタイズする — scheme、host、path パラメータを検証する
- ナビゲーション前に `Uri.tryParse` で検証する

```dart
// BAD — SQL injection
await db.rawQuery("SELECT * FROM users WHERE email = '$userInput'");

// GOOD — parameterized
await db.query('users', where: 'email = ?', whereArgs: [userInput]);

// BAD — unvalidated deep link
final uri = Uri.parse(incomingLink);
context.go(uri.path); // could navigate to any route

// GOOD — validated deep link
final uri = Uri.tryParse(incomingLink);
if (uri != null && uri.host == 'myapp.com' && _allowedPaths.contains(uri.path)) {
  context.go(uri.path);
}
```

## データ保護 (Data Protection)

- token、PII、credential は `flutter_secure_storage` にのみ保存する
- 機微データを `SharedPreferences` やローカルファイルに平文で書き込まない
- ログアウト時に auth state をクリアする: token、キャッシュされた user data、cookies
- 機微操作には生体認証（`local_auth`）を使う
- 機微データをログに出さない — `print(token)` や `debugPrint(password)` は禁止

## Android 固有 (Android-Specific)

- `AndroidManifest.xml` では必要な permission のみ宣言する
- Android component（`Activity`、`Service`、`BroadcastReceiver`）は必要な場合のみ export し、不要なら `android:exported="false"` を付ける
- intent filter をレビューする — implicit intent filter を持つ exported component は任意のアプリからアクセス可能
- 機微データを表示する画面には `FLAG_SECURE` を使う（スクリーンショット防止）

```xml
<!-- AndroidManifest.xml — restrict exported components -->
<activity android:name=".MainActivity" android:exported="true">
    <!-- Only the launcher activity needs exported=true -->
</activity>
<activity android:name=".SensitiveActivity" android:exported="false" />
```

## iOS 固有 (iOS-Specific)

- `Info.plist` では必要な usage description のみ宣言する（`NSCameraUsageDescription` など）
- secret は Keychain に保存する — `flutter_secure_storage` は iOS で Keychain を使う
- App Transport Security（ATS）を使う — arbitrary loads を禁止する
- 機微ファイルには data protection entitlement を有効にする

## WebView セキュリティ (WebView Security)

- `webview_flutter` v4+（`WebViewController` / `WebViewWidget`）を使う — レガシーの `WebView` widget は削除されている
- 明示的に必要でない限り JavaScript を無効にする（`JavaScriptMode.disabled`）
- 読み込み前に URL を検証する — deep link から任意 URL を読み込まない
- 絶対に必要で慎重に sandbox 化しない限り、JavaScript に Dart callback を公開しない
- `NavigationDelegate.onNavigationRequest` で navigation request を intercept し検証する

```dart
// webview_flutter v4+ API (WebViewController + WebViewWidget)
final controller = WebViewController()
  ..setJavaScriptMode(JavaScriptMode.disabled) // disabled unless required
  ..setNavigationDelegate(
    NavigationDelegate(
      onNavigationRequest: (request) {
        final uri = Uri.tryParse(request.url);
        if (uri == null || uri.host != 'trusted.example.com') {
          return NavigationDecision.prevent;
        }
        return NavigationDecision.navigate;
      },
    ),
  );

// In your widget tree:
WebViewWidget(controller: controller)
```

## 難読化とビルドセキュリティ (Obfuscation and Build Security)

- release build では難読化を有効にする: `flutter build apk --obfuscate --split-debug-info=./debug-info/`
- `--split-debug-info` の出力は version control に含めない（crash symbolication 専用）
- ProGuard/R8 ルールが serialized class を意図せず露出していないことを確認する
- release 前に `flutter analyze` を実行し、すべての warning に対処する
