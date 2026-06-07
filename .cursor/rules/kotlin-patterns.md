---
description: "Kotlin patterns: common ルールの拡張"
globs: ["**/*.kt", "**/*.kts", "**/build.gradle.kts"]
alwaysApply: false
---
# Kotlin Patterns (Kotlin Patterns)

> このファイルは common patterns ルールを Kotlin 固有の内容で拡張する。

## Sealed Classes

網羅的な型階層には sealed class/interface を使用:

```kotlin
sealed class Result<out T> {
    data class Success<T>(val data: T) : Result<T>()
    data class Failure(val error: AppError) : Result<Nothing>()
}
```

## Extension Functions

継承なしで振る舞いを追加。使用箇所にスコープ:

```kotlin
fun String.toSlug(): String =
    lowercase().replace(Regex("[^a-z0-9\\s-]"), "").replace(Regex("\\s+"), "-")
```

## Scope Functions

- `let`: nullable またはスコープ内の結果を変換
- `apply`: オブジェクトを設定
- `also`: 副作用
- scope function のネストは避ける

## Dependency Injection

Ktor project では DI に Koin を使用:

```kotlin
val appModule = module {
    single<UserRepository> { ExposedUserRepository(get()) }
    single { UserService(get()) }
}
```

## 参照 (Reference)

coroutine、DSL builder、delegation を含む包括的な Kotlin パターンは skill: `kotlin-patterns` を参照。
