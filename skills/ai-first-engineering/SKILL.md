---
name: ai-first-engineering
description: Engineering operating model for teams where AI agents generate a large share of implementation output.
origin: ECC
---

# AI ファーストエンジニアリング (AI-First Engineering)

AI 支援コード生成でリリースするチームのプロセス、レビュー、アーキテクチャを設計する際にこのスキルを使用します。

## プロセスの変化 (Process Shifts)

1. 計画の品質はタイピングスピードより重要。
2. 評価のカバレッジは個人的な自信より重要。
3. レビューの焦点は構文からシステムの動作へ。

## アーキテクチャ要件 (Architecture Requirements)

エージェントフレンドリーなアーキテクチャを優先する：
- 明示的な境界
- 安定したコントラクト
- 型付きインターフェース
- 決定論的なテスト

隠れた慣習に広がる暗黙の動作を避ける。

## AI ファーストチームでのコードレビュー (Code Review in AI-First Teams)

レビュー対象：
- 動作のリグレッション
- セキュリティの前提
- データの整合性
- 障害処理
- ロールアウトの安全性

自動化によって既にカバーされているスタイルの問題に費やす時間を最小化する。

## 採用と評価シグナル (Hiring and Evaluation Signals)

AI ファーストの強いエンジニア：
- 曖昧な作業を明確に分解する
- 測定可能な受け入れ基準を定義する
- 高シグナルのプロンプトと評価を生成する
- 納期プレッシャー下でリスクコントロールを強制する

## テスト標準 (Testing Standard)

生成されたコードのテストバーを引き上げる：
- 操作されたドメインに対する必須のリグレッションカバレッジ
- 明示的なエッジケースのアサーション
- インターフェース境界の統合チェック
