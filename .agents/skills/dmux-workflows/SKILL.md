---
name: dmux-workflows
description: Multi-agent orchestration using dmux (tmux pane manager for AI agents). Patterns for parallel agent workflows across Claude Code, Codex, OpenCode, and other harnesses. Use when running multiple agent sessions in parallel or coordinating multi-agent development workflows.
---

# dmux ワークフロー (dmux Workflows)

dmux を使って parallel AI agent session を orchestrate する。

## 有効化タイミング (When to Activate)

- 複数 agent session を並列実行する
- Claude Code、Codex、その他 harness 間で作業を調整する
- divide-and-conquer parallelism が有効な複雑タスク
- ユーザーが "run in parallel"、"split this work"、"use dmux"、"multi-agent" と言う

## dmux とは (What is dmux)

dmux は AI agent pane を管理する tmux ベース orchestration tool:
- `n` で prompt 付き新 pane を作成
- `m` で pane output を main session に merge
- 対応: Claude Code、Codex、OpenCode、Cline、Gemini、Qwen

**Install:** `npm install -g dmux` または [github.com/standardagents/dmux](https://github.com/standardagents/dmux)

## クイックスタート (Quick Start)

```bash
# Start dmux session
dmux

# Create agent panes (press 'n' in dmux, then type prompt)
# Pane 1: "Implement the auth middleware in src/auth/"
# Pane 2: "Write tests for the user service"
# Pane 3: "Update API documentation"

# Each pane runs its own agent session
# Press 'm' to merge results back
```

## ワークフローパターン (Workflow Patterns)

### パターン 1: Research + Implement (Pattern 1: Research + Implement)

research と implementation を parallel track に分割:

```
Pane 1 (Research): "Research best practices for rate limiting in Node.js.
  Check current libraries, compare approaches, and write findings to
  /tmp/rate-limit-research.md"

Pane 2 (Implement): "Implement rate limiting middleware for our Express API.
  Start with a basic token bucket, we'll refine after research completes."

# After Pane 1 completes, merge findings into Pane 2's context
```

### パターン 2: Multi-File Feature (Pattern 2: Multi-File Feature)

独立ファイルにまたがる作業を parallelize:

```
Pane 1: "Create the database schema and migrations for the billing feature"
Pane 2: "Build the billing API endpoints in src/api/billing/"
Pane 3: "Create the billing dashboard UI components"

# Merge all, then do integration in main pane
```

### パターン 3: Test + Fix Loop (Pattern 3: Test + Fix Loop)

1 pane で test、もう 1 pane で fix:

```
Pane 1 (Watcher): "Run the test suite in watch mode. When tests fail,
  summarize the failures."

Pane 2 (Fixer): "Fix failing tests based on the error output from pane 1"
```

### パターン 4: Cross-Harness (Pattern 4: Cross-Harness)

タスクごとに異なる AI tool を使う:

```
Pane 1 (Claude Code): "Review the security of the auth module"
Pane 2 (Codex): "Refactor the utility functions for performance"
Pane 3 (Claude Code): "Write E2E tests for the checkout flow"
```

### パターン 5: Code Review Pipeline (Pattern 5: Code Review Pipeline)

parallel review 視点:

```
Pane 1: "Review src/api/ for security vulnerabilities"
Pane 2: "Review src/api/ for performance issues"
Pane 3: "Review src/api/ for test coverage gaps"

# Merge all reviews into a single report
```

## ベストプラクティス (Best Practices)

1. **Independent tasks only.** 互いの output に依存するタスクは parallelize しない。
2. **Clear boundaries.** 各 pane は distinct file または concern で作業する。
3. **Merge strategically.** conflict を避けるため merge 前に pane output を review する。
4. **Use git worktrees.** file conflict しやすい作業は pane ごとに separate worktree を使う。
5. **Resource awareness.** 各 pane は API token を消費する — 合計 pane は 5〜6 未満に保つ。

## Git Worktree 統合 (Git Worktree Integration)

重複ファイルに触れるタスク向け:

```bash
# Create worktrees for isolation
git worktree add ../feature-auth feat/auth
git worktree add ../feature-billing feat/billing

# Run agents in separate worktrees
# Pane 1: cd ../feature-auth && claude
# Pane 2: cd ../feature-billing && claude

# Merge branches when done
git merge feat/auth
git merge feat/billing
```

## 補完ツール (Complementary Tools)

| Tool | What It Does | When to Use |
|------|-------------|-------------|
| **dmux** | tmux pane management for agents | Parallel agent sessions |
| **Superset** | Terminal IDE for 10+ parallel agents | Large-scale orchestration |
| **Claude Code Task tool** | In-process subagent spawning | Programmatic parallelism within a session |
| **Codex multi-agent** | Built-in agent roles | Codex-specific parallel work |

## トラブルシューティング (Troubleshooting)

- **Pane not responding:** agent session が input 待ちか確認。`m` で output を読む。
- **Merge conflicts:** pane ごとに git worktree で file change を isolate する。
- **High token usage:** parallel pane 数を減らす。各 pane は full agent session である。
- **tmux not found:** `brew install tmux` (macOS) または `apt install tmux` (Linux) で install。
