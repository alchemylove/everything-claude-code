---
name: database-migration
description: everything-claude-code における database-migration 向けワークフローコマンドの雛形。
allowed_tools: ["Bash", "Read", "Write", "Grep", "Glob"]
---

# /database-migration

`everything-claude-code` で **database-migration** に取り組むときはこのワークフローを使う。

## 目的 (Goal)

マイグレーションファイルを伴うデータベーススキーマの変更

## よく使うファイル (Common Files)

- `**/schema.*`
- `migrations/*`

## 推奨手順 (Suggested Sequence)

1. 編集前に現状と失敗モードを把握する。
2. ワークフローの目的を満たす最小の一貫した変更を行う。
3. 変更したファイルに最も関連する検証を実行する。
4. 何を変更したか、何がまだレビュー待ちかを要約する。

## 典型的なコミットシグナル (Typical Commit Signals)

- マイグレーションファイルを作成
- スキーマ定義を更新
- 型を生成または更新

## 注意事項 (Notes)

- これは雛形であり、ハードコードされたスクリプトではない。
- ワークフローが大きく変わったらコマンドを更新する。
