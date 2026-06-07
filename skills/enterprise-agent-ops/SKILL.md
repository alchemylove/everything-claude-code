---
name: enterprise-agent-ops
description: Operate long-lived agent workloads with observability, security boundaries, and lifecycle management.
origin: ECC
---

# エンタープライズエージェント運用 (Enterprise Agent Ops)

単一 CLI セッションを超える運用制御が必要なクラウドホスト型または常時稼働エージェントシステムにこのスキルを使用する。

## 運用ドメイン (Operational Domains)

1. ランタイムライフサイクル（start、pause、stop、restart）
2. オブザーバビリティ（logs、metrics、traces）
3. 安全制御（scopes、permissions、kill switches）
4. 変更管理（rollout、rollback、audit）

## ベースライン制御 (Baseline Controls)

- 不変デプロイアーティファクト
- 最小権限認証情報
- 環境レベルのシークレット注入
- ハードタイムアウトとリトライ予算
- 高リスクアクション向け監査ログ

## 追跡すべきメトリクス (Metrics to Track)

- 成功率
- タスクあたり平均リトライ回数
- 復旧までの時間
- 成功タスクあたりコスト
- 障害クラス分布

## インシデントパターン (Incident Pattern)

障害が急増したとき:
1. 新規ロールアウトを凍結する
2. 代表的なトレースを取得する
3. 失敗ルートを隔離する
4. 最小の安全な変更でパッチする
5. 回帰 + セキュリティチェックを実行する
6. 段階的に再開する

## デプロイ統合 (Deployment Integrations)

このスキルは次と組み合わせる:
- PM2 ワークフロー
- systemd サービス
- コンテナオーケストレーター
- CI/CD ゲート
