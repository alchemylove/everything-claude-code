---
description: Kotlin/Gradle ビルドエラー、コンパイラ警告、依存関係の問題をインクリメンタルに修正する。最小限の外科的修正のために kotlin-build-resolver agent を呼び出す。
---

# Kotlin ビルドと修正 (Kotlin Build and Fix)

このコマンドは **kotlin-build-resolver** agent を呼び出し、最小限の変更で Kotlin ビルドエラーをインクリメンタルに修正します。

## このコマンドの動作 (What This Command Does)

1. **診断を実行 (Run Diagnostics)**: `./gradlew build`、`detekt`、`ktlintCheck` を実行する
2. **エラーを解析 (Parse Errors)**: ファイルごとにグループ化し、重大度でソートする
3. **インクリメンタルに修正 (Fix Incrementally)**: 一度に1つのエラー
4. **各修正を検証 (Verify Each Fix)**: 各変更後にビルドを再実行する
5. **サマリーを報告 (Report Summary)**: 修正内容と残件を表示する

## 使用するタイミング (When to Use)

`/kotlin-build` を使用するのは:
- `./gradlew build` がエラーで失敗する場合
- Kotlin コンパイラがエラーを報告する場合
- `./gradlew detekt` が違反を報告する場合
- Gradle 依存関係の解決が失敗する場合
- ビルドを壊す変更を pull した後

## 実行される診断コマンド (Diagnostic Commands Run)

```bash
# Primary build check
./gradlew build 2>&1

# Static analysis
./gradlew detekt 2>&1 || echo "detekt not configured"
./gradlew ktlintCheck 2>&1 || echo "ktlint not configured"

# Dependency issues
./gradlew dependencies --configuration runtimeClasspath 2>&1 | head -100

# Optional deep refresh when caches or dependency metadata are suspect
./gradlew build --refresh-dependencies
```

## セッション例 (Example Session)

````text
User: /kotlin-build

Agent:
# Kotlin Build Resolution

## Initial Diagnostics

```bash
$ ./gradlew build
e: src/main/kotlin/com/example/service/UserService.kt:25:15 Unresolved reference: UserRepository
e: src/main/kotlin/com/example/routes/UserRoutes.kt:42:9 Type mismatch: inferred type is String but Int was expected
e: src/main/kotlin/com/example/routes/UserRoutes.kt:58:5 'when' expression must be exhaustive
```

Errors found: 3

## Fix 1: Unresolved Reference

File: src/main/kotlin/com/example/service/UserService.kt:25
Error: Unresolved reference: UserRepository
Cause: Missing import

```kotlin
// Added import
import com.example.repository.UserRepository
```

```bash
$ ./gradlew build
# 2 errors remaining
```

## Fix 2: Type Mismatch

File: src/main/kotlin/com/example/routes/UserRoutes.kt:42
Error: Type mismatch: inferred type is String but Int was expected

```kotlin
// Changed
val count = call.parameters["count"]
// To
val count = call.parameters["count"]?.toIntOrNull()
    ?: return@get call.respond(HttpStatusCode.BadRequest, "Invalid count")
```

```bash
$ ./gradlew build
# 1 error remaining
```

## Fix 3: Non-Exhaustive When

File: src/main/kotlin/com/example/routes/UserRoutes.kt:58
Error: 'when' expression must be exhaustive

```kotlin
// Added missing branch
when (user.role) {
    Role.ADMIN -> handleAdmin(user)
    Role.USER -> handleUser(user)
    Role.MODERATOR -> handleModerator(user) // Added
}
```

```bash
$ ./gradlew build
# Build successful!
```

## Final Verification

```bash
$ ./gradlew detekt
# No issues

$ ./gradlew test
# All tests passed
```

## Summary

| Metric | Count |
|--------|-------|
| Build errors fixed | 3 |
| Detekt issues fixed | 0 |
| Files modified | 2 |
| Remaining issues | 0 |

Build Status: PASS: SUCCESS
````

## よくある修正対象エラー (Common Errors Fixed)

| Error | Typical Fix |
|-------|-------------|
| `Unresolved reference: X` | Add import or dependency |
| `Type mismatch` | Fix type conversion or assignment |
| `'when' must be exhaustive` | Add missing sealed class branches |
| `Suspend function can only be called from coroutine` | Add `suspend` modifier |
| `Smart cast impossible` | Use local `val` or `let` |
| `None of the following candidates is applicable` | Fix argument types |
| `Could not resolve dependency` | Fix version or add repository |

## 修正戦略 (Fix Strategy)

1. **ビルドエラーを最優先** — コードはコンパイル可能である必要がある
2. **Detekt 違反を次に** — コード品質の問題を修正する
3. **ktlint 警告を3番目に** — フォーマットを修正する
4. **一度に1つの修正** — 各変更を検証する
5. **最小限の変更** — リファクタリングせず、修正のみ行う

## 停止条件 (Stop Conditions)

agent は以下の場合に停止して報告します:
- 同じエラーが3回試行後も残る
- 修正によりエラーが増える
- アーキテクチャ変更が必要
- 外部依存関係が不足している

## 関連コマンド (Related Commands)

- `/kotlin-test` — ビルド成功後にテストを実行する
- `/kotlin-review` — コード品質をレビューする
- `verification-loop` skill — フル検証ループ

## 関連 (Related)

- Agent: `agents/kotlin-build-resolver.md`
- Skill: `skills/kotlin-patterns/`
