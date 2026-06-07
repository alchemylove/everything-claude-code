---
description: Android および KMP プロジェクトの Gradle ビルドエラーを修正します。
---

# Gradle ビルド修正 (Gradle Build Fix)

Android および Kotlin Multiplatform プロジェクトの Gradle build および compilation error をインクリメンタルに修正します。

## ステップ 1: ビルド設定の検出 (Step 1: Detect Build Configuration)

プロジェクトタイプを特定し、適切な build を実行:

| Indicator | Build Command |
|-----------|---------------|
| `build.gradle.kts` + `composeApp/` (KMP) | `./gradlew composeApp:compileKotlinMetadata 2>&1` |
| `build.gradle.kts` + `app/` (Android) | `./gradlew app:compileDebugKotlin 2>&1` |
| `settings.gradle.kts` with modules | `./gradlew assemble 2>&1` |
| Detekt configured | `./gradlew detekt 2>&1` |

`gradle.properties` と `local.properties` の設定も確認します。

## ステップ 2: エラーの解析とグループ化 (Step 2: Parse and Group Errors)

1. build command を実行し output を capture
2. Kotlin compilation error と Gradle configuration error を分離
3. module と file path でグループ化
4. ソート: configuration error を最初に、次に dependency order で compilation error

## ステップ 3: 修正ループ (Step 3: Fix Loop)

各 error に対して:

1. **ファイルを読む** — error 行周辺の完全な context
2. **診断** — 一般的なカテゴリ:
   - missing import または unresolved reference
   - type mismatch または incompatible types
   - `build.gradle.kts` 内の missing dependency
   - Expect/actual mismatch (KMP)
   - Compose compiler error
3. **最小限の修正** — error を解決する最小の変更
4. **ビルドを再実行** — fix を検証し新しい error を確認
5. **続行** — 次の error へ

## ステップ 4: ガードレール (Step 4: Guardrails)

以下の場合はユーザーに停止して確認:
- fix が解決するより多くの error を導入
- 3 回の試行後も同じ error が持続
- error が新しい dependency の追加や module structure の変更を必要とする
- Gradle sync 自体が失敗（configuration-phase error）
- error が generated code 内にある（Room、SQLDelight、KSP）

## ステップ 5: サマリー (Step 5: Summary)

報告内容:
- 修正された error（module、file、description）
- 残りの error
- 導入された新しい error（zero であるべき）
- 推奨される next steps

## 一般的な Gradle/KMP 修正 (Common Gradle/KMP Fixes)

| Error | Fix |
|-------|-----|
| Unresolved reference in `commonMain` | Check if the dependency is in `commonMain.dependencies {}` |
| Expect declaration without actual | Add `actual` implementation in each platform source set |
| Compose compiler version mismatch | Align Kotlin and Compose compiler versions in `libs.versions.toml` |
| Duplicate class | Check for conflicting dependencies with `./gradlew dependencies` |
| KSP error | Run `./gradlew kspCommonMainKotlinMetadata` to regenerate |
| Configuration cache issue | Check for non-serializable task inputs |
