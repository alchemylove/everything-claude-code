---
name: deployment-patterns
description: Deployment workflows, CI/CD pipeline patterns, Docker containerization, health checks, rollback strategies, and production readiness checklists for web applications.
origin: ECC
---

# デプロイメント パターン (Deployment Patterns)

本番環境でのデプロイメント戦略とパターン。

## 使用時期 (When to Activate)

- Kubernetesへのデプロイメント戦略
- ゼロダウンタイムアップグレード
- カナリアまたはブルーグリーンロールアウト
- 自動スケール構成
- デプロイメントヘルスチェック設定

## デプロイメント戦略 (Deployment Strategies)

### 1. ローリングデプロイメント (Rolling Deployment)

古いポッドを段階的に新しいものと置き換え。デフォルトで安全。

```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
```

### 2. ブルーグリーン (Blue-Green Deployment)

2つの完全な環境。即座にスイッチ可能。

### 3. カナリアデプロイメント (Canary Deployment)

トラフィックのわずかなパーセンテージを新バージョンに。段階的に増加。

## ベストプラクティス (Docker)

- [ ] ヘルスチェックエンドポイント実装
- [ ] ログシステム構成
- [ ] メトリクス収集セットアップ
- [ ] ロールバック計画作成
- [ ] 本番環境との間隔でテスト

詳細については、ドキュメントを参照してください。
