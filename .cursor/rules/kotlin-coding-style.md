---
description: "Kotlin coding style: common ルールの拡張"
globs: ["**/*.kt", "**/*.kts", "**/build.gradle.kts"]
alwaysApply: false
---
# Kotlin Coding Style (Kotlin Coding Style)

> このファイルは common coding style ルールを Kotlin 固有の内容で拡張する。

## フォーマット (Formatting)

- **ktfmt** または **ktlint** による auto-formatting（`kotlin-hooks.md` で設定）
- 複数行宣言では trailing comma を使用

## 不変性 (Immutability)

グローバルな不変性要件は common coding style ルールで強制。
Kotlin 固有には:

- `var` より `val` を優先
- 不変 collection 型（`List`, `Map`, `Set`）を使用
- 不変更新には `copy()` 付き `data class` を使用

## Null Safety

- `!!` を避け — `?.`, `?:`, `require`, または `checkNotNull` を使用
- Java interop 境界では platform type を明示的に処理

## Expression Bodies

単一式関数では expression body を優先:

```kotlin
fun isAdult(age: Int): Boolean = age >= 18
```

## 参照 (Reference)

包括的な Kotlin イディオムとパターンは skill: `kotlin-patterns` を参照。
