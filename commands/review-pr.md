---
description: 専門 agent を用いた包括的な PR レビュー
---

専門的な複数視点で pull request を包括レビューする。

## 使い方 (Usage)

`/review-pr [PR-number-or-URL] [--focus=comments|tests|errors|types|code|simplify]`

PR が未指定なら、現在ブランチの PR をレビューする。focus が未指定なら、フル review stack を実行する。

## 手順 (Steps)

1. PR の特定:
   - `gh pr view` で PR 詳細、変更ファイル、diff を取得
2. プロジェクトガイダンスの確認:
   - `CLAUDE.md`、lint config、TypeScript config、repo 規約を探す
3. 専門レビュー agent の実行:
   - `code-reviewer`
   - `comment-analyzer`
   - `pr-test-analyzer`
   - `silent-failure-hunter`
   - `type-design-analyzer`
   - `code-simplifier`
4. 結果の集約:
   - 重複する所見を dedupe
   - 重大度でランク付け
5. 重大度別にグループ化して報告

## 信頼度ルール (Confidence Rule)

confidence >= 80 の issue のみ報告する:

- Critical: bug、セキュリティ、データ損失
- Important: テスト不足、品質問題、スタイル違反
- Advisory: 明示的に要求された場合のみの提案
