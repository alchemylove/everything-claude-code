# Agent オーケストレーション (Agent Orchestration)

## 利用可能な Agent (Available Agents)

`~/.claude/agents/` に配置:

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| planner | Implementation planning | Complex features, refactoring |
| architect | System design | Architectural decisions |
| tdd-guide | Test-driven development | New features, bug fixes |
| code-reviewer | Code review | After writing code |
| security-reviewer | Security analysis | Before commits |
| build-error-resolver | Fix build errors | When build fails |
| e2e-runner | E2E testing | Critical user flows |
| refactor-cleaner | Dead code cleanup | Code maintenance |
| doc-updater | Documentation | Updating docs |
| rust-reviewer | Rust code review | Rust projects |
| harmonyos-app-resolver | HarmonyOS app development | HarmonyOS/ArkTS projects |

## 即時 Agent 利用 (Immediate Agent Usage)

ユーザーのプロンプトは不要:
1. 複雑な feature 要求 — **planner** agent を使用
2. コードを書いた／変更した直後 — **code-reviewer** agent を使用
3. bug 修正または新 feature — **tdd-guide** agent を使用
4. アーキテクチャ上の判断 — **architect** agent を使用

## 並列タスク実行 (Parallel Task Execution)

独立した操作には常に並列 Task 実行を使用する:

```markdown
# GOOD: Parallel execution
Launch 3 agents in parallel:
1. Agent 1: Security analysis of auth module
2. Agent 2: Performance review of cache system
3. Agent 3: Type checking of utilities

# BAD: Sequential when unnecessary
First agent 1, then agent 2, then agent 3
```

## 多角的分析 (Multi-Perspective Analysis)

複雑な問題には、役割分割した sub-agent を使用する:
- Factual reviewer
- Senior engineer
- Security expert
- Consistency reviewer
- Redundancy checker
