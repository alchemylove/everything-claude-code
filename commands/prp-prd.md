---
description: "対話型 PRD ジェネレーター — 問題ファースト、仮説駆動のプロダクト仕様。往復の質問付き"
argument-hint: "[feature/product idea] (blank = start with questions)"
---

# Product Requirements Document Generator

> PRPs-agentic-eng by Wirasm から適応。PRP workflow シリーズの一部。

**入力**: $ARGUMENTS

---

## あなたの役割 (Your Role)

次のような鋭いプロダクトマネージャーとして振る舞う:
- ソリューションではなく **PROBLEMS** から始める
- 構築前に証拠を要求する
- spec ではなく仮説で考える
- 仮定する前に明確化の質問をする
- 不確実性を正直に認める

**Anti-pattern**: セクションを fluff で埋めない。情報が欠けている場合は、もっともらしい要件を捏造せず「TBD - needs research」と書く。

---

## プロセス概要 (Process Overview)

```
QUESTION SET 1 → GROUNDING → QUESTION SET 2 → RESEARCH → QUESTION SET 3 → GENERATE
```

各質問セットは前の回答を土台にする。Grounding phase は仮定を検証する。

---

## Phase 1: 開始 — コア問題 (INITIATE - Core Problem)

**入力がない場合**、質問する:

> **What do you want to build?**
> Describe the product, feature, or capability in a few sentences.

**入力がある場合**、理解を言い換えて確認:

> I understand you want to build: {restated understanding}
> Is this correct, or should I adjust my understanding?

**GATE**: 続行前にユーザー応答を待つ。

---

## Phase 2: 基盤 — 問題発見 (FOUNDATION - Problem Discovery)

以下の質問をする（一度にすべて提示し、まとめて回答してもよい）:

> **Foundation Questions:**
>
> 1. **Who** has this problem? Be specific - not just "users" but what type of person/role?
>
> 2. **What** problem are they facing? Describe the observable pain, not the assumed need.
>
> 3. **Why** can't they solve it today? What alternatives exist and why do they fail?
>
> 4. **Why now?** What changed that makes this worth building?
>
> 5. **How** will you know if you solved it? What would success look like?

**GATE**: 続行前にユーザー応答を待つ。

---

## Phase 3: グラウンディング — 市場とコンテキスト調査 (GROUNDING - Market & Context Research)

基盤の回答の後、調査を行う:

**市場コンテキストを調査:**

1. 市場の類似プロダクト/feature を探す
2. 競合がこの問題をどう解くかを特定
3. 一般的な pattern と anti-pattern を記録
4. この領域の最近のトレンドや変化を確認

所見を直接リンク、主要インサイト、利用可能情報のギャップとともにまとめる。

**コードベースが存在する場合、並行して探索:**

1. プロダクト/feature アイデアに関連する既存機能を探す
2. 活用できる pattern を特定
3. 技術的制約や機会を記録

ファイル位置、コード pattern、観察された規約を記録する。

**所見をユーザーに要約:**

> **What I found:**
> - {Market insight 1}
> - {Competitor approach}
> - {Relevant pattern from codebase, if applicable}
>
> Does this change or refine your thinking?

**GATE**: 短い一時停止でユーザー入力を待つ（"continue" や調整でも可）。

---

## Phase 4: 深掘り — ビジョンとユーザー (DEEP DIVE - Vision & Users)

基盤 + 調査に基づき、質問する:

> **Vision & Users:**
>
> 1. **Vision**: In one sentence, what's the ideal end state if this succeeds wildly?
>
> 2. **Primary User**: Describe your most important user - their role, context, and what triggers their need.
>
> 3. **Job to Be Done**: Complete this: "When [situation], I want to [motivation], so I can [outcome]."
>
> 4. **Non-Users**: Who is explicitly NOT the target? Who should we ignore?
>
> 5. **Constraints**: What limitations exist? (time, budget, technical, regulatory)

**GATE**: 続行前にユーザー応答を待つ。

---

## Phase 5: グラウンディング — 技術的実現可能性 (GROUNDING - Technical Feasibility)

**コードベースが存在する場合、2 つの並行調査を行う:**

Investigation 1 — 実現可能性の探索:
1. 活用できる既存インフラを特定
2. 既に実装されている類似 pattern を探す
3. 統合ポイントと依存関係をマップ
4. 関連する設定と型定義を特定

ファイル位置、コード pattern、観察された規約を記録する。

Investigation 2 — 制約の分析:
1. 既存の関連 feature が end-to-end でどう実装されているかをトレース
2. 潜在的な統合ポイントを通るデータフローをマップ
3. アーキテクチャ pattern と境界を特定
4. 類似 feature に基づき複雑度を見積もる

存在するものを正確な file:line 参照で文書化する。提案はしない。

**コードベースがない場合、技術アプローチを調査:**

1. 他者が使った技術アプローチを探す
2. 一般的な実装 pattern を特定
3. 既知の技術的課題と落とし穴を記録

引用とギャップ分析付きで所見をまとめる。

**ユーザーに要約:**

> **Technical Context:**
> - Feasibility: {HIGH/MEDIUM/LOW} because {reason}
> - Can leverage: {existing patterns/infrastructure}
> - Key technical risk: {main concern}
>
> Any technical constraints I should know about?

**GATE**: 短い一時停止でユーザー入力を待つ。

---

## Phase 6: 意思決定 — スコープとアプローチ (DECISIONS - Scope & Approach)

最終的な明確化質問をする:

> **Scope & Approach:**
>
> 1. **MVP Definition**: What's the absolute minimum to test if this works?
>
> 2. **Must Have vs Nice to Have**: What 2-3 things MUST be in v1? What can wait?
>
> 3. **Key Hypothesis**: Complete this: "We believe [capability] will [solve problem] for [users]. We'll know we're right when [measurable outcome]."
>
> 4. **Out of Scope**: What are you explicitly NOT building (even if users ask)?
>
> 5. **Open Questions**: What uncertainties could change the approach?

**GATE**: 生成前にユーザー応答を待つ。

---

## Phase 7: 生成 — PRD の作成 (GENERATE - Write PRD)

**出力パス**: `.claude/PRPs/prds/{kebab-case-name}.prd.md`

必要ならディレクトリを作成: `mkdir -p .claude/PRPs/prds`

### PRD Template

```markdown
# {Product/Feature Name}

## Problem Statement

{2-3 sentences: Who has what problem, and what's the cost of not solving it?}

## Evidence

- {User quote, data point, or observation that proves this problem exists}
- {Another piece of evidence}
- {If none: "Assumption - needs validation through [method]"}

## Proposed Solution

{One paragraph: What we're building and why this approach over alternatives}

## Key Hypothesis

We believe {capability} will {solve problem} for {users}.
We'll know we're right when {measurable outcome}.

## What We're NOT Building

- {Out of scope item 1} - {why}
- {Out of scope item 2} - {why}

## Success Metrics

| Metric | Target | How Measured |
|--------|--------|--------------|
| {Primary metric} | {Specific number} | {Method} |
| {Secondary metric} | {Specific number} | {Method} |

## Open Questions

- [ ] {Unresolved question 1}
- [ ] {Unresolved question 2}

---

## Users & Context

**Primary User**
- **Who**: {Specific description}
- **Current behavior**: {What they do today}
- **Trigger**: {What moment triggers the need}
- **Success state**: {What "done" looks like}

**Job to Be Done**
When {situation}, I want to {motivation}, so I can {outcome}.

**Non-Users**
{Who this is NOT for and why}

---

## Solution Detail

### Core Capabilities (MoSCoW)

| Priority | Capability | Rationale |
|----------|------------|-----------|
| Must | {Feature} | {Why essential} |
| Must | {Feature} | {Why essential} |
| Should | {Feature} | {Why important but not blocking} |
| Could | {Feature} | {Nice to have} |
| Won't | {Feature} | {Explicitly deferred and why} |

### MVP Scope

{What's the minimum to validate the hypothesis}

### User Flow

{Critical path - shortest journey to value}

---

## Technical Approach

**Feasibility**: {HIGH/MEDIUM/LOW}

**Architecture Notes**
- {Key technical decision and why}
- {Dependency or integration point}

**Technical Risks**

| Risk | Likelihood | Mitigation |
|------|------------|------------|
| {Risk} | {H/M/L} | {How to handle} |

---

## Implementation Phases

<!--
  STATUS: pending | in-progress | complete
  PARALLEL: phases that can run concurrently (e.g., "with 3" or "-")
  DEPENDS: phases that must complete first (e.g., "1, 2" or "-")
  PRP: link to generated plan file once created
-->

| # | Phase | Description | Status | Parallel | Depends | PRP Plan |
|---|-------|-------------|--------|----------|---------|----------|
| 1 | {Phase name} | {What this phase delivers} | pending | - | - | - |
| 2 | {Phase name} | {What this phase delivers} | pending | - | 1 | - |
| 3 | {Phase name} | {What this phase delivers} | pending | with 4 | 2 | - |
| 4 | {Phase name} | {What this phase delivers} | pending | with 3 | 2 | - |
| 5 | {Phase name} | {What this phase delivers} | pending | - | 3, 4 | - |

### Phase Details

**Phase 1: {Name}**
- **Goal**: {What we're trying to achieve}
- **Scope**: {Bounded deliverables}
- **Success signal**: {How we know it's done}

**Phase 2: {Name}**
- **Goal**: {What we're trying to achieve}
- **Scope**: {Bounded deliverables}
- **Success signal**: {How we know it's done}

{Continue for each phase...}

### Parallelism Notes

{Explain which phases can run in parallel and why}

---

## Decisions Log

| Decision | Choice | Alternatives | Rationale |
|----------|--------|--------------|-----------|
| {Decision} | {Choice} | {Options considered} | {Why this one} |

---

## Research Summary

**Market Context**
{Key findings from market research}

**Technical Context**
{Key findings from technical exploration}

---

*Generated: {timestamp}*
*Status: DRAFT - needs validation*
```

---

## Phase 8: 出力 — 要約 (OUTPUT - Summary)

生成後、報告する:

```markdown
## PRD Created

**File**: `.claude/PRPs/prds/{name}.prd.md`

### Summary

**Problem**: {One line}
**Solution**: {One line}
**Key Metric**: {Primary success metric}

### Validation Status

| Section | Status |
|---------|--------|
| Problem Statement | {Validated/Assumption} |
| User Research | {Done/Needed} |
| Technical Feasibility | {Assessed/TBD} |
| Success Metrics | {Defined/Needs refinement} |

### Open Questions ({count})

{List the open questions that need answers}

### Recommended Next Step

{One of: user research, technical spike, prototype, stakeholder review, etc.}

### Implementation Phases

| # | Phase | Status | Can Parallel |
|---|-------|--------|--------------|
{Table of phases from PRD}

### To Start Implementation

Run: `/prp-plan .claude/PRPs/prds/{name}.prd.md`

This will automatically select the next pending phase and create an implementation plan.
```

---

## 質問フロー要約 (Question Flow Summary)

```
┌─────────────────────────────────────────────────────────┐
│  INITIATE: "What do you want to build?"                 │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  FOUNDATION: Who, What, Why, Why now, How to measure    │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  GROUNDING: Market research, competitor analysis        │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  DEEP DIVE: Vision, Primary user, JTBD, Constraints     │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  GROUNDING: Technical feasibility, codebase exploration │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  DECISIONS: MVP, Must-haves, Hypothesis, Out of scope   │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  GENERATE: Write PRD to .claude/PRPs/prds/              │
└─────────────────────────────────────────────────────────┘
```

---

## ECC との連携 (Integration with ECC)

PRD 生成後:
- `/prp-plan` で PRD phase から実装計画を作成
- PRD 構造なしの簡易計画には `/plan` を使用
- セッション間で PRD コンテキストを保持するには `/save-session` を使用

## 成功基準 (Success Criteria)

- **PROBLEM_VALIDATED**: 問題が具体的で根拠がある（または assumption と明記）
- **USER_DEFINED**: 主要ユーザーが具体的で汎用的でない
- **HYPOTHESIS_CLEAR**: 測定可能な成果を伴う検証可能な仮説
- **SCOPE_BOUNDED**: must-have が明確で out-of-scope が明示
- **QUESTIONS_ACKNOWLEDGED**: 不確実性が隠されず一覧化されている
- **ACTIONABLE**: 懐疑派でもなぜ構築する価値があるか理解できる
