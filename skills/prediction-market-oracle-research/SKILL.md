---
name: prediction-market-oracle-research
description: Research prediction markets as data sources or oracle signals for products, agents, dashboards, and corporate decision intelligence. Use for source-grounded analysis of market-implied probabilities, caveats, and integration patterns without investment advice.
origin: ECC
---

# Prediction Market Oracle 調査 (Prediction Market Oracle Research)

prediction market を data source、forecasting input、oracle 的 signal、decision-intelligence 層として検討するときにこの skill を使用する。

## ガードレール (Guardrails)

- market price を客観的真実と扱わない。
- 投資助言や trading 推奨を提供しない。
- venue mechanics、liquidity、incentive、resolution ルールを implied signal から分離する。
- manipulation、thin liquidity、stale market、曖昧な outcome を明示する。
- on-chain または execution 連動システムでは、write 権限付与前に `llm-trading-agent-security` を実行する。

## 調査ワークフロー (Research Workflow)

1. signal が支える意思決定を定義する。
2. 関連 market、event、tag、venue を特定する。
3. timestamp と source link 付きで market-implied probability を記録する。
4. signal 品質を評価:
   - liquidity
   - spread
   - market age
   - 既知なら trader/incentive 集中
   - resolution authority
   - 地域・アカウント制限
5. filing、news、poll、research、customer data、internal KPI など非 market source と比較する。
6. 述べた意思決定に対して signal が usable、weak、unsuitable のいずれかを推奨する。

## 統合パターン (Integration Patterns)

- Research assistant: human analyst 向け source 根拠コンテキスト
- Dashboard signal: internal metric と並べた market-implied probability
- Agent memory input: 後から取得可能な timestamp 付き signal
- Alerting input: probability、spread、liquidity が閾値を超えたら通知
- Scenario planning: trade 自動化なしで複数 event outcome を比較

## 出力契約 (Output Contract)

次を使用:

1. decision context
2. market sources
3. signal quality
4. comparison sources
5. integration recommendation
6. caveats

末尾に:

```text
Prediction-market signals are informational inputs, not investment advice.
```
