---
name: recsys-pipeline-architect
description: Design composable recommendation, ranking, and feed pipelines using the six-stage Source→Hydrator→Filter→Scorer→Selector→SideEffect framework popularized by xAI's open-sourced For You algorithm. Use this skill whenever the user is building any system that picks "the top K items for a (user, context)" — social feeds, content CMSs, RAG rerankers, task prioritizers, notification triage, search reranking, ad ranking.
origin: community
---

# recsys-pipeline-architect (recsys-pipeline-architect)

composable recommendation、ranking、feed pipeline 向け spec-and-scaffold skill。**6 段階パターン** — Source → Hydrator → Filter → Scorer → Selector → SideEffect — をエンコード。xAI 公開 [For You algorithm](https://github.com/xai-org/x-algorithm) (Apache 2.0) で普及。本 skill は pattern の独立再実装 (MIT) — 元コードはコピーしない。

Upstream: <https://github.com/mturac/recsys-pipeline-architect>

## 使用タイミング (When to Use)

- user が「(user/context) に top K item」を選ぶシステムを構築したい
- user が「X をどう rank すべきか」や feed/personalization 問題を説明
- scoring function はあるが周辺 pipeline plumbing が必要
- 単一 relevance score から tunable weight 付き multi-action prediction へ移行
- LLM/ML scorer を包み、filter、hydrator、side-effect、実行可能 scaffold が必要（TypeScript / Go / Python）
- トリガー: "recommendation system", "feed algorithm", "ranking pipeline", "for you feed", "candidate pipeline", "content recommender", "pipeline architecture for recsys", "RAG retrieval reranker"

## 使用しないとき (When NOT to Use)

- model architecture 作業（transformer design、two-tower retrieval、embedding training）— 本 skill は model 周辺の plumbing であり model 自体ではない
- 純 ML training pipeline — scoring function は user 責任
- デプロイ済み pipeline の運用（monitoring、autoscaling）— 範囲外

## 6 段階フレームワーク (The six-stage framework)

| # | Stage | Job | Parallel? |
|---|---|---|---|
| 1 | **Source** | 1 つ以上の origin から candidate を取得 | Yes — 複数 source は parallel |
| 2 | **Hydrator** | filter/score に必要な metadata で各 candidate を enrich | Yes — 独立 hydrator は parallel |
| 3 | **Filter** | 表示すべきでない candidate を drop（blocked、expired、duplicate、ineligible） | Sequential — 各 filter はより少ない item を見る |
| 4 | **Scorer** | 残った candidate に 1 つ以上の score を付与 | Sequential — 後続 scorer は先行 score を見る |
| 5 | **Selector** | final score で sort し top K を返す | Single op |
| 6 | **SideEffect** | 配信 ID を cache、impression を log、event 発行、counter 更新 | Async — response をブロックしてはならない |

### この順序の理由 (Why this exact order)

- hydration 前に source: enrich のコストを払う前に candidate を把握
- filter 前に hydration: 多くの filter は source が持たない metadata を必要とする
- score 前に filter: score は高コスト。不適格を先に落とす
- Scorer chain（単一 scorer ではない）: 実システムは ML score + diversity rerank + business rule を合成
- score 後に selector: scoring を deterministic・cacheable に保つ
- SideEffect は最後かつ async: user response をブロックしない

## 起動時ワークフロー (Workflow when invoked)

8 ステップで user を導く:

1. **use case を明確化**（1 ラウンド、3 問）: rank 対象 item？input context？language/runtime？
2. **candidate source を特定**: 通常 in-network（followed/owned/subscribed）+ out-of-network（ML retrieval / trending / similar-to-liked）
3. **必要 hydration を列挙**: 各 filter/scorer が source が持たない data を何必要とするか
4. **filter を列挙**: duplicate、self、age、block/mute、previously-served、eligibility。順序重要 — 安いものを先に
5. **scorer chain を設計**: primary (ML) → combiner (weight 付き multi-action) → diversity → business rules
6. **Selector**: final score 降順、top K（または in/out-network の stratified mix）
7. **SideEffects**: served ID cache、impression event、counter 更新、analytics — すべて fire-and-forget
8. **user の stack で scaffold を生成**

## 表面化すべき主要トレードオフ (Key trade-offs to surface)

### 1. Single score vs multi-action prediction

- **Single score**: relevance 用に 1 model。挙動変更 → retrain
- **Multi-action**: 多数 action の `P(action)` を予測し serving 時に weight で合成。挙動変更 → weight 変更。retrain 不要

X For You は positive/negative weight 付き multi-action。頻繁チューニングが想定なら multi-action を推奨。

### 2. Candidate isolation in scoring

- **Isolated**: 各 candidate を独立 score。deterministic、cacheable
- **Joint**: batch 中で candidate が互いに attend（例: transformer over batch）。表現力は高いが batch 間で non-deterministic

デフォルトは isolation。明示的理由（batch-aware diversity 等）があるときのみ joint。

### 3. Online vs offline

- **Request-time (online)**: 各 request で pipeline。latency budget 100–300ms。デフォルト
- **Pre-computed (offline batch)**: 定期実行、結果 cache。低 latency、低 freshness
- **Hybrid**: candidate retrieval offline、ranking online

## 厳守ルール (Hard rules)

1. **benchmark 数値を捏造しない。**「どれだけ速い？」→「workload 次第、自分で計測」
2. **帰属規律。** pattern 言及時は "popularized by xAI's open-sourced For You algorithm" / `github.com/xai-org/x-algorithm` (Apache 2.0)
3. **商標不使用。** artifact を "X-like" と名付けたり "For You" branding を使わない。pattern は自由、brand は不可。推奨名: "candidate pipeline", "feed pipeline", "ranking pipeline", "recsys pipeline"
4. **トレードオフを表面化。** multi-action vs single、isolation vs joint、online vs offline — 黙ってデフォルトしない
5. **生成 scaffold は実行可能であること。** pseudocode を code のふりをしない
6. **Filter 順序が重要。** 安い → 高い。universal → user-specific
7. **Side effect はブロックしない。** goroutine / promise without await / asyncio task 等の fire-and-forget

## アンチパターン (Anti-Patterns)

- filter 前に score（drop される candidate に compute を浪費）
- 同期 side effect（cache write / impression が response をブロック）
- 複数 objective（engagement vs safety vs diversity vs ads）なのに単一 "relevance" score
- デフォルトで joint scoring（non-deterministic、cache 困難、rerank stage と合成しにくい）
- 実行不能な pseudocode を「説明用」として生成

## Upstream 内容 (Upstream contents)

<https://github.com/mturac/recsys-pipeline-architect> には:

- 完全 8 ステップ workflow 付き `SKILL.md`
- 5 本の on-demand reference doc: 4 言語 interface (TS/Go/Python/Rust)、multi-action scoring、candidate isolation、filter cookbook (12 patterns)、scorer cookbook
- 3 つの実行可能 example scaffold（各 test suite green）
- v0.1.0 tag、MIT license、pattern は xAI X For You (Apache 2.0) に帰属

Install: `npx skills add mturac/recsys-pipeline-architect`
