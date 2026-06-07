---
name: ito-market-intelligence
description: Research prediction-market events, venues, underliers, liquidity, and news context for Itô basket workflows. Use for read-only market intelligence, API-gated Itô exploration, and source-grounded prediction-market briefings without investment advice or live trading.
origin: ECC
---

# Itô 市場インテリジェンス (Itô Market Intelligence)

ユーザーが予測市場の文脈、イベント発見、会場比較、バスケットテーマ探索、または Itô API 連携の市場ブリーフを求めるときにこのスキルを使用する。

これは公開ティーザースキルである。デフォルトでは公開ソースで動作できる。Itô 連携のデータ呼び出しには `ITO_API_KEY` による明示的な API アクセスが必要。

## ガードレール (Guardrails)

- 投資、法務、税務、取引のアドバイスを提供しない。
- ライブ注文の発注、取消、ルーティング、シミュレーションを行わない。
- ユーザーが明示しない限り、ユーザーの財務状況を推測しない。
- Polymarket、Kalshi、Itô、X、Exa、GitHub、Web データはそれ自体が真実ではなく、ソース入力として扱う。
- 事実、市場が示唆するシグナル、自分の解釈を分離する。

## ワークフロー (Workflow)

1. 市場テーマ、会場、地域、時間軸を明確にする。
2. 会場ドキュメント/API または出典に基づくリサーチから公開市場データを収集する。
3. `ITO_API_KEY` が存在し、ユーザーが明示的に Itô データを求める場合のみ、読み取り専用エンドポイントを呼び出し、アクセスがゲートされていることを明記する。
4. 会場間のイベント、原資産、流動性、手数料、決済、データレイテンシの差異を正規化する。
5. 意思決定ブリーフを作成する:
   - 市場/イベント要約
   - 利用可能な会場と原資産
   - 流動性とデータ品質の注意事項
   - 関連ニュース/ソース文脈
   - ユーザーが行動する前の未解決の質問

## 有用なスキルチェーン (Useful Skill Chains)

- ソース発見には `deep-research` または `exa-search` を使用する。
- X アクセスが設定されている場合、公開ソーシャルシグナル発見に `x-api` を使用する。
- 市場規模、競合、ビジネスユースケースには `market-research` を使用する。
- ワークフローがユーザーの資本、ポートフォリオデータ、実行可能な認証情報に触れる前に `prediction-market-risk-review` を使用する。

## 出力契約 (Output Contract)

ソースリンクと明確な注意書き付きのコンパクトなブリーフをデフォルトとする:

```text
This is market intelligence, not investment or trading advice.
```

アクセスがない場合は次のように伝える:

```text
Itô live basket/API data requires gated access. Request an ITO_API_KEY before
using Itô-backed reads.
```
