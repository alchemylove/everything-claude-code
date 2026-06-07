---
description: "Kotlin security: common ルールの拡張"
globs: ["**/*.kt", "**/*.kts", "**/build.gradle.kts"]
alwaysApply: false
---
# Kotlin Security (Kotlin Security)

> このファイルは common security ルールを Kotlin 固有の内容で拡張する。

## Secret 管理 (Secret Management)

```kotlin
val apiKey = System.getenv("API_KEY")
    ?: throw IllegalStateException("API_KEY not configured")
```

## SQL Injection 防止 (SQL Injection Prevention)

常に Exposed の parameterized query を使用:

```kotlin
// Good: Parameterized via Exposed DSL
UsersTable.selectAll().where { UsersTable.email eq email }

// Bad: String interpolation in raw SQL
exec("SELECT * FROM users WHERE email = '$email'")
```

## Authentication

JWT には Ktor の Auth plugin を使用:

```kotlin
install(Authentication) {
    jwt("jwt") {
        verifier(
            JWT.require(Algorithm.HMAC256(secret))
                .withAudience(audience)
                .withIssuer(issuer)
                .build()
        )
        validate { credential ->
            val payload = credential.payload
            if (payload.audience.contains(audience) &&
                payload.issuer == issuer &&
                payload.subject != null) {
                JWTPrincipal(payload)
            } else {
                null
            }
        }
    }
}
```

## Null Safety as Security

Kotlin の型システムは null 関連の脆弱性を防ぐ — この保証を維持するため `!!` を避ける。
