---
name: ito-basket-compare
description: Compare Itô prediction-market baskets against a user's knowledge base, portfolio notes, financial context, watchlist, or research thesis. Use for read-only basket comparison and gap analysis without investment advice or live trading.
origin: ECC
---

# Itô バスケット比較 (Itô Basket Compare)

バスケット、テーマ、市場セットをユーザーのナレッジベース、ポートフォリオメモ、リサーチメモ、CRM 文脈、または表明されたテーゼと比較するためにこのスキルを使用する。

このスキルは読み取り専用である。取引を推奨しない。ユーザーが次に何をするか決める前に、適合性、エクスポージャー、前提、欠落文脈を検査するのに役立つ。

## ガードレール (Guardrails)

- 投資アドバイスを提供したり、買い、売り、保有、ヘッジ、レバレッジ、ポジションサイズを指示したりしない。
- 注文の実行、準備、送信を行わない。
- ユーザーが明示的に指し示さない限り、非公開ドキュメントを使用しない。
- 明示的なユーザー要求の後にのみ、読み取り専用の Itô バスケット/市場データに `ITO_API_KEY` を使用する。
- 財務情報と比較する場合、プライバシーを保護し、比較に必要なフィールドのみを要約する。

## 比較モード (Comparison Modes)

### バスケット vs ナレッジベース (Basket vs Knowledge Base)

1. バスケットテーマと原資産を特定する。
2. ユーザーの関連メモ、ドキュメント、メモリスニペットを取得する。
3. 各原資産を主張、ソース、不確実性、古い前提にマッピングする。
4. 整合するシグナル、矛盾するシグナル、不足リサーチを返す。

### バスケット vs ポートフォリオメモ (Basket vs Portfolio Notes)

1. ユーザーのウォッチリスト、保有要約、エクスポージャーメモを解析する。
2. テーマ、地域、時間軸、イベント結果を比較する。
3. 集中、相関、重複するナラティブエクスポージャーをフラグする。
4. 推奨を避ける。出力は検査と質問として表現する。

### バスケット vs 財務文脈 (Basket vs Financial Context)

1. ユーザー提供または明示的に選択された財務文脈のみを受け入れる。
2. 流動性、ドローダウン、時間軸、制約のミスマッチを特定する。
3. 推測せず、不足している制約を尋ねる。

## 出力契約 (Output Contract)

次の構造を使用する:

1. バスケット要約
2. 比較対象
3. 一致
4. 矛盾または古い前提
5. 欠落文脈
6. ユーザーアクションチェックリスト

末尾に次を付ける:

```text
This comparison is informational and not investment or trading advice.
```
