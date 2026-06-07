---
description: "Agent orchestration: 利用可能な agent、並列実行、多角的分析"
alwaysApply: true
---
# Agent Orchestration (Agent Orchestration)

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

## 即時 Agent 利用 (Immediate Agent Usage)

ユーザーのプロンプト不要:
1. 複雑な feature リクエスト — **planner** agent を使用
2. コードを書いた/変更した直後 — **code-reviewer** agent を使用
3. bug 修正または新 feature — **tdd-guide** agent を使用
4. アーキテクチャ上の判断 — **architect** agent を使用

## 並列タスク実行 (Parallel Task Execution)

独立した操作には常に並列 Task 実行を使用:

```markdown
# GOOD: 並列実行
3 つの agent を並列起動:
1. Agent 1: auth モジュールのセキュリティ分析
2. Agent 2: cache システムのパフォーマンスレビュー
3. Agent 3: utilities の型チェック

# BAD: 不要なときに逐次実行
まず agent 1、次に agent 2、最後に agent 3
```

## 多角的分析 (Multi-Perspective Analysis)

複雑な問題には split role sub-agent を使用:
- Factual reviewer
- Senior engineer
- Security expert
- Consistency reviewer
- Redundancy checker
