---
description: "Git workflow: conventional commits、PR プロセス"
alwaysApply: true
---
# Git Workflow (Git Workflow)

## Commit Message 形式 (Commit Message Format)
```
<type>: <description>

<optional body>
```

Types: feat, fix, refactor, docs, test, chore, perf, ci

Note: Attribution は `~/.claude/settings.json` でグローバルに無効化。

## Pull Request ワークフロー (Pull Request Workflow)

PR 作成時:
1. 全 commit 履歴を分析（最新 commit のみではない）
2. `git diff [base-branch]...HEAD` で全変更を確認
3. 包括的な PR サマリーを起草
4. TODO 付き test plan を含める
5. 新 branch の場合は `-u` フラグで push

> git 操作の前の開発プロセス（planning、TDD、code review）については
> development workflow ルールを参照。
