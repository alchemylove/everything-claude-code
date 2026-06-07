---
description: "Kotlin testing: common ルールの拡張"
globs: ["**/*.kt", "**/*.kts", "**/build.gradle.kts"]
alwaysApply: false
---
# Kotlin Testing (Kotlin Testing)

> このファイルは common testing ルールを Kotlin 固有の内容で拡張する。

## Framework

spec style（StringSpec, FunSpec, BehaviorSpec）の **Kotest** と mocking の **MockK** を使用。

## Coroutine Testing

`kotlinx-coroutines-test` の `runTest` を使用:

```kotlin
test("async operation completes") {
    runTest {
        val result = service.fetchData()
        result.shouldNotBeEmpty()
    }
}
```

## Coverage

coverage レポートには **Kover** を使用:

```bash
./gradlew koverHtmlReport
./gradlew koverVerify
```

## 参照 (Reference)

詳細な Kotest パターン、MockK の使い方、property-based testing は skill: `kotlin-testing` を参照。
