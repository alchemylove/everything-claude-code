# エンタープライズ制御 (Enterprise Controls)

エンタープライズ向け ECC デプロイメント用のガバナンス入門ファイル。

## ベースライン (Baseline)

- Repository: https://github.com/affaan-m/everything-claude-code
- Recommended profile: full
- インストールマニフェスト、監査 allowlist、Codex ベースラインはレビュー対象として維持する。

## 承認の期待事項 (Approval Expectations)

- セキュリティに敏感なワークフロー変更には、レビュアーの明示的な承認が必要。
- 監査の抑制（suppression）には理由と、可能な限り狭い matcher を含めること。
- 生成された skill はチームへ広く展開する前にレビューすること。
