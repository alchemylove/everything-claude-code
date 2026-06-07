---
description: イディオマティックなパターン、ウィジェットのベストプラクティス、状態管理、パフォーマンス、アクセシビリティ、セキュリティの観点で Flutter/Dart コードをレビュー。flutter-reviewer エージェントを起動する。
---

# Flutter コードレビュー (Flutter Code Review)

このコマンドは **flutter-reviewer** エージェントを起動し、Flutter/Dart コードの変更をレビューする。

## このコマンドの内容 (What This Command Does)

1. **コンテキストの収集**: `git diff --staged` と `git diff` をレビュー
2. **プロジェクトの調査**: `pubspec.yaml`、`analysis_options.yaml`、状態管理ソリューションを確認
3. **セキュリティ事前スキャン**: ハードコードされたシークレットと重大なセキュリティ問題を確認
4. **フルレビュー**: 完全なレビューチェックリストを適用
5. **所見の報告**: severity ごとにグループ化し、修正ガイダンスを出力

## 前提条件 (Prerequisites)

`/flutter-review` を実行する前に:
1. **ビルドが通ること** — 先に `/flutter-build` を実行。壊れたコードのレビューは不完全
2. **テストが通ること** — `/flutter-test` でリグレッションがないことを確認
3. **マージコンフリクトなし** — すべて解決し、diff が意図した変更のみを反映
4. **`flutter analyze` がクリーン** — レビュー前に analyzer 警告を修正

## 使用タイミング (When to Use)

`/flutter-review` を使用する場合:
- Flutter/Dart 変更の PR 提出前（ビルドとテスト通過後）
- 新機能実装後に早期に問題を検出するとき
- 他者の Flutter コードをレビューするとき
- ウィジェット、状態管理コンポーネント、サービスクラスを監査するとき
- 本番リリース前

## レビュー領域 (Review Areas)

| Area | Severity |
|------|----------|
| Hardcoded secrets, cleartext HTTP | CRITICAL |
| Architecture violations, state management anti-patterns | CRITICAL |
| Widget rebuild issues, resource leaks | HIGH |
| Missing `dispose()`, `BuildContext` after await | HIGH |
| Dart null safety, missing error/loading states | HIGH |
| Const propagation, widget composition | HIGH |
| Performance: expensive work in `build()` | HIGH |
| Accessibility, semantic labels | MEDIUM |
| Missing tests for state transitions | HIGH |
| Hardcoded strings (l10n) | MEDIUM |
| Pub dependency hygiene | LOW |

## セッション例 (Example Session)

````text
User: /flutter-review

Agent:
# Flutter Code Review

## Context

Changed files:
- lib/features/auth/presentation/login_page.dart
- lib/features/auth/data/auth_repository_impl.dart

State management: Riverpod (detected from pubspec.yaml)
Architecture: feature-first

## Security Pre-scan

✓ No hardcoded secrets detected
✓ No cleartext HTTP calls

## Review Findings

[HIGH] BuildContext used after async gap without mounted check
File: lib/features/auth/presentation/login_page.dart:67
Issue: `context.go('/home')` called after `await auth.login(...)` with no `mounted` check.
Fix: Add `if (!context.mounted) return;` before any navigation after awaits (Flutter 3.7+).

[HIGH] AsyncValue error state not handled
File: lib/features/auth/presentation/login_page.dart:42
Issue: `ref.watch(authProvider)` switches on loading/data but has no `error` branch.
Fix: Add error case to the switch expression or `when()` call to show a user-facing error message.

[MEDIUM] Hardcoded string not localized
File: lib/features/auth/presentation/login_page.dart:89
Issue: `Text('Login')` — user-visible string not using localization system.
Fix: Use the project's l10n accessor: `Text(context.l10n.loginButton)`.

## Review Summary

| Severity | Count | Status |
|----------|-------|--------|
| CRITICAL | 0     | pass   |
| HIGH     | 2     | block  |
| MEDIUM   | 1     | info   |
| LOW      | 0     | note   |

Verdict: BLOCK — HIGH issues must be fixed before merge.
````

## 承認基準 (Approval Criteria)

- **Approve**: CRITICAL または HIGH の問題なし
- **Block**: CRITICAL または HIGH の問題はマージ前に必須修正

## 関連コマンド (Related Commands)

- `/flutter-build` — 先にビルドエラーを修正
- `/flutter-test` — レビュー前にテストを実行
- `/code-review` — 一般コードレビュー（言語非依存）

## 関連 (Related)

- Agent: `agents/flutter-reviewer.md`
- Skill: `skills/flutter-dart-code-review/`
- Rules: `rules/dart/`
