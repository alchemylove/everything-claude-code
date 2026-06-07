---
name: product-lens
description: Use this skill to validate the "why" before building, run product diagnostics, and pressure-test product direction before the request becomes an implementation contract.
origin: ECC
---

# Product Lens — 作る前に考える (Product Lens — Think Before You Build)

この lane は product diagnosis を担当し、implementation-ready specification の執筆は担当しない。

user が durable な PRD-to-SRS または capability-contract artifact を必要とする場合は `product-capability` に handoff する。

## 使用タイミング (When to Use)

- いかなる feature の開始前 —「なぜ」を検証する
- 週次 product review — 正しいものを作っているか
- feature 選択で迷っているとき
- launch 前 — user journey の sanity check
- 曖昧な idea を engineering planning 前の product brief に変換するとき

## 仕組み (How It Works)

### モード 1: Product Diagnostic (Mode 1: Product Diagnostic)

YC office hours の自動化版。厳しい質問をする:

```
1. 誰のためか？（「開発者」ではなく具体的人物）
2. 痛みは何か？（頻度・深刻さ・現状の代替行動を定量化）
3. なぜ今か？（可能/必要になった変化は何か）
4. 10-star 版は？（金と時間が無限なら）
5. MVP は？（thesis を証明する最小物）
6. anti-goal は？（明示的に作らないもの）
7. うまくいっているとどう分かるか？（vibes ではなく metric）
```

出力: 回答・リスク・go/no-go 推奨を含む `PRODUCT-BRIEF.md`。

結果が「yes, build this」なら次の lane は `product-capability` であり、さらなる founder-theater ではない。

### モード 2: Founder Review (Mode 2: Founder Review)

founder lens で現プロジェクトをレビュー:

```
1. README、CLAUDE.md、package.json、最近の commit を読む
2. 推論: これは何を目指しているか
3. スコア: product-market fit シグナル (0-10)
   - Usage 成長軌道
   - Retention 指標（repeat contributor、return user）
   - Revenue シグナル（pricing page、billing code、Stripe 統合）
   - Competitive moat（コピー困難な点）
4. 特定: これを 10x する一つのこと
5. フラグ: 意味のないものを作っている箇所
```

### モード 3: User Journey Audit (Mode 3: User Journey Audit)

実際の user experience をマップ:

```
1. 新規 user として clone/install
2. すべての friction point を記録（混乱ステップ、エラー、不足 doc）
3. 各ステップの所要時間
4. 競合 onboarding と比較
5. スコア: time-to-value（最初の win までの時間）
6. 推奨: onboarding 改善トップ 3
```

### モード 4: Feature Prioritization (Mode 4: Feature Prioritization)

10 idea から 2 つを選ぶとき:

```
1. 候補 feature を列挙
2. 各を impact (1-5) × confidence (1-5) ÷ effort (1-5) でスコア
3. ICE score でランク
4. 制約適用: runway、team size、dependency
5. 出力: rationale 付き優先 roadmap
```

## 出力 (Output)

すべてのモードは essay ではなく actionable doc を出力する。各推奨に具体的 next step を含める。

## 統合 (Integration)

次と併用:
- `/browser-qa` — user journey audit 所見の検証
- `/design-system audit` — visual polish 評価
- `/canary-watch` — launch 後モニタリング
- `product-capability` — product brief を implementation-ready capability plan にする場合
