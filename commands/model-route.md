---
description: 複雑さ、リスク、予算に基づいて、現在のタスクに最適なモデルティアを推奨します。
---

# モデルルーティングコマンド (Model Route Command)

複雑さと予算に基づいて、現在のタスクに最適なモデルティアを推奨します。

## 使い方 (Usage)

`/model-route [task-description] [--budget low|med|high]`

## ルーティングヒューリスティック (Routing Heuristic)

- `haiku`: 決定論的で低リスクな機械的変更
- `sonnet`: 実装とリファクタリングのデフォルト
- `opus`: アーキテクチャ、深いレビュー、曖昧な要件

## 必須出力 (Required Output)

- 推奨モデル
- 信頼度レベル
- このモデルが適する理由
- 最初の試行が失敗した場合のフォールバックモデル

## 引数 (Arguments)

$ARGUMENTS:
- `[task-description]` オプションのフリーテキスト
- `--budget low|med|high` オプション
