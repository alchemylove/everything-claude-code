# メトリクスとスポンサーシッププレイブック (Metrics and Sponsorship Playbook)

このファイルはスポンサーコールとエコシステムパートナーレビュー向けの実用スクリプトです。

## 追跡すべき項目 (What to Track)

毎回の更新で4カテゴリを使ってください：

1. **Distribution** — npm パッケージと GitHub App installs
2. **Adoption** — stars、forks、contributors、リリース cadence
3. **Product surface** — commands/skills/agents とクロスプラットフォームサポート
4. **Reliability** — テスト合格数と本番 bug のターンアラウンド

## ライブメトリクスの取得 (Pull Live Metrics)

### npm downloads

```bash
# Weekly downloads
curl -s https://api.npmjs.org/downloads/point/last-week/ecc-universal
curl -s https://api.npmjs.org/downloads/point/last-week/ecc-agentshield

# Last 30 days
curl -s https://api.npmjs.org/downloads/point/last-month/ecc-universal
curl -s https://api.npmjs.org/downloads/point/last-month/ecc-agentshield
```

### GitHub repository adoption

```bash
gh api repos/affaan-m/ECC \
  --jq '{stars:.stargazers_count,forks:.forks_count,contributors_url:.contributors_url,open_issues:.open_issues_count}'
```

### GitHub traffic (maintainer access required)

```bash
gh api repos/affaan-m/ECC/traffic/views
gh api repos/affaan-m/ECC/traffic/clones
```

### GitHub App installs

GitHub App install 数は現在 Marketplace/App ダッシュボードが最も信頼できます。
最新値は次から取得：

- [ECC Tools Marketplace](https://github.com/marketplace/ecc-tools)

## まだ公開計測できないもの (What Cannot Be Measured Publicly (Yet))

- Claude plugin の install/download 数は現在公開 API では露出していません。
- パートナー会話では、npm メトリクス + GitHub App installs + リポジトリ traffic を proxy bundle として使ってください。

## 推奨スポンサーパッケージング (Suggested Sponsor Packaging)

交渉の出発点として次を使ってください：

- **Pilot Partner:** `$200/month`
  - 最初のパートナーシップ検証とシンプルな月次スポンサー更新に最適。
- **Growth Partner:** `$500/month`
  - roadmap チェックインと実装フィードバックループを含む。
- **Strategic Partner:** `$1,000+/month`
  - マルチタッチ協業、ローンチサポート、より深い運用アライメント。

## 60秒トーキングトラック (60-Second Talking Track)

コールでこれを使ってください：

> ECC は設定リポジトリではなく、エージェント harness パフォーマンスシステムとして位置づけられています。
> 採用は npm distribution、GitHub App installs、リポジトリ成長で追跡します。
> Claude plugin installs は構造的に公開では過小計上されるため、ブレンドメトリクスモデルを使います。
> プロジェクトは本番グレードの hook 信頼性と大規模な合格テストスイートで、Claude Code、Cursor、OpenCode、Codex app/CLI をサポートします。

ローンチ準備済みソーシャルコピースニペットは [`social-launch-copy.md`](./social-launch-copy.md) を参照。
