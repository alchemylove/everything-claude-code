---
paths:
  - "**/*.kt"
  - "**/*.kts"
---
# Kotlin コーディングスタイル (Kotlin Coding Style)

> このファイルは [common/coding-style.md](../common/coding-style.md) を拡張し、Kotlin 固有の内容を追加する。

## フォーマット (Formatting)

- スタイル強制には **ktlint** または **Detekt**
- 公式 Kotlin コードスタイル（`gradle.properties` に `kotlin.code.style=official`）

## 不変性 (Immutability)

- `var` より `val` を優先 — デフォルトは `val`、変更が必要なときだけ `var`
- 値型には `data class`、公開 API では不変コレクション（`List`、`Map`、`Set`）を使用
- 状態更新はコピーオンライト: `state.copy(field = newValue)`

## 命名 (Naming)

Kotlin の規約に従う:
- 関数・プロパティは `camelCase`
- クラス・インターフェース・オブジェクト・型エイリアスは `PascalCase`
- 定数は `SCREAMING_SNAKE_CASE`（`const val` または `@JvmStatic`）
- インターフェースは `I` ではなく振る舞いで接頭辞: `IClickable` ではなく `Clickable`

## Null 安全性 (Null Safety)

- `!!` は使わない — `?.`、`?:`、`requireNotNull()`、`checkNotNull()` を優先
- null 安全なスコープ処理には `?.let {}` を使用
- 正当に結果がない関数は nullable 型を返す

```kotlin
// BAD
val name = user!!.name

// GOOD
val name = user?.name ?: "Unknown"
val name = requireNotNull(user) { "User must be set before accessing name" }.name
```

## Sealed 型 (Sealed Types)

閉じた状態階層のモデルには sealed class/interface を使用:

```kotlin
sealed interface UiState<out T> {
    data object Loading : UiState<Nothing>
    data class Success<T>(val data: T) : UiState<T>
    data class Error(val message: String) : UiState<Nothing>
}
```

sealed 型では常に網羅的な `when` を使用 — `else` ブランチは不要。

## 拡張関数 (Extension Functions)

ユーティリティ操作には拡張関数を使うが、発見しやすく保つ:
- レシーバ型にちなんだファイル名に配置（`StringExt.kt`、`FlowExt.kt`）
- スコープを限定 — `Any` や過度に汎用的な型への拡張は追加しない

## スコープ関数 (Scope Functions)

適切なスコープ関数を使う:
- `let` — null チェック + 変換: `user?.let { greet(it) }`
- `run` — レシーバを使って結果を計算: `service.run { fetch(config) }`
- `apply` — オブジェクトを設定: `builder.apply { timeout = 30 }`
- `also` — 副作用: `result.also { log(it) }`
- スコープ関数の深いネストは避ける（最大 2 レベル）

## エラーハンドリング (Error Handling)

- `Result<T>` またはカスタム sealed 型を使用
- スロー可能なコードのラップには `runCatching {}` を使用
- `CancellationException` はキャッチしない — 常に再スロー
- 制御フローに `try-catch` を使わない

```kotlin
// BAD — using exceptions for control flow
val user = try { repository.getUser(id) } catch (e: NotFoundException) { null }

// GOOD — nullable return
val user: User? = repository.findUser(id)
```
