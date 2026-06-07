---
paths:
  - "**/*.cs"
  - "**/*.csx"
  - "**/*.csproj"
  - "**/*.sln"
  - "**/Directory.Build.props"
  - "**/Directory.Build.targets"
---
# C# フック (C# Hooks)

> このファイルは [common/hooks.md](../common/hooks.md) を拡張し、C# 固有の内容を追加する。

## PostToolUse フック (PostToolUse Hooks)

`~/.claude/settings.json` で設定:

- **dotnet format**: 編集した C# ファイルを自動フォーマットし、analyzer 修正を適用
- **dotnet build**: 編集後に solution または project がコンパイルできることを確認
- **dotnet test --no-build**: 挙動変更後に最も近い関連テスト project を再実行

## Stop フック (Stop Hooks)

- C# の広範な変更があるセッション終了前に最終 `dotnet build` を実行
- 変更された `appsettings*.json` を警告し、シークレットがコミットされないようにする
