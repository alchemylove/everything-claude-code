---
description: "リーンで問題起点のPRDを生成し、実装計画のために/planに引き渡します。"
argument-hint: "[製品/機能のアイデア]（空欄 = 質問から開始）"
---

# PRDコマンド (PRD Command)

**プロダクト要件ドキュメント**を作成します — SDLCの要件フェーズのアーティファクトです。成功のために*何*が真でなければならないか、*なぜ*かを記録し、*どのように*の前で止まります。実装の分解は`/plan`に委任されます。

**入力**: `$ARGUMENTS`

## このコマンドのスコープ (Scope of this command)

| このコマンドがすること | このコマンドがしないこと |
|---|---|
| 問題とユーザーをフレーミング | アーキテクチャの設計 |
| 成功基準とスコープの記録 | ファイルの選択やパターンの記述 |
| 未解決の質問とリスクの一覧 | 実装タスクの列挙 |
| `.claude/prds/{name}.prd.md`の書き込み | 実装計画の作成 — それは`/plan` |

実装の詳細を書いていることに気づいたら、止めて削除してください。それは`/plan`に属します。

**アンチフラフルール**: 情報が不足している場合は`TBD — {方法}による検証が必要`と書く。もっともらしく聞こえる要件を作り出さないこと。

## ワークフロー (Workflow)

4つのフェーズ。各フェーズは単一のゲート — 質問し、ユーザーを待ち、次に進む。ネストしたループも並行リサーチの儀式もなし。

### フェーズ 1 — FRAME (Phase 1 — FRAME)

`$ARGUMENTS`が空の場合、質問:

> 何をビルドしたいですか？1〜2文で。

提供された場合、1文で再述し質問:

> 理解しました: *{restated}*。正しいですか、調整すべきですか？

次にフレーミング質問を一度に提示:

> 1. **誰が**この問題を抱えていますか？（具体的な役割またはセグメント）
> 2. **何が**観察可能な痛みですか？（想定されるニーズではなく行動を記述）
> 3. **なぜ**既存のもので解決できないのですか？
> 4. **なぜ今？** — 何が変わってこれを行う価値があるのですか？

ユーザーを待つ。回答（または明示的な"skip"）なしに先に進まない。

### フェーズ 2 — GROUND (Phase 2 — GROUND)

エビデンスを求める。これは最も短いフェーズであり、最も重要:

> この問題が実在し解決する価値があるというエビデンスは何ですか？（ユーザーの引用、サポートチケット、メトリクス、観察された行動、失敗したワークアラウンド — 具体的なもの何でも）

ユーザーにエビデンスがない場合、PRDのEvidenceセクションを`Assumption — needs validation via {user research | analytics | prototype}`と記録。これによりPRDの誠実さが保たれる。

### フェーズ 3 — DECIDE (Phase 3 — DECIDE)

スコープと仮説を一度に:

> 1. **仮説** — 完成させてください: *We believe **{capability}** will **{solve problem}** for **{users}**. We'll know we're right when **{measurable outcome}**.*
> 2. **MVP** — 仮説をテストするために必要な最小限は？
> 3. **Out of scope** — ユーザーが求めても明示的に**ビルドしない**ものは？
> 4. **Open questions** — アプローチを変える可能性のある不確実性は？

回答を待つ。

### フェーズ 4 — GENERATE & HAND OFF (Phase 4 — GENERATE & HAND OFF)

必要に応じてディレクトリを作成し、PRDを書き、報告。

```bash
mkdir -p .claude/prds
```

**出力パス**: `.claude/prds/{kebab-case-name}.prd.md`

#### PRDテンプレート (PRD Template)

```markdown
# {Product / Feature Name}

## Problem
{2–3 sentences: who has what problem, and what's the cost of leaving it unsolved?}

## Evidence
- {User quote, data point, or observation}
- {OR: "Assumption — needs validation via {method}"}

## Users
- **Primary**: {role, context, what triggers the need}
- **Not for**: {who this explicitly excludes}

## Hypothesis
We believe **{capability}** will **{solve problem}** for **{users}**.
We'll know we're right when **{measurable outcome}**.

## Success Metrics
| Metric | Target | How measured |
|---|---|---|
| {primary} | {number} | {method} |

## Scope
**MVP** — {the minimum to test the hypothesis}

**Out of scope**
- {item} — {why deferred}

## Delivery Milestones
<!-- Business outcomes, not engineering tasks. /plan turns each into a plan. -->
<!-- Status: pending | in-progress | complete -->

| # | Milestone | Outcome | Status | Plan |
|---|---|---|---|---|
| 1 | {name} | {user-visible change} | pending | — |
| 2 | {name} | {user-visible change} | pending | — |

## Open Questions
- [ ] {question that could change scope or approach}

## Risks
| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|

---
*Status: DRAFT — requirements only. Implementation planning pending via /plan.*
```

#### ユーザーへの報告 (Report to user)

```
PRD created: .claude/prds/{name}.prd.md

Problem:    {one line}
Hypothesis: {one line}
MVP:        {one line}

Validation status:
  Problem  {validated | assumption}
  Users    {concrete | generic — refine}
  Metrics  {defined | TBD}

Open questions: {count}

Next step: /plan .claude/prds/{name}.prd.md
  → /plan will pick the next pending milestone and produce an implementation plan.
```

## 統合 (Integration)

- `/plan <prd-path>` — PRDを消費し、次の保留中のマイルストーンの実装計画を作成。
- `tdd-workflow`スキル — テストファーストで計画を実装。
- `/pr` — PRDと計画を参照するPRを作成。

## 成功基準 (Success criteria)

- **PROBLEM_CLEAR**: 問題が具体的でエビデンスがある（または仮説としてフラグ付き）。
- **USER_CONCRETE**: プライマリユーザーが具体的な役割であり、"users"ではない。
- **HYPOTHESIS_TESTABLE**: 測定可能な成果が含まれている。
- **SCOPE_BOUNDED**: 明示的なMVPと明示的な out-of-scope。
- **NO_IMPLEMENTATION_DETAIL**: ファイルパス、ライブラリ、タスクの分解が含まれていない — もし含まれていたら`/plan`ステップに移動。
