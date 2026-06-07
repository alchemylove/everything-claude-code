---
paths:
  - "**/*.dart"
  - "**/pubspec.yaml"
  - "**/analysis_options.yaml"
---
# Dart/Flutter フック (Dart/Flutter Hooks)

> このファイルは [common/hooks.md](../common/hooks.md) を拡張し、Dart および Flutter 固有の内容を追加する。

## PostToolUse Hooks

`~/.claude/settings.json` で設定する:

- **dart format**: 編集後に `.dart` ファイルを自動フォーマット
- **dart analyze**: Dart ファイル編集後に静的解析を実行し、warning を表示
- **flutter test**: 大きな変更後に影響を受けるテストを任意で実行

## 推奨 Hook 設定 (Recommended Hook Configuration)

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": { "tool_name": "Edit", "file_paths": ["**/*.dart"] },
        "hooks": [
          { "type": "command", "command": "dart format $CLAUDE_FILE_PATHS" }
        ]
      }
    ]
  }
}
```

## コミット前チェック (Pre-commit Checks)

Dart/Flutter の変更をコミットする前に実行する:

```bash
dart format --set-exit-if-changed .
dart analyze --fatal-infos
flutter test
```

## 便利なワンライナー (Useful One-liners)

```bash
# Format all Dart files
dart format .

# Analyze and report issues
dart analyze

# Run all tests with coverage
flutter test --coverage

# Regenerate code-gen files
dart run build_runner build --delete-conflicting-outputs

# Check for outdated packages
flutter pub outdated

# Upgrade packages within constraints
flutter pub upgrade
```
