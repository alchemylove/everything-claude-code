---
description: "Kotlin hooks: common ルールの拡張"
globs: ["**/*.kt", "**/*.kts", "**/build.gradle.kts"]
alwaysApply: false
---
# Kotlin Hooks (Kotlin Hooks)

> このファイルは common hooks ルールを Kotlin 固有の内容で拡張する。

## PostToolUse Hooks

`~/.claude/settings.json` で設定:

- **ktfmt/ktlint**: 編集後に `.kt` と `.kts` ファイルを auto-format
- **detekt**: Kotlin ファイル編集後に static analysis を実行
- **./gradlew build**: 変更後にコンパイルを検証
