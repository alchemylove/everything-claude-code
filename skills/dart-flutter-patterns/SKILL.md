---
name: dart-flutter-patterns
description: null safety、immutable state、async composition、widget architecture、BLoC/Riverpod/Provider、GoRouter、Dio、Freezed、clean architecture を含む本番向け Dart/Flutter パターン。Flutter, Dart, BLoC, Riverpod.
origin: ECC
---

# Dart/Flutterパターン (Dart/Flutter Patterns)

## 使用時期 (When to Use)

次の場合にこのスキルを使用：
- 新しいFlutter機能を開始し、状態管理、ナビゲーション、またはデータアクセスのイディオマティックパターンが必要
- Dartコードのレビューまたは作成とnull安全性、シール型、非同期構成のガイダンスが必要
- 新しいFlutterプロジェクトをセットアップしBLoC、Riverpod、またはProviderのうち選択
- 安全なHTTPクライアント、WebView統合、ローカルストレージを実装
- Flutterウィジェット、Cubit、または Riverpod プロバイダーのテストを作成
- GoRouterを認証ガードでワイヤリング

## 動作方法 (How It Works)

このスキルは、懸念事項で整理されたコピーペーストの準備ができたDart/Flutterコードパターンを提供：
1. **Null安全性** — `!`を避ける、`?.`/`??`/パターンマッチングを好む
2. **不変状態** — シール型、`freezed`、`copyWith`
3. **非同期構成** — 並行`Future.wait`、`await`後の安全な`BuildContext`
4. **ウィジェットアーキテクチャ** — クラスに抽出（メソッドではなく）、`const`伝播、スコープ付きリビルド
5. **状態管理** — BLoC/Cubit イベント、Riverpod ノーティファイアおよび派生プロバイダー
6. **ナビゲーション** — `refreshListenable`経由の反応型認証ガード付きGoRouter
7. **ネットワーキング** — インターセプタ付きDio、ワンタイム再試行ガード付きトークンリフレッシュ
8. **エラーハンドリング** — グローバルキャプチャ、`ErrorWidget.builder`、crashlyticsワイヤリング
9. **テスト** — ユニット（BLoC test）、ウィジェット（ProviderScopeオーバーライド）、モック上のフェイク

## 例 (Examples)

```dart
// シール状態 — 不可能な状態を防止
sealed class AsyncState<T> {}
final class Loading<T> extends AsyncState<T> {}
final class Success<T> extends AsyncState<T> { final T data; const Success(this.data); }
final class Failure<T> extends AsyncState<T> { final Object error; const Failure(this.error); }

// 反応型認証リダイレクト付きGoRouter
final router = GoRouter(
  refreshListenable: GoRouterRefreshStream(authCubit.stream),
  redirect: (context, state) {
    final authed = context.read<AuthCubit>().state is AuthAuthenticated;
    if (!authed && !state.matchedLocation.startsWith('/login')) return '/login';
    return null;
  },
  routes: [...],
);
```

詳細については、ドキュメントを参照してください。
