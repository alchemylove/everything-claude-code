---
description: Flutter/Dart テストを実行し、失敗を報告し、テスト問題を段階的に修正。unit、widget、golden、integration テストをカバー。
---

# Flutter テスト (Flutter Test)

このコマンドは Flutter テストスイートを実行し、結果を報告する。失敗時は段階的に診断・修正する。

## このコマンドの内容 (What This Command Does)

1. **テストの実行**: `flutter test` を実行（または変更ファイルにスコープ）
2. **失敗の解析**: 種別と原因で失敗テストを特定
3. **段階的修正**: 可能な限り一度に 1 失敗ずつ
4. **検証**: 各修正後に再実行
5. **報告**: カバレッジサマリーと残りの失敗を表示

## 使用タイミング (When to Use)

`/flutter-test` を使用する場合:
- 機能実装後に何も壊れていないか確認するとき
- `/flutter-build` 実行後にテストが通ることを確認するとき
- 新規コードのテストカバレッジを確認するとき
- 特定のテストファイルが失敗しているとき
- PR 提出前

## 実行コマンド (Commands Run)

```bash
# Run all tests
flutter test 2>&1

# Run with coverage
flutter test --coverage 2>&1

# Run specific test file
flutter test test/unit/domain/usecases/get_user_test.dart 2>&1

# Run tests matching a name pattern
flutter test --name "CartBloc" 2>&1

# Run integration tests (requires device/emulator)
flutter test integration_test/ 2>&1

# Update golden files when intentional visual changes are made
flutter test --update-goldens 2>&1
```

## セッション例 (Example Session)

````text
User: /flutter-test

Agent:
# Flutter Test Run

## Test Execution

```
$ flutter test
00:05 +42 -2: Some tests failed.

FAILED: test/unit/data/user_repository_test.dart
  UserRepository getById
    returns null when user not found
    Expected: null
    Actual: User(id: 'test-id', name: 'Test User')

FAILED: test/widget/cart_page_test.dart
  CartPage
    shows empty state when cart is empty
    Expected: exactly one widget with text 'Your cart is empty'
    Found: no widget with text 'Your cart is empty'
```

## Fix 1: Repository Test

File: test/unit/data/user_repository_test.dart
Failure: `getById` returns a user when it should return null for missing ID

Root cause: Test setup adds a user with ID 'test-id' but queries with 'missing-id'.
Fix: Updated test to query with 'missing-id' — setup was correct, query was wrong.

```
$ flutter test test/unit/data/user_repository_test.dart
1 test passed.
```

## Fix 2: Widget Test

File: test/widget/cart_page_test.dart
Failure: Empty state text widget not found

Root cause: Empty state message was renamed from 'Your cart is empty' to 'Cart is empty' in the widget.
Fix: Updated test string to match current widget copy.

```
$ flutter test test/widget/cart_page_test.dart
1 test passed.
```

## Final Run

```
$ flutter test --coverage
All 44 tests passed.
Coverage: 84.2% (target: 80%)
```

## Summary

| Metric | Value |
|--------|-------|
| Total tests | 44 |
| Passed | 44 |
| Failed | 0 |
| Coverage | 84.2% |

Test Status: PASS ✓
````

## よくあるテスト失敗 (Common Test Failures)

| Failure | Typical Fix |
|---------|-------------|
| `Expected: <X> Actual: <Y>` | アサーションを更新するか実装を修正 |
| `Widget not found` | finder セレクタを修正するかウィジェット改名後にテストを更新 |
| `Golden file not found` | `flutter test --update-goldens` で生成 |
| `Golden mismatch` | diff を確認。意図した変更なら `--update-goldens` を実行 |
| `MissingPluginException` | テストセットアップでプラットフォームチャネルをモック |
| `LateInitializationError` | `setUp()` で `late` フィールドを初期化 |
| `pumpAndSettle timed out` | 明示的な `pump(Duration)` 呼び出しに置き換え |

## 関連コマンド (Related Commands)

- `/flutter-build` — テスト実行前にビルドエラーを修正
- `/flutter-review` — テスト通過後にコードをレビュー
- `tdd-workflow` skill — テスト駆動開発ワークフロー

## 関連 (Related)

- Agent: `agents/flutter-reviewer.md`
- Agent: `agents/dart-build-resolver.md`
- Skill: `skills/flutter-dart-code-review/`
- Rules: `rules/dart/testing.md`
