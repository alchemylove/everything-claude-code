---
name: agent-architecture-audit
description: Full-stack diagnostic for agent and LLM applications. Audits the 12-layer agent stack for wrapper regression, memory pollution, tool discipline failures, hidden repair loops, and rendering corruption. Produces severity-ranked findings with code-first fixes. Essential for developers building agent applications, autonomous loops, or any LLM-powered feature.
origin: oh-my-agent-check
tools: Read, Write, Edit, Bash, Grep, Glob
---

# エージェント Architecture 監査 (Agent Architecture Audit)

wrapper layer、stale memory、retry loop、transport/rendering mutation の背後に failure を隠す agent system 向け diagnostic workflow。

## 有効化タイミング (When to Activate)

**必須:**
- agent/LLM-powered application を production リリースする前
- tool calling、memory、multi-step workflow を含む feature を ship する前
- wrapper layer 追加後に agent behavior が劣化した
- user が「agent が悪化している」「tool が flaky」と報告
- playground では同じ model が動くが wrapper 内で壊れる
- 15 分以上 debug しても root cause が不明

**特に重要:**
- 新しい prompt layer、tool definition、memory system を追加した
- system 内の agent 間で behavior が一貫しない
- 昨日は問題なかった model が今日 hallucinate する
- hidden repair/retry loop が response を黙って mutate している疑い

**使用しない:**
- 一般 code debug → `agent-introspection-debugging`
- code review → 言語別 reviewer agent
- security scan → `security-review` または `security-review/scan`
- agent performance benchmark → `agent-eval`
- 新 feature 実装 → 適切な workflow skill

## 12-Layer Stack (The 12-Layer Stack)

すべての agent system には次の layer がある。いずれも answer を corrupt しうる:

| # | Layer | 何が壊れるか |
|---|-------|----------------|
| 1 | System prompt | 矛盾する instruction、instruction bloat |
| 2 | Session history | 前 turn からの stale context injection |
| 3 | Long-term memory | session 横断 pollution、新 conversation への旧 topic |
| 4 | Distillation | 圧縮 artifact が pseudo-fact として再流入 |
| 5 | Active recall | context を浪費する冗長 re-summary layer |
| 6 | Tool selection | 誤 routing、required tool の skip |
| 7 | Tool execution | hallucinated execution — 呼んだと主張するが実行していない |
| 8 | Tool interpretation | tool output の誤読または無視 |
| 9 | Answer shaping | final response の format corruption |
| 10 | Platform rendering | transport layer mutation（UI/API/CLI が valid answer を mutate） |
| 11 | Hidden repair loops | 2 回目 LLM pass を走らせる silent fallback/retry agent |
| 12 | Persistence | expired state や cached artifact が live evidence として再利用 |

## 一般的な Failure Pattern (Common Failure Patterns)

### 1. Wrapper Regression

base model は正しい answer を出すが、wrapper layer が悪化させる。

**Symptoms:**
- playground/direct API では動くが agent 内で壊れる
- 新 prompt layer 追加後に既存 behavior が劣化
- agent は confident だが confidently wrong
- 「last update 前は動いていた」

### 2. Memory Contamination

history、memory retrieval、distillation 経由で旧 topic が新 conversation に leak する。

**Symptoms:**
- 無関係な過去 topic を agent が持ち出す
- user correction が stick しない（旧 memory が新情報を上書き）
- same-session artifact が pseudo-fact として再流入
- memory が bound なく grow し、時間とともに quality が低下

### 3. Tool Discipline Failure

tool は prompt に宣言されているが code で enforce されていない。model が skip または execution を hallucinate する。

**Symptoms:**
- prompt に "Must use tool X" があるが call せず answer する
- tool result は正しそうだが実際には実行されていない
- 異なる tool が同じ責務で競合
- 使うべきでない時に tool を使う、必須時に skip する

### 4. Rendering/Transport Corruption

agent 内部 answer は正しいが、platform layer が delivery 中に mutate する。

**Symptoms:**
- log は正しい answer、user には broken output
- markdown rendering、JSON parsing、streaming fragment が valid response を corrupt
- hidden fallback agent が delivery 前に answer を置換
- terminal と UI で output が異なる

### 5. Hidden Agent Layers

silent repair、retry、summarization、recall agent が explicit contract なしで動く。

**Symptoms:**
- internal generation と user delivery の間で output が変わる
- user が知らない "auto-fix" loop が 2 回目 LLM pass を実行
- 複数 agent が coordination なしに同一 output を modify
- answer が invisible layer によって "smoothed" または "corrected" される

## 監査 Workflow (Audit Workflow)

### フェーズ 1: Scope (Phase 1: Scope)

監査対象を定義:

- **Target system** — どの agent application か
- **Entrypoints** — user はどう interact するか
- **Model stack** — どの LLM/provider か
- **Symptoms** — user は何を報告しているか
- **Time window** — いつから始まったか
- **Layers to audit** — 12 layer のうちどれが該当するか

### フェーズ 2: Evidence Collection (Phase 2: Evidence Collection)

codebase から evidence を収集:

- **Source code** — agent loop、tool router、memory admission、prompt assembly
- **Logs** — session trace、tool call record
- **Config** — prompt template、tool schema、provider setting
- **Memory files** — SOP、knowledge base、session archive

`rg` で anti-pattern を検索:

```bash
# Tool requirements expressed only in prompt text (not code)
rg "must.*tool|必须.*工具|required.*call" --type md

# Tool execution without validation
rg "tool_call|toolCall|tool_use" --type py --type ts

# Hidden LLM calls outside main agent loop
rg "completion|chat\.create|messages\.create|llm\.invoke"

# Memory admission without user-correction priority
rg "memory.*admit|long.*term.*update|persist.*memory" --type py --type ts

# Fallback loops that run additional LLM calls
rg "fallback|retry.*llm|repair.*prompt|re-?prompt" --type py --type ts

# Silent output mutation
rg "mutate|rewrite.*response|transform.*output|shap" --type py --type ts
```

### フェーズ 3: Failure Mapping (Phase 3: Failure Mapping)

各 finding について文書化:

- **Symptom** — user が見るもの
- **Mechanism** — wrapper がどう cause するか
- **Source layer** — 12 layer のどれか
- **Root cause** — 最も深い cause
- **Evidence** — file:line または log:row reference
- **Confidence** — 0.0 から 1.0

### フェーズ 4: Fix Strategy (Phase 4: Fix Strategy)

デフォルト fix 順序（prompt-first ではなく code-first）:

1. **Code-gate tool requirements** — prompt テキストだけでなく code で enforce
2. **Remove or narrow hidden repair agents** — fallback を explicit contract 付きに
3. **Reduce context duplication** — prompt + history + memory + distillation への同一情報
4. **Tighten memory admission** — user correction > agent assertion
5. **Tighten distillation triggers** — 圧縮すべきでないものを圧縮しない
6. **Reduce rendering mutation** — transform せず pass-through
7. **Convert to typed JSON envelopes** — freeform prose ではなく structured internal flow

## Severity Model (Severity Model)

| Level | Meaning | Action |
|-------|---------|--------|
| `critical` | agent が confidently wrong operational behavior を出しうる | 次リリース前に fix |
| `high` | agent が頻繁に correctness/stability を degrade | 今 sprint で fix |
| `medium` | correctness は通常 survive するが output が fragile/wasteful | 次 cycle で計画 |
| `low` | 主に cosmetic または maintainability issue | backlog |

## 出力形式 (Output Format)

user には次の順で提示:

1. **Severity-ranked findings**（最も critical から）
2. **Architecture diagnosis**（どの layer が何を corrupt したか、なぜか）
3. **Ordered fix plan**（prompt-first ではなく code-first）

compliment や summary から始めない。system が broken なら直接そう述べる。

## クイック診断 Question (Quick Diagnostic Questions)

agent system 監査時に答える:

| # | Question | If Yes → |
|---|----------|----------|
| 1 | model は required tool を skip しても answer できるか？ | tool が code-gate されていない |
| 2 | 旧 conversation content が新 turn に現れるか？ | memory contamination |
| 3 | 同一情報が system prompt AND memory AND history にあるか？ | context duplication |
| 4 | platform は delivery 前に 2 回目 LLM pass を走らせるか？ | hidden repair loop |
| 5 | internal generation と user delivery で output が異なるか？ | rendering corruption |
| 6 | "must use tool X" rule は prompt テキストのみか？ | tool discipline failure |
| 7 | agent 自身の monologue が persistent memory になりうるか？ | memory poisoning |

## 避けるべき Anti-Pattern (Anti-Patterns to Avoid)

- wrapper-layer regression を falsify する前に model を blame しない
- contamination path を示さず memory を blame しない
- clean current state が dirty historical incident を消さない
- markdown prose を trustworthy internal protocol として扱わない
- code が enforce しないのに prompt の "must use tool" を accept しない
- finding は direct、evidence-backed、severity-ranked に保つ

## Report Schema (Report Schema)

監査は次の shape の structured report を出力:

```json
{
  "schema_version": "ecc.agent-architecture-audit.report.v1",
  "executive_verdict": {
    "overall_health": "high_risk",
    "primary_failure_mode": "string",
    "most_urgent_fix": "string"
  },
  "scope": {
    "target_name": "string",
    "model_stack": ["string"],
    "layers_to_audit": ["string"]
  },
  "findings": [
    {
      "severity": "critical|high|medium|low",
      "title": "string",
      "mechanism": "string",
      "source_layer": "string",
      "root_cause": "string",
      "evidence_refs": ["file:line"],
      "confidence": 0.0,
      "recommended_fix": "string"
    }
  ],
  "ordered_fix_plan": [
    { "order": 1, "goal": "string", "why_now": "string", "expected_effect": "string" }
  ]
}
```

## 関連 Skill (Related Skills)

- `agent-introspection-debugging` — agent runtime failure（loop、timeout、state error）の debug
- `agent-eval` — agent performance の head-to-head benchmark
- `security-review` — code/configuration の security audit
- `autonomous-agent-harness` — autonomous agent operation の setup
- `agent-harness-construction` — agent harness の scratch からの構築
