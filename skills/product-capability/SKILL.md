---
name: product-capability
description: Translate PRD intent, roadmap asks, or product discussions into an implementation-ready capability plan that exposes constraints, invariants, interfaces, and unresolved decisions before multi-service work starts. Use when the user needs an ECC-native PRD-to-SRS lane instead of vague planning prose.
---

# プロダクトケイパビリティ (Product Capability)

この skill は product intent を explicit engineering constraints に変換する。

ギャップが「何を build すべきか？」ではなく「implementation 開始前に何が真である必要があるか？」のときに使う。

## 使用タイミング (When to Use)

- PRD、roadmap item、discussion、founder note はあるが implementation constraints がまだ implicit
- feature が複数 service、repo、team を跨ぎ、coding 前に capability contract が必要
- product intent は明確だが architecture、data、lifecycle、policy 含意がまだ fuzzy
- senior engineer が review で同じ hidden assumption を繰り返し述べる
- harness と session を跨いで再利用できる artifact が必要

## 正規アーティファクト (Canonical Artifact)

repo に durable product-context file（`PRODUCT.md`、`docs/product/`、program-spec directory など）があれば、そこを更新する。

capability manifest がまだなければ、次の template で作成する:

- `docs/examples/product-capability-template.md`

目的は planning stack を増やすことではない。hidden capability constraints を durable で reusable にすることである。

## 非交渉ルール (Non-Negotiable Rules)

- product truth を捏造しない。未解決 question は明示する。
- user-visible promise と implementation detail を分離する。
- fixed policy、architecture preference、まだ open なものを区別する。
- 要求が既存 repo constraints と矛盾する場合、smooth over せず明確に述べる。
- 散在 ad hoc note より 1 つの reusable capability artifact を優先する。

## 入力 (Inputs)

必要なものだけ読む:

1. Product intent
   - issue、discussion、PRD、roadmap note、founder message
2. Current architecture
   - 関連 repo docs、contracts、schemas、routes、existing workflows
3. Existing capability context
   - `PRODUCT.md`、design docs、RFCs、migration notes、operating-model docs
4. Delivery constraints
   - auth、billing、compliance、rollout、backwards compatibility、performance、review policy

## コアワークフロー (Core Workflow)

### 1. capability を再述 (1. Restate the capability)

ask を 1 つの precise statement に圧縮する:

- user または operator は誰か
- ship 後に存在する新 capability は何か
- それにより変わる outcome は何か

この statement が弱いと implementation は drift する。

### 2. capability constraints を解決 (2. Resolve capability constraints)

implementation 前に成立すべき constraints を抽出する:

- business rules
- scope boundaries
- invariants
- trust boundaries
- data ownership
- lifecycle transitions
- rollout / migration requirements
- failure と recovery 期待

これらは often senior-engineer memory にしかないものである。

### 3. implementation-facing contract を定義 (3. Define the implementation-facing contract)

SRS-style capability plan を生成する:

- capability summary
- explicit non-goals
- actors と surfaces
- required states と transitions
- interfaces / inputs / outputs
- data model implications
- security / billing / policy constraints
- observability と operator requirements
- implementation を block する open questions

### 4. execution へ変換 (4. Translate into execution)

exact handoff で締める:

- direct implementation ready
- architecture review が先
- product clarification が先

有用なら次の ECC-native lane を指す:

- `project-flow-ops`
- `workspace-surface-audit`
- `api-connector-builder`
- `dashboard-builder`
- `tdd-workflow`
- `verification-loop`

## 出力形式 (Output Format)

次の順で結果を返す:

```text
CAPABILITY
- one-paragraph restatement

CONSTRAINTS
- fixed rules, invariants, and boundaries

IMPLEMENTATION CONTRACT
- actors
- surfaces
- states and transitions
- interface/data implications

NON-GOALS
- what this lane explicitly does not own

OPEN QUESTIONS
- blockers or product decisions still required

HANDOFF
- what should happen next and which ECC lane should take it
```

## 良い成果 (Good Outcomes)

- Product intent が PR 途中で hidden constraints を再発見せず implement 可能な具体性になった。
- Engineering review が memory や Slack context ではなく durable artifact を持つ。
- 結果 plan が Claude Code、Codex、Cursor、OpenCode、ECC 2.0 planning surface で再利用できる。
