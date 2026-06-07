---
name: feature-development
description: everything-claude-code における feature-development 向けワークフローコマンドの雛形。
allowed_tools: ["Bash", "Read", "Write", "Grep", "Glob"]
---

# /feature-development

`everything-claude-code` で **feature-development** に取り組むときはこのワークフローを使う。

## 目的 (Goal)

標準的な機能実装ワークフロー

## よく使うファイル (Common Files)

- `manifests/*`
- `schemas/*`
- `**/*.test.*`
- `**/api/**`

## 推奨手順 (Suggested Sequence)

1. 編集前に現状と失敗モードを把握する。
2. ワークフローの目的を満たす最小の一貫した変更を行う。
3. 変更したファイルに最も関連する検証を実行する。
4. 何を変更したか、何がまだレビュー待ちかを要約する。

## 典型的なコミットシグナル (Typical Commit Signals)

- 機能実装を追加
- 機能のテストを追加
- ドキュメントを更新

## 注意事項 (Notes)

- これは雛形であり、ハードコードされたスクリプトではない。
- ワークフローが大きく変わったらコマンドを更新する。
