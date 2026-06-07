---
description: コードベース理解とアーキテクチャ重視のガイド付き feature 開発
---

既存コードを理解してから新規コードを書くことを重視する、構造化された feature 開発ワークフロー。

## フェーズ (Phases)

### 1. ディスカバリー (Discovery)

- feature リクエストを注意深く読む
- 要件、制約、受け入れ基準を特定
- リクエストが曖昧な場合は明確化の質問をする

### 2. コードベース探索 (Codebase Exploration)

- `code-explorer` を使って関連する既存コードを分析
- 実行パスとアーキテクチャ層をトレース
- 統合ポイントと規約を理解

### 3. 明確化の質問 (Clarifying Questions)

- 探索結果を提示
- 設計とエッジケースに関する的を絞った質問をする
- 続行する前にユーザーの回答を待つ

### 4. アーキテクチャ設計 (Architecture Design)

- `code-architect` を使って feature を設計
- 実装ブループリントを提供
- 実装前に承認を待つ

### 5. 実装 (Implementation)

- 承認された設計に従って feature を実装
- 適切な場合は TDD を優先
- コミットは小さく焦点を絞る

### 6. 品質レビュー (Quality Review)

- `code-reviewer` を使って実装をレビュー
- critical および important な問題に対処
- テストカバレッジを検証

### 7. サマリー (Summary)

- 構築内容を要約
- フォローアップ項目や制限を列挙
- テスト手順を提供
