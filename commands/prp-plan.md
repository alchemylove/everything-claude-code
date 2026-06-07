---
description: コードベース分析とパターン抽出を含む、包括的な feature 実装計画を作成する
argument-hint: <feature description | path/to/prd.md>
---

> PRPs-agentic-eng by Wirasm から適応。PRP workflow シリーズの一部。

# PRP Plan

追加の質問なしで feature を 1 パスで実装するために必要な、コードベースのパターン・規約・コンテキストをすべて含む詳細で自己完結した実装計画を作成する。

**Core Philosophy**: 優れた plan には、実装に必要なものがすべて含まれる。すべての pattern、すべての規約、すべての gotcha — 一度キャプチャし、全体で参照する。

**Golden Rule**: 実装中にコードベースを検索する必要があるなら、その知識を今 plan にキャプチャする。

---

## Phase 0 — 検出 (DETECT)

`$ARGUMENTS` から入力タイプを判定する:

| Input Pattern | Detection | Action |
|---|---|---|
| Path ending in `.prd.md` | File path to PRD | Parse PRD, find next pending phase |
| Path to `.md` with "Implementation Phases" | PRD-like document | Parse phases, find next pending |
| Path to any other file | Reference file | Read file for context, treat as free-form |
| Free-form text | Feature description | Proceed directly to Phase 1 |
| Empty / blank | No input | Ask user what feature to plan |

### PRD 解析（入力が PRD の場合）(PRD Parsing)

1. `cat "$PRD_PATH"` で PRD ファイルを読む
2. **Implementation Phases** セクションを解析
3. status 別に phase を探す:
   - `pending` phase を探す
   - 依存チェーンを確認（先行 phase が `complete` である必要がある場合がある）
   - **次に実行可能な pending phase** を選択
4. 選択した phase から抽出:
   - Phase 名と説明
   - Acceptance criteria
   - 先行 phase への依存
   - scope のメモや制約
5. phase 説明を計画対象の feature として使用

pending phase が残っていない場合、すべての phase が完了したと報告する。

---

## Phase 1 — 解析 (PARSE)

feature 要件を抽出し明確化する。

### Feature の理解 (Feature Understanding)

入力（PRD phase または自由形式の説明）から以下を特定:

- **What** — 何を構築するか（具体的な成果物）
- **Why** — なぜ重要か（ユーザー価値）
- **Who** — 誰が使うか（対象ユーザー/システム）
- **Where** — どこに収まるか（コードベースのどの部分）

### User Story

以下の形式:
```
As a [type of user],
I want [capability],
So that [benefit].
```

### 複雑度評価 (Complexity Assessment)

| Level | Indicators | Typical Scope |
|---|---|---|
| **Small** | Single file, isolated change, no new dependencies | 1-3 files, <100 lines |
| **Medium** | Multiple files, follows existing patterns, minor new concepts | 3-10 files, 100-500 lines |
| **Large** | Cross-cutting concerns, new patterns, external integrations | 10+ files, 500+ lines |
| **XL** | Architectural changes, new subsystems, migration needed | 20+ files, consider splitting |

### 曖昧さゲート (Ambiguity Gate)

以下のいずれかが不明な場合、**続行前にユーザーに質問して停止**:

- コア成果物が曖昧
- 成功基準が未定義
- 複数の妥当な解釈がある
- 技術アプローチに大きな未知がある

推測しない。質問する。仮定に基づく plan は実装時に失敗する。

---

## Phase 2 — 探索 (EXPLORE)

深いコードベース intelligence を収集する。以下の各カテゴリについてコードベースを直接検索する。

### コードベース検索（8 カテゴリ）(Codebase Search)

各カテゴリについて、grep、find、ファイル読み取りで検索:

1. **Similar Implementations** — 計画対象に似た既存 feature を探す。類似の pattern、endpoint、component、module を探す。

2. **Naming Conventions** — コードベースの該当領域で、ファイル、関数、変数、クラス、export の命名方法を特定する。

3. **Error Handling** — 類似コードパスでエラーがどう捕捉・伝播・ログ・ユーザー返却されるかを探す。

4. **Logging Patterns** — 何が、どのレベルで、どの形式でログされるかを特定する。

5. **Type Definitions** — 関連する type、interface、schema とその構成を探す。

6. **Test Patterns** — 類似 feature のテスト方法を探す。テストファイルの場所、命名、setup/teardown、assertion スタイルを記録。

7. **Configuration** — 関連する config ファイル、環境変数、feature flag を探す。

8. **Dependencies** — 類似 feature が使う package、import、内部 module を特定する。

### コードベース分析（5 トレース）(Codebase Analysis)

関連ファイルを読み、以下をトレース:

1. **Entry Points** — リクエスト/アクションがシステムに入り、変更対象領域に到達する経路
2. **Data Flow** — 関連コードパスでデータがどう流れるか
3. **State Changes** — どの state がどこで変更されるか
4. **Contracts** — 守るべき interface、API、protocol
5. **Patterns** — 使用されているアーキテクチャパターン（repository、service、controller など）

### 統合ディスカバリ表 (Unified Discovery Table)

所見を単一の参照にまとめる:

| Category | File:Lines | Pattern | Key Snippet |
|---|---|---|---|
| Naming | `src/services/userService.ts:1-5` | camelCase services, PascalCase types | `export class UserService` |
| Error | `src/middleware/errorHandler.ts:10-25` | Custom AppError class | `throw new AppError(...)` |
| ... | ... | ... | ... |

---

## Phase 3 — 調査 (RESEARCH)

feature が外部ライブラリ、API、不慣れな技術を含む場合:

1. 公式ドキュメントを Web で検索
2. 使用例とベストプラクティスを探す
3. バージョン固有の gotcha を特定

各所見を以下の形式で:

```
KEY_INSIGHT: [what you learned]
APPLIES_TO: [which part of the plan this affects]
GOTCHA: [any warnings or version-specific issues]
```

確立された内部 pattern のみを使う feature の場合は、この phase をスキップし、「No external research needed — feature uses established internal patterns.」と記録する。

---

## Phase 4 — 設計 (DESIGN)

### UX 変換（該当する場合）(UX Transformation)

before/after のユーザー体験を文書化:

**Before:**
```
┌─────────────────────────────┐
│  [Current user experience]  │
│  Show the current flow,     │
│  what the user sees/does    │
└─────────────────────────────┘
```

**After:**
```
┌─────────────────────────────┐
│  [New user experience]      │
│  Show the improved flow,    │
│  what changes for the user  │
└─────────────────────────────┘
```

### Interaction Changes

| Touchpoint | Before | After | Notes |
|---|---|---|---|
| ... | ... | ... | ... |

UX 変更のない純粋な backend/内部変更の場合、「Internal change — no user-facing UX transformation.」と記録する。

---

## Phase 5 — アーキテクチャ (ARCHITECT)

### 戦略的設計 (Strategic Design)

実装アプローチを定義:

- **Approach**: 高レベル戦略（例: 「既存 repository pattern に従う新 service layer を追加」）
- **Alternatives Considered**: 検討した他のアプローチと却下理由
- **Scope**: 構築するものの具体的な境界
- **NOT Building**: スコープ外の明示的リスト（実装中のスコープ creep を防ぐ）

---

## Phase 6 — 生成 (GENERATE)

以下のテンプレートで完全な plan 文書を書く。`.claude/PRPs/plans/{kebab-case-feature-name}.plan.md` に保存。

ディレクトリがなければ作成:
```bash
mkdir -p .claude/PRPs/plans
```

### Plan Template

````markdown
# Plan: [Feature Name]

## Summary
[2-3 sentence overview]

## User Story
As a [user], I want [capability], so that [benefit].

## Problem → Solution
[Current state] → [Desired state]

## Metadata
- **Complexity**: [Small | Medium | Large | XL]
- **Source PRD**: [path or "N/A"]
- **PRD Phase**: [phase name or "N/A"]
- **Estimated Files**: [count]

---

## UX Design

### Before
[ASCII diagram or "N/A — internal change"]

### After
[ASCII diagram or "N/A — internal change"]

### Interaction Changes
| Touchpoint | Before | After | Notes |
|---|---|---|---|

---

## Mandatory Reading

Files that MUST be read before implementing:

| Priority | File | Lines | Why |
|---|---|---|---|
| P0 (critical) | `path/to/file` | 1-50 | Core pattern to follow |
| P1 (important) | `path/to/file` | 10-30 | Related types |
| P2 (reference) | `path/to/file` | all | Similar implementation |

## External Documentation

| Topic | Source | Key Takeaway |
|---|---|---|
| ... | ... | ... |

---

## Patterns to Mirror

Code patterns discovered in the codebase. Follow these exactly.

### NAMING_CONVENTION
// SOURCE: [file:lines]
[actual code snippet showing the naming pattern]

### ERROR_HANDLING
// SOURCE: [file:lines]
[actual code snippet showing error handling]

### LOGGING_PATTERN
// SOURCE: [file:lines]
[actual code snippet showing logging]

### REPOSITORY_PATTERN
// SOURCE: [file:lines]
[actual code snippet showing data access]

### SERVICE_PATTERN
// SOURCE: [file:lines]
[actual code snippet showing service layer]

### TEST_STRUCTURE
// SOURCE: [file:lines]
[actual code snippet showing test setup]

---

## Files to Change

| File | Action | Justification |
|---|---|---|
| `path/to/file.ts` | CREATE | New service for feature |
| `path/to/existing.ts` | UPDATE | Add new method |

## NOT Building

- [Explicit item 1 that is out of scope]
- [Explicit item 2 that is out of scope]

---

## Step-by-Step Tasks

### Task 1: [Name]
- **ACTION**: [What to do]
- **IMPLEMENT**: [Specific code/logic to write]
- **MIRROR**: [Pattern from Patterns to Mirror section to follow]
- **IMPORTS**: [Required imports]
- **GOTCHA**: [Known pitfall to avoid]
- **VALIDATE**: [How to verify this task is correct]

### Task 2: [Name]
- **ACTION**: ...
- **IMPLEMENT**: ...
- **MIRROR**: ...
- **IMPORTS**: ...
- **GOTCHA**: ...
- **VALIDATE**: ...

[Continue for all tasks...]

---

## Testing Strategy

### Unit Tests

| Test | Input | Expected Output | Edge Case? |
|---|---|---|---|
| ... | ... | ... | ... |

### Edge Cases Checklist
- [ ] Empty input
- [ ] Maximum size input
- [ ] Invalid types
- [ ] Concurrent access
- [ ] Network failure (if applicable)
- [ ] Permission denied

---

## Validation Commands

### Static Analysis
```bash
# Run type checker
[project-specific type check command]
```
EXPECT: Zero type errors

### Unit Tests
```bash
# Run tests for affected area
[project-specific test command]
```
EXPECT: All tests pass

### Full Test Suite
```bash
# Run complete test suite
[project-specific full test command]
```
EXPECT: No regressions

### Database Validation (if applicable)
```bash
# Verify schema/migrations
[project-specific db command]
```
EXPECT: Schema up to date

### Browser Validation (if applicable)
```bash
# Start dev server and verify
[project-specific dev server command]
```
EXPECT: Feature works as designed

### Manual Validation
- [ ] [Step-by-step manual verification checklist]

---

## Acceptance Criteria
- [ ] All tasks completed
- [ ] All validation commands pass
- [ ] Tests written and passing
- [ ] No type errors
- [ ] No lint errors
- [ ] Matches UX design (if applicable)

## Completion Checklist
- [ ] Code follows discovered patterns
- [ ] Error handling matches codebase style
- [ ] Logging follows codebase conventions
- [ ] Tests follow test patterns
- [ ] No hardcoded values
- [ ] Documentation updated (if needed)
- [ ] No unnecessary scope additions
- [ ] Self-contained — no questions needed during implementation

## Risks
| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| ... | ... | ... | ... |

## Notes
[Any additional context, decisions, or observations]
```

---

## 出力 (Output)

### Plan の保存 (Save the Plan)

生成した plan を以下に書く:
```
.claude/PRPs/plans/{kebab-case-feature-name}.plan.md
```

### PRD の更新（入力が PRD の場合）(Update PRD)

この plan が PRD phase から生成された場合:
1. phase status を `pending` から `in-progress` に更新
2. plan ファイルパスを phase の参照として追加

### ユーザーへの報告 (Report to User)

```
## Plan Created

- **File**: .claude/PRPs/plans/{kebab-case-feature-name}.plan.md
- **Source PRD**: [path or "N/A"]
- **Phase**: [phase name or "standalone"]
- **Complexity**: [level]
- **Scope**: [N files, M tasks]
- **Key Patterns**: [top 3 discovered patterns]
- **External Research**: [topics researched or "none needed"]
- **Risks**: [top risk or "none identified"]
- **Confidence Score**: [1-10] — likelihood of single-pass implementation

> Next step: Run `/prp-implement .claude/PRPs/plans/{name}.plan.md` to execute this plan.
```

---

## 検証 (Verification)

最終化前に、以下のチェックリストで plan を検証する:

### Context Completeness
- [ ] 関連ファイルをすべて発見し文書化済み
- [ ] 命名規約を例付きでキャプチャ済み
- [ ] エラーハンドリング pattern を文書化済み
- [ ] テスト pattern を特定済み
- [ ] 依存関係を一覧化済み

### Implementation Readiness
- [ ] すべての task に ACTION、IMPLEMENT、MIRROR、VALIDATE がある
- [ ] 追加のコードベース検索を要する task がない
- [ ] import パスが指定されている
- [ ] 該当箇所に GOTCHA を文書化済み

### Pattern Faithfulness
- [ ] コードスニペットは実際のコードベース例（捏造ではない）
- [ ] SOURCE 参照は実在のファイルと行番号を指す
- [ ] pattern は naming、errors、logging、data access、tests をカバー
- [ ] 新コードは既存コードと区別がつかない

### Validation Coverage
- [ ] static analysis コマンドを指定済み
- [ ] test コマンドを指定済み
- [ ] build 検証を含む

### UX Clarity
- [ ] before/after を文書化（または N/A）
- [ ] interaction 変更を一覧化
- [ ] UX の edge case を特定

### No Prior Knowledge Test
このコードベースに不慣れな開発者が、この plan だけで feature を実装でき、コードベース検索や質問なしで済むこと。そうでなければ不足コンテキストを追加する。

---

## 次のステップ (Next Steps)

- `/prp-implement <plan-path>` でこの plan を実行
- アーティファクトなしの簡易計画には `/plan` を使用
- scope が不明な場合は先に `/prp-prd` で PRD を作成
````
