---
description: Kotlin 向け TDD workflow を強制する。Kotest でテストを先に書き、その後実装する。Kover で 80%+ coverage を検証する。
---

# Kotlin TDD コマンド (Kotlin TDD Command)

このコマンドは Kotest、MockK、Kover を用いた Kotlin コード向け test-driven development 手法を強制します。

## このコマンドの動作 (What This Command Does)

1. **型/インターフェースを定義 (Define Types/Interfaces)**: まず関数シグネチャを scaffold する
2. **Kotest テストを書く (Write Kotest Tests)**: 包括的な test spec を作成する（RED）
3. **テストを実行 (Run Tests)**: 正しい理由で失敗することを検証する
4. **コードを実装 (Implement Code)**: 通過する最小限のコードを書く（GREEN）
5. **リファクタ (Refactor)**: テストを green のまま改善する
6. **カバレッジを確認 (Check Coverage)**: Kover で 80%+ coverage を確保する

## 使用するタイミング (When to Use)

`/kotlin-test` を使用するのは:
- 新しい Kotlin 関数やクラスを実装するとき
- 既存 Kotlin コードに test coverage を追加するとき
- bug を修正するとき（まず失敗するテストを書く）
- 重要なビジネスロジックを構築するとき
- Kotlin で TDD workflow を学ぶとき

## TDD サイクル (TDD Cycle)

```
RED     -> Write failing Kotest test
GREEN   -> Implement minimal code to pass
REFACTOR -> Improve code, tests stay green
REPEAT  -> Next test case
```

## セッション例 (Example Session)

````text
User: /kotlin-test I need a function to validate user registration requests

Agent:
# TDD Session: Registration Validator

## Step 1: Define Interface

```kotlin
// validator/RegistrationValidator.kt
package com.example.validator

data class RegistrationRequest(
    val name: String,
    val email: String,
    val password: String,
)

sealed class ValidationResult {
    data object Valid : ValidationResult()
    data class Invalid(val errors: List<String>) : ValidationResult()
}

fun validateRegistration(request: RegistrationRequest): ValidationResult {
    TODO("not implemented")
}
```

## Step 2: Write Kotest Tests (RED)

```kotlin
// validator/RegistrationValidatorTest.kt
package com.example.validator

import io.kotest.core.spec.style.FunSpec
import io.kotest.matchers.shouldBe
import io.kotest.matchers.types.shouldBeInstanceOf

class RegistrationValidatorTest : FunSpec({
    test("valid registration returns Valid") {
        val request = RegistrationRequest(
            name = "Alice",
            email = "alice@example.com",
            password = "SecureP@ss1",
        )

        val result = validateRegistration(request)

        result.shouldBeInstanceOf<ValidationResult.Valid>()
    }

    test("blank name returns Invalid") {
        val request = RegistrationRequest(
            name = "",
            email = "alice@example.com",
            password = "SecureP@ss1",
        )

        val result = validateRegistration(request)

        val invalid = result.shouldBeInstanceOf<ValidationResult.Invalid>()
        invalid.errors shouldBe listOf("Name is required")
    }

    test("invalid email returns Invalid") {
        val request = RegistrationRequest(
            name = "Alice",
            email = "not-an-email",
            password = "SecureP@ss1",
        )

        val result = validateRegistration(request)

        val invalid = result.shouldBeInstanceOf<ValidationResult.Invalid>()
        invalid.errors shouldBe listOf("Invalid email format")
    }

    test("short password returns Invalid") {
        val request = RegistrationRequest(
            name = "Alice",
            email = "alice@example.com",
            password = "short",
        )

        val result = validateRegistration(request)

        val invalid = result.shouldBeInstanceOf<ValidationResult.Invalid>()
        invalid.errors shouldBe listOf("Password must be at least 8 characters")
    }

    test("multiple errors returns all errors") {
        val request = RegistrationRequest(
            name = "",
            email = "bad",
            password = "short",
        )

        val result = validateRegistration(request)

        val invalid = result.shouldBeInstanceOf<ValidationResult.Invalid>()
        invalid.errors.size shouldBe 3
    }
})
```

## Step 3: Run Tests - Verify FAIL

```bash
$ ./gradlew test

RegistrationValidatorTest > valid registration returns Valid FAILED
  kotlin.NotImplementedError: An operation is not implemented

FAILED (5 tests, 0 passed, 5 failed)
```

✓ Tests fail as expected (NotImplementedError).

## Step 4: Implement Minimal Code (GREEN)

```kotlin
// validator/RegistrationValidator.kt
package com.example.validator

private val EMAIL_REGEX = Regex("^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$")
private const val MIN_PASSWORD_LENGTH = 8

fun validateRegistration(request: RegistrationRequest): ValidationResult {
    val errors = buildList {
        if (request.name.isBlank()) add("Name is required")
        if (!EMAIL_REGEX.matches(request.email)) add("Invalid email format")
        if (request.password.length < MIN_PASSWORD_LENGTH) add("Password must be at least $MIN_PASSWORD_LENGTH characters")
    }

    return if (errors.isEmpty()) ValidationResult.Valid
    else ValidationResult.Invalid(errors)
}
```

## Step 5: Run Tests - Verify PASS

```bash
$ ./gradlew test

RegistrationValidatorTest > valid registration returns Valid PASSED
RegistrationValidatorTest > blank name returns Invalid PASSED
RegistrationValidatorTest > invalid email returns Invalid PASSED
RegistrationValidatorTest > short password returns Invalid PASSED
RegistrationValidatorTest > multiple errors returns all errors PASSED

PASSED (5 tests, 5 passed, 0 failed)
```

✓ All tests passing!

## Step 6: Check Coverage

```bash
$ ./gradlew koverHtmlReport

Coverage: 100.0% of statements
```

✓ Coverage: 100%

## TDD Complete!
````

## テストパターン (Test Patterns)

### StringSpec（最もシンプル）

```kotlin
class CalculatorTest : StringSpec({
    "add two positive numbers" {
        Calculator.add(2, 3) shouldBe 5
    }
})
```

### BehaviorSpec（BDD）

```kotlin
class OrderServiceTest : BehaviorSpec({
    Given("a valid order") {
        When("placed") {
            Then("should be confirmed") { /* ... */ }
        }
    }
})
```

### データ駆動テスト (Data-Driven Tests)

```kotlin
class ParserTest : FunSpec({
    context("valid inputs") {
        withData("2026-01-15", "2026-12-31", "2000-01-01") { input ->
            parseDate(input).shouldNotBeNull()
        }
    }
})
```

### Coroutine テスト (Coroutine Testing)

```kotlin
class AsyncServiceTest : FunSpec({
    test("concurrent fetch completes") {
        runTest {
            val result = service.fetchAll()
            result.shouldNotBeEmpty()
        }
    }
})
```

## カバレッジコマンド (Coverage Commands)

```bash
# Run tests with coverage
./gradlew koverHtmlReport

# Verify coverage thresholds
./gradlew koverVerify

# XML report for CI
./gradlew koverXmlReport

# Open HTML report
open build/reports/kover/html/index.html

# Run specific test class
./gradlew test --tests "com.example.UserServiceTest"

# Run with verbose output
./gradlew test --info
```

## カバレッジ目標 (Coverage Targets)

| Code Type | Target |
|-----------|--------|
| Critical business logic | 100% |
| Public APIs | 90%+ |
| General code | 80%+ |
| Generated code | Exclude |

## TDD ベストプラクティス (TDD Best Practices)

**DO:**
- 実装より先にテストを書く
- 各変更後にテストを実行する
- 表現力のある assertion には Kotest matchers を使う
- suspend 関数には MockK の `coEvery`/`coVerify` を使う
- 実装詳細ではなく振る舞いをテストする
- エッジケース（empty、null、max values）を含める

**DON'T:**
- テストより先に実装を書く
- RED フェーズをスキップする
- private 関数を直接テストする
- coroutine テストで `Thread.sleep()` を使う
- flaky テストを無視する

## 関連コマンド (Related Commands)

- `/kotlin-build` — ビルドエラーを修正する
- `/kotlin-review` — 実装後にコードをレビューする
- `verification-loop` skill — フル検証ループを実行する

## 関連 (Related)

- Skill: `skills/kotlin-testing/`
- Skill: `skills/tdd-workflow/`
