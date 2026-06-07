---
description: idiomatic patterns、null safety、coroutine safety、security を含む包括的な Kotlin コードレビュー。kotlin-reviewer agent を呼び出す。
---

# Kotlin コードレビュー (Kotlin Code Review)

このコマンドは **kotlin-reviewer** agent を呼び出し、Kotlin 固有の包括的なコードレビューを行います。

## このコマンドの動作 (What This Command Does)

1. **Kotlin 変更を特定 (Identify Kotlin Changes)**: `git diff` で変更された `.kt` と `.kts` ファイルを検出する
2. **ビルドと静的解析を実行 (Run Build & Static Analysis)**: `./gradlew build`、`detekt`、`ktlintCheck` を実行する
3. **セキュリティスキャン (Security Scan)**: SQL injection、command injection、ハードコードされた secret をチェックする
4. **Null Safety レビュー (Null Safety Review)**: `!!` の使用、platform type の扱い、unsafe cast を分析する
5. **Coroutine レビュー (Coroutine Review)**: structured concurrency、dispatcher の使用、cancellation をチェックする
6. **レポート生成 (Generate Report)**: 重大度別に issue を分類する

## 使用するタイミング (When to Use)

`/kotlin-review` を使用するのは:
- Kotlin コードを書いた、または変更した後
- Kotlin 変更をコミットする前
- Kotlin コードを含む pull request をレビューするとき
- 新しい Kotlin コードベースにオンボーディングするとき
- idiomatic Kotlin パターンを学ぶとき

## レビューカテゴリ (Review Categories)

### CRITICAL（必須修正）
- SQL/Command injection の脆弱性
- 正当化なしの force-unwrap `!!`
- Platform type の null safety 違反
- GlobalScope の使用（structured concurrency 違反）
- ハードコードされた認証情報
- 安全でない deserialization

### HIGH（修正推奨）
- immutable で十分な箇所での mutable state
- coroutine context 内の blocking 呼び出し
- 長いループでの cancellation チェック不足
- sealed types に対する non-exhaustive `when`
- 大きな関数（50行超）
- 深いネスト（4レベル超）

### MEDIUM（検討）
- non-idiomatic Kotlin（Java スタイルのパターン）
- trailing comma の欠落
- scope function の誤用またはネスト
- 大きな collection chain での sequence 未使用
- 冗長な明示的型

## 自動チェック (Automated Checks Run)

```bash
# Build check
./gradlew build

# Static analysis
./gradlew detekt

# Formatting check
./gradlew ktlintCheck

# Tests
./gradlew test
```

## 使用例 (Example Usage)

````text
User: /kotlin-review

Agent:
# Kotlin Code Review Report

## Files Reviewed
- src/main/kotlin/com/example/service/UserService.kt (modified)
- src/main/kotlin/com/example/routes/UserRoutes.kt (modified)

## Static Analysis Results
✓ Build: Successful
✓ detekt: No issues
WARNING: ktlint: 2 formatting warnings

## Issues Found

[CRITICAL] Force-Unwrap Null Safety
File: src/main/kotlin/com/example/service/UserService.kt:28
Issue: Using !! on nullable repository result
```kotlin
val user = repository.findById(id)!!  // NPE risk
```
Fix: Use safe call with error handling
```kotlin
val user = repository.findById(id)
    ?: throw UserNotFoundException("User $id not found")
```

[HIGH] GlobalScope Usage
File: src/main/kotlin/com/example/routes/UserRoutes.kt:45
Issue: Using GlobalScope breaks structured concurrency
```kotlin
GlobalScope.launch {
    notificationService.sendWelcome(user)
}
```
Fix: Use the call's coroutine scope
```kotlin
launch {
    notificationService.sendWelcome(user)
}
```

## Summary
- CRITICAL: 1
- HIGH: 1
- MEDIUM: 0

Recommendation: FAIL: Block merge until CRITICAL issue is fixed
````

## 承認基準 (Approval Criteria)

| Status | Condition |
|--------|-----------|
| PASS: Approve | No CRITICAL or HIGH issues |
| WARNING: Warning | Only MEDIUM issues (merge with caution) |
| FAIL: Block | CRITICAL or HIGH issues found |

## 他コマンドとの統合 (Integration with Other Commands)

- まず `/kotlin-test` でテストが通ることを確認する
- ビルドエラーがある場合は `/kotlin-build` を使用する
- コミット前に `/kotlin-review` を使用する
- Kotlin 以外の懸念には `/code-review` を使用する

## 関連 (Related)

- Agent: `agents/kotlin-reviewer.md`
- Skills: `skills/kotlin-patterns/`, `skills/kotlin-testing/`
