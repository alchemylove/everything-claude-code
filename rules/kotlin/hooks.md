---
paths:
  - "**/*.kt"
  - "**/*.kts"
  - "**/build.gradle.kts"
---
# Kotlin フック (Kotlin Hooks)

> このファイルは [common/hooks.md](../common/hooks.md) を拡張し、Kotlin 固有の内容を追加する。

## PostToolUse Hooks

`~/.claude/settings.json` で設定:

- **ktfmt/ktlint**: 編集後に `.kt` と `.kts` ファイルを自動フォーマット
- **detekt**: Kotlin ファイル編集後に静的解析を実行
- **./gradlew build**: 変更後にコンパイルを検証
