---
description: "Development workflow: plan、TDD、review、commit パイプライン"
alwaysApply: true
---
# Development Workflow (Development Workflow)

> このルールは git 操作の前に行われる feature 開発プロセス全体を、git workflow ルールに拡張する。

Feature Implementation Workflow は開発パイプラインを記述する: planning、TDD、code review、その後 git へ commit。

## Feature 実装ワークフロー (Feature Implementation Workflow)

1. **まず Plan**
   - **planner** agent で実装計画を作成
   - 依存関係とリスクを特定
   - phase に分割

2. **TDD アプローチ**
   - **tdd-guide** agent を使用
   - まず test を書く（RED）
   - test を通す実装（GREEN）
   - リファクタ（IMPROVE）
   - 80%+ coverage を検証

3. **Code Review**
   - コード記述直後に **code-reviewer** agent を使用
   - CRITICAL と HIGH の issue に対処
   - 可能なら MEDIUM の issue も修正

4. **Commit & Push**
   - 詳細な commit message
   - conventional commits 形式に従う
   - commit message 形式と PR プロセスは git workflow ルールを参照
