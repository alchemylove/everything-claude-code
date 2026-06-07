---
description: Dart analyzer エラーと Flutter ビルド失敗を段階的に修正。最小限の外科的修正のため dart-build-resolver エージェントを起動する。
---

# Flutter ビルドと修正 (Flutter Build and Fix)

このコマンドは **dart-build-resolver** エージェントを起動し、最小限の変更で Dart/Flutter ビルドエラーを段階的に修正する。

## このコマンドの内容 (What This Command Does)

1. **診断の実行**: `flutter analyze`、`flutter pub get` を実行
2. **エラーの解析**: ファイルごとにグループ化し、severity でソート
3. **段階的修正**: 一度に 1 エラーずつ
4. **各修正の検証**: 変更のたびに解析を再実行
5. **サマリーの報告**: 修正内容と残件を表示

## 使用タイミング (When to Use)

`/flutter-build` を使用する場合:
- `flutter analyze` がエラーを報告する
- 任意のプラットフォームで `flutter build` が失敗する
- `dart pub get` / `flutter pub get` がバージョン競合で失敗する
- `build_runner` がコード生成に失敗する
- pull 後にビルドが壊れた場合

## 実行される診断コマンド (Diagnostic Commands Run)

```bash
# Analysis
flutter analyze 2>&1

# Dependencies
flutter pub get 2>&1

# Code generation (if project uses build_runner)
dart run build_runner build --delete-conflicting-outputs 2>&1

# Platform builds
flutter build apk 2>&1
flutter build web 2>&1
```

## セッション例 (Example Session)

````text
User: /flutter-build

Agent:
# Flutter Build Resolution

## Initial Diagnostics

```
$ flutter analyze
Analyzing project...

  error • lib/features/user/data/user_repository_impl.dart:34:16 •
    A value of type 'String?' can't be assigned to a variable of type 'String'. •
    invalid_assignment

  error • lib/features/cart/presentation/cart_page.dart:58:5 •
    The method 'add' isn't defined for the type 'List<Item>'.
    Try correcting the name to the name of an existing method, or defining a method named 'add'. •
    undefined_method

2 errors found.
```

## Fix 1: Null Safety

File: lib/features/user/data/user_repository_impl.dart:34
Error: A value of type 'String?' can't be assigned to type 'String'

Changed:
```dart
final id = response.id;
```
To:
```dart
final id = response.id ?? '';
```

```
$ flutter analyze
1 error found.
```

## Fix 2: Immutable List

File: lib/features/cart/presentation/cart_page.dart:58
Error: The method 'add' isn't defined for the type 'List<Item>'
Cause: State holds an unmodifiable list; mutation goes through Cubit

Changed:
```dart
state.items.add(item);
```
To:
```dart
context.read<CartCubit>().addItem(item);
// Note: Cubit exposes named methods (addItem, removeItem);
// .add(event) is the BLoC event API — don't mix them.
```

```
$ flutter analyze
No issues found!
```

## Final Verification

```
$ flutter test
All tests passed.
```

## Summary

| Metric | Count |
|--------|-------|
| Analysis errors fixed | 2 |
| Files modified | 2 |
| Remaining issues | 0 |

Build Status: PASS ✓
````

## よくある修正対象エラー (Common Errors Fixed)

| Error | Typical Fix |
|-------|-------------|
| `A value of type 'X?' can't be assigned to 'X'` | `?? default` または null ガードを追加 |
| `The name 'X' isn't defined` | import を追加するか typo を修正 |
| `Non-nullable instance field must be initialized` | 初期化子または `late` を追加 |
| `Version solving failed` | pubspec.yaml のバージョン制約を調整 |
| `Missing concrete implementation of 'X'` | 不足しているインターフェースメソッドを実装 |
| `build_runner: Part of X expected` | 古い `.g.dart` を削除して再ビルド |

## 修正戦略 (Fix Strategy)

1. **解析エラーを最優先** — コードはエラーフリーであること
2. **警告のトリアージを次に** — ランタイムバグの原因になりうる警告を修正
3. **pub 競合を 3 番目** — 依存関係解決を修正
4. **一度に 1 修正** — 各変更を検証
5. **最小限の変更** — リファクタリングせず、修正のみ

## 停止条件 (Stop Conditions)

エージェントは以下の場合に停止して報告する:
- 3 回試行後も同じエラーが続く
- 修正がさらに多くのエラーを導入する
- アーキテクチャ変更が必要
- パッケージアップグレード競合でユーザーの判断が必要

## 関連コマンド (Related Commands)

- `/flutter-test` — ビルド成功後にテストを実行
- `/flutter-review` — コード品質をレビュー
- `verification-loop` skill — フル検証ループ

## 関連 (Related)

- Agent: `agents/dart-build-resolver.md`
- Skill: `skills/flutter-dart-code-review/`
