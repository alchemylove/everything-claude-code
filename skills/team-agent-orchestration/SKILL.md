---
name: team-agent-orchestration
description: "Run team-based orchestration for agent squads using work items, ownership, agent Kanban, merge gates, and control pane handoffs."
origin: ECC
---

# Team Agent Orchestration (Team Agent Orchestration)

agent を単一 assistant ではなく team として管理するときにこの skill を使用する。目的は team-based orchestration を信頼可能にすること: 明確な work item、明示 ownership、agent Kanban state、branch 隔離、control pane 可視性、merge gate。

## 有効化タイミング (When To Activate)

- タスクが複数 agent、tool、harness、branch、worktree にまたがる
- user が team orchestration、agent Kanban、squad、conductor、control pane、manager、desktop app、Zellij、tmux、Hermes、Devin、Codex、Claude Code、multi-agent work に言及
- プロジェクトが people と agent 横断の共有 workflow state を必要とする
- 既存 agent fan-out が output は出すが merge 可能 product にならない

## 運用モデル (Operating Model)

各 agent を狭い contract を持つ teammate として扱う:

- **Owner**: work item に accountable な person または agent
- **Scope**: file、branch、tool surface、禁止領域
- **State**: backlog、ready、running、review、blocked、merged、archived
- **Evidence**: test、screenshot、log、review note、eval report
- **Merge gate**: integration を許可する正確な条件

## Agent Kanban (Agent Kanban)

session 横断で work を可視化するときに agent Kanban を使用。

| Column | Meaning | Exit Criteria |
| --- | --- | --- |
| Backlog | 候補 work item、未整形 | Acceptance criteria 記述済み |
| Ready | 整形済みで assign 可能 | Owner と branch/worktree 割当 |
| Running | agent が actively 作業中 | Handoff artifact と changed file が存在 |
| Review | 完了だが未 merge | test、diff review、risk check pass |
| Blocked | 外部入力または failed gate | Blocker に owner と next action |
| Merged | mainline に統合 | PR merged または local main 更新 |
| Archived | もはや relevant でない | 理由記録 |

各 card は次 schema に収まること:

```json
{
  "id": "agent-card-001",
  "title": "Build dynamic workflow skill",
  "owner": "codex",
  "state": "running",
  "branch": "product/dynamic-workflow-team-orchestration",
  "worktree": ".",
  "acceptance": [
    "Skill exists",
    "Tests cover required concepts",
    "Content artifact contains video and article angles"
  ],
  "merge_gate": "lint, focused tests, and catalog check pass",
  "handoff": "path/to/handoff.md"
}
```

## Team-Based Orchestration フロー (Team-Based Orchestration Flow)

1. **board を整形**: 曖昧な ambition を owner と merge gate 付き work item に変換
2. **実行モード選択**: single-agent、dynamic workflow mode、dmux/tmux、worktree fan-out、external desktop orchestrator
3. **境界割当**: card ごとに 1 owner、明確 file scope、integrator なしの重複 write 禁止
4. **agent 実行**: 各 agent は code だけでなく evidence と handoff note を書く
5. **順次 review**: test → diff review → security/risk → content/product polish
6. **意図的 merge**: 1 integrator が conflict を解決し control pane または status artifact を更新
7. **再利用 skill 抽出**: card pattern が繰り返すなら `skills/` に promote

## Control Pane 要件 (Control Pane Requirements)

team orchestration 向け有用 control pane は次を示す:

- active work item と agent Kanban state
- owner、harness、branch、worktree、last heartbeat
- handoff artifact、test、screenshot、PR への link
- owner と unblock action でグループ化された blocker
- vibes ではなく gate 別 merge readiness
- shared skill 化すべき再利用 workflow 候補

operator が「誰が owner か、何が変わったか、どの gate が失敗したか、何が安全に merge できるか」に答えられるまで automation を増やさない。

## Dynamic Workflow 互換 (Dynamic Workflow Compatibility)

card が dynamic workflow mode を必要とするとき:

- task-local harness を card owner 配下に
- input/output を card に保存
- Running → Review 前に eval を要求
- 繰り返し使用後のみ shared skill に promote

## 監視すべき失敗モード (Failure Modes To Watch)

- **Agent soup**: 多数 agent が走るが owner や merge gate がない
- **Invisible work**: 有用 output が chat transcript のみ
- **Board theater**: Kanban はあるが acceptance criteria がない card
- **Overlapping writes**: worktree なしで同一 file を parallel 編集
- **No product artifact**: 手順は doc を出すが runnable/publishable surface がない

## 出力標準 (Output Standard)

各 orchestration pass を次で終える:

- board/card 変更
- merged または pending branch
- test と eval evidence
- owner と next action 付き blocker
- 新 shared skill 候補
