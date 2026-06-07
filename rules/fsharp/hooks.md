---
paths:
  - "**/*.fs"
  - "**/*.fsx"
  - "**/*.fsproj"
  - "**/*.sln"
  - "**/*.slnx"
  - "**/Directory.Build.props"
  - "**/Directory.Build.targets"
---
# F# フック (F# Hooks)

> このファイルは [common/hooks.md](../common/hooks.md) を拡張し、F# 固有の内容を追加する。

## PostToolUse フック (PostToolUse Hooks)

`~/.claude/settings.json` で設定する:

- **fantomas**: 編集された F# ファイルを自動フォーマット
- **dotnet build**: 編集後にソリューションまたはプロジェクトが引き続きコンパイルされることを確認する
- **dotnet test --no-build**: 動作の変更後に最も近い関連テストプロジェクトを再実行する

## Stop フック (Stop Hooks)

- 広範な F# の変更を伴うセッションを終了する前に最終的な `dotnet build` を実行する
- 変更された `appsettings*.json` ファイルに対して警告を出し、シークレットがコミットされないようにする
