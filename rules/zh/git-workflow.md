# Git ワークフロー (Git Workflow)

## コミットメッセージ形式 (Commit Message Format)
```
<type>: <description>

<optional body>
```

Types: feat, fix, refactor, docs, test, chore, perf, ci

Note: Attribution disabled globally via ~/.claude/settings.json.

## Pull Request ワークフロー (Pull Request Workflow)

PR 作成時:
1. 全コミット履歴を分析する（最新コミットだけでなく）
2. `git diff [base-branch]...HEAD` で全変更を確認する
3. 包括的な PR サマリーを起草する
4. TODO 付きのテストプランを含める
5. 新規ブランチなら `-u` フラグで push する

> git 操作の前の開発プロセス（計画、TDD、コードレビュー）の全体は
> [development-workflow.md](./development-workflow.md) を参照。
