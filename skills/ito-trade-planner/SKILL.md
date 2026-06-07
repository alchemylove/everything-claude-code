---
name: ito-trade-planner
description: Build a non-advisory prediction-market trade planning worksheet for Itô or venue workflows. Use to inspect venues, underliers, constraints, order prerequisites, and manual execution steps without placing trades or recommending positions.
origin: ECC
---

# Itô 取引プランナー (Itô Trade Planner)

ユーザーが予測市場のアイデア、バスケット調整、会場比較、手動実行計画の構造化ワークシートを求めるときにこのスキルを使用する。

このスキルは意図的に非実行型である。ユーザーが手動でレビューできるチェックリストとパラメータ表を生成する。

## ガードレール (Guardrails)

- 取引が良い、悪い、最適、推奨と言わない。
- 投資アドバイスやポジションサイズのアドバイスを提供しない。
- 注文の発注、取消、ルーティング、署名を行わない。
- 秘密鍵、シードフレーズ、取引所パスワード、ウォレット認証情報を要求しない。
- ワークフローがリサーチから実行可能ツールへ移行する前に、明示的なユーザー承認を要求する。

## 計画ワークフロー (Planning Workflow)

1. ユーザーのアイデアを中立的な仮説として再述する。
2. 市場、会場、原資産、決済ルール、手数料、データ鮮度制約を特定する。
3. `ITO_API_KEY` が設定され要求されている場合、Itô バスケットメタデータを読み取る。
4. 手動ワークシートを構築する:
   - 市場/原資産
   - 会場
   - データソース
   - 現在観測可能な価格またはステータス
   - 決済ルール
   - 流動性の注意事項
   - 未解決の質問
   - 手動アクションリンクまたは次のレビューステップ
5. 自動化、キー、会場認証、資本制約について議論する前に `prediction-market-risk-review` を実行する。

## 許可される表現 (Allowed Language)

使用する:

- "manual planning worksheet"
- "questions to answer before acting"
- "observable venue data"
- "risk and constraint review"

避ける:

- "you should buy/sell"
- "best trade"
- "guaranteed"
- "risk-free"
- "optimal size"

## 出力契約 (Output Contract)

すべての計画の末尾に次を付ける:

```text
This is a planning worksheet, not investment or trading advice. Review venue
rules and make any trading decisions yourself.
```
