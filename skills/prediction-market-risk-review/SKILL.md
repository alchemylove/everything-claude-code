---
name: prediction-market-risk-review
description: Review prediction-market, basket, oracle, and trading-agent workflows for compliance, safety, data-quality, privacy, and execution risk. Use before any workflow handles venue auth, user portfolio data, API keys, or trade planning.
origin: ECC
---

# Prediction Market リスクレビュー (Prediction Market Risk Review)

prediction-market workflow が user 金融コンテキスト、venue 認証、portfolio data、automation、または execution 可能な tool に触れる前にこの skill を使用する。

## レビューゲート (Review Gates)

### アドバイス境界 (Advice Boundary)

- 出力が情報提供であることを確認する。
- buy/sell/hold/size 推奨を除去する。
- 手動 user 意思決定ポイントを明示する。

### Venue と規制境界 (Venue And Regulatory Boundary)

- venue 規約、地域制限、アカウント制限、API ルールを特定する。
- 該当する場合、betting、derivatives、securities、commodities の曖昧さを legal レビューにフラグする。
- venue 制限や rate limit を迂回しない。

### データ品質 (Data Quality)

- market liquidity、spread、resolution ルール、stale price、source timestamp を確認する。
- public venue data と Itô gated data を分離する。
- ラベルなしで public と private source を混在させない。

### セキュリティ (Security)

- private key、seed phrase、password の要求・保存をしない。
- `ITO_API_KEY` と venue API key を log や doc に含めない。
- デフォルトは read-only scope を使用する。
- private 実装が execution を追加する前に、circuit breaker、spend limit、dry run、human approval を要求する。

### プライバシー (Privacy)

- user portfolio、金融、knowledge-base data を最小化する。
- public artifact では private source を redact する。
- レビューに必要なフィールドのみ保持する。

## 出力契約 (Output Contract)

次を返す:

1. レビュー範囲
2. pass/warn/fail 所見
3. ブロックされた action
4. 必要な緩和策
5. 安全な次ステップ

execution 可能なステップが要求された場合、別途 implementation plan と明示的 user approval を要求する。
