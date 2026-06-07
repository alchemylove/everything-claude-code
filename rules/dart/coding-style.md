---
paths:
  - "**/*.dart"
  - "**/pubspec.yaml"
  - "**/analysis_options.yaml"
---
# Dart/Flutter コーディングスタイル (Dart/Flutter Coding Style)

> このファイルは [common/coding-style.md](../common/coding-style.md) を拡張し、Dart および Flutter 固有の内容を追加する。

## フォーマット (Formatting)

- すべての `.dart` ファイルに **dart format** を使う — CI で強制（`dart format --set-exit-if-changed .`）
- 行長: 80 文字（dart format のデフォルト）
- 複数行の引数/パラメータリストには末尾カンマを付け、diff とフォーマットを改善する

## 不変性 (Immutability)

- ローカル変数には `final`、コンパイル時定数には `const` を優先する
- すべてのフィールドが `final` の場合は `const` コンストラクタを使う
- public API からは変更不可コレクションを返す（`List.unmodifiable`、`Map.unmodifiable`）
- 不変 state クラスの state 変更には `copyWith()` を使う

```dart
// BAD
var count = 0;
List<String> items = ['a', 'b'];

// GOOD
final count = 0;
const items = ['a', 'b'];
```

## 命名 (Naming)

Dart の規約に従う:
- 変数、パラメータ、named constructor には `camelCase`
- クラス、enum、typedef、extension には `PascalCase`
- ファイル名とライブラリ名には `snake_case`
- トップレベルの `const` 定数には `SCREAMING_SNAKE_CASE`
- private メンバーには `_` プレフィックス
- extension 名は拡張する型を表す: `StringExtensions` であり `MyHelpers` ではない

## Null Safety

- `!`（bang operator）は避ける — `?.`、`??`、`if (x != null)`、または Dart 3 の pattern matching を優先する。`!` は null がプログラミングエラーでありクラッシュが正しい挙動の場合のみ使う
- 初回使用前に初期化が保証されない限り `late` は避ける（nullable または constructor init を優先）
- 常に提供される必要がある constructor パラメータには `required` を使う

```dart
// BAD — crashes at runtime if user is null
final name = user!.name;

// GOOD — null-aware operators
final name = user?.name ?? 'Unknown';

// GOOD — Dart 3 pattern matching (exhaustive, compiler-checked)
final name = switch (user) {
  User(:final name) => name,
  null => 'Unknown',
};

// GOOD — early-return null guard
String getUserName(User? user) {
  if (user == null) return 'Unknown';
  return user.name; // promoted to non-null after the guard
}
```

## Sealed Types と Pattern Matching（Dart 3+）(Sealed Types and Pattern Matching (Dart 3+))

closed state 階層をモデル化するには sealed class を使う:

```dart
sealed class AsyncState<T> {
  const AsyncState();
}

final class Loading<T> extends AsyncState<T> {
  const Loading();
}

final class Success<T> extends AsyncState<T> {
  const Success(this.data);
  final T data;
}

final class Failure<T> extends AsyncState<T> {
  const Failure(this.error);
  final Object error;
}
```

sealed types では常に網羅的な `switch` を使う — default/wildcard は使わない:

```dart
// BAD
if (state is Loading) { ... }

// GOOD
return switch (state) {
  Loading() => const CircularProgressIndicator(),
  Success(:final data) => DataWidget(data),
  Failure(:final error) => ErrorWidget(error.toString()),
};
```

## エラーハンドリング (Error Handling)

- `on` 句で exception 型を指定する — 裸の `catch (e)` は使わない
- `Error` サブタイプは catch しない — プログラミングバグを示す
- 回復可能なエラーには `Result` スタイルの型または sealed class を使う
- 制御フローに exception を使わない

```dart
// BAD
try {
  await fetchUser();
} catch (e) {
  log(e.toString());
}

// GOOD
try {
  await fetchUser();
} on NetworkException catch (e) {
  log('Network error: ${e.message}');
} on NotFoundException {
  handleNotFound();
}
```

## Async / Futures

- Future は常に `await` するか、意図的な fire-and-forget であることを示すために明示的に `unawaited()` を呼ぶ
- 何も `await` しない関数を `async` にしない
- 並行操作には `Future.wait` / `Future.any` を使う
- 任意の `await` の後に `BuildContext` を使う前に `context.mounted` を確認する（Flutter 3.7+）

```dart
// BAD — ignoring Future
fetchData(); // fire-and-forget without marking intent

// GOOD
unawaited(fetchData()); // explicit fire-and-forget
await fetchData();      // or properly awaited
```

## インポート (Imports)

- 全体で `package:` import を使う — cross-feature や cross-layer のコードに relative import（`../`）は使わない
- 順序: `dart:` → 外部 `package:` → 内部 `package:`（同一 package）
- 未使用 import は禁止 — `dart analyze` が `unused_import` で強制する

## コード生成 (Code Generation)

- 生成ファイル（`.g.dart`、`.freezed.dart`、`.gr.dart`）はコミットするか gitignore するかを一貫させる — プロジェクトごとに 1 つの戦略を選ぶ
- 生成ファイルを手動編集しない
- generator アノテーション（`@JsonSerializable`、`@freezed`、`@riverpod` など）は canonical source ファイルのみに置く
