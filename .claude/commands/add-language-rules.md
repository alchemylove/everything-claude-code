---
name: add-language-rules
description: everything-claude-code における add-language-rules 向けワークフローコマンドの雛形。
allowed_tools: ["Bash", "Read", "Write", "Grep", "Glob"]
---

# /add-language-rules

`everything-claude-code` で **add-language-rules** に取り組むときはこのワークフローを使う。

## 目的 (Goal)

rules システムに新しいプログラミング言語を追加する。coding style、hooks、patterns、security、testing のガイドラインを含む。

## よく使うファイル (Common Files)

- `rules/*/coding-style.md`
- `rules/*/hooks.md`
- `rules/*/patterns.md`
- `rules/*/security.md`
- `rules/*/testing.md`

## 推奨手順 (Suggested Sequence)

1. 編集前に現状と失敗モードを把握する。
2. ワークフローの目的を満たす最小の一貫した変更を行う。
3. 変更したファイルに最も関連する検証を実行する。
4. 何を変更したか、何がまだレビュー待ちかを要約する。

## 典型的なコミットシグナル (Typical Commit Signals)

- `rules/{language}/` 配下に新しいディレクトリを作成
- 言語固有の内容で coding-style.md、hooks.md、patterns.md、security.md、testing.md を追加
- 必要に応じて関連 skill を参照またはリンク

## 注意事項 (Notes)

- これは雛形であり、ハードコードされたスクリプトではない。
- ワークフローが大きく変わったらコマンドを更新する。
