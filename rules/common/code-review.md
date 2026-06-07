# コードレビュー基準 (Code Review Standards)

## 目的 (Purpose)

コードレビューは、マージ前に品質、セキュリティ、保守性を確保します。本ルールは、いつ・どのようにレビューを行うかを定義します。

## レビュー実施タイミング (When to Review)

**必須のレビュートリガー:**

- コードを書いた／変更した後
- 共有ブランチへのコミット前
- セキュリティに敏感なコード（auth、payments、user data）を変更したとき
- アーキテクチャ変更を行ったとき
- pull request をマージする前

**レビュー依頼前の要件 (Pre-Review Requirements):**

レビューを依頼する前に、以下を確認する:

- すべての自動チェック（CI/CD）がパスしている
- マージコンフリクトが解消されている
- ブランチがターゲットブランチと同期している

## レビューチェックリスト (Review Checklist)

コード完了とマークする前に:

- [ ] コードが読みやすく、命名が適切
- [ ] 関数が焦点を絞っている（<50 行）
- [ ] ファイルが凝集している（<800 行）
- [ ] 深いネストがない（>4 レベル）
- [ ] エラーが明示的に処理されている
- [ ] ハードコードされた secret や credential がない
- [ ] `console.log` や debug 文がない
- [ ] 新機能にテストがある
- [ ] テストカバレッジが最低 80% を満たす

## セキュリティレビュートリガー (Security Review Triggers)

**次の場合は STOP し、security-reviewer agent を使用する:**

- 認証または認可コード
- ユーザー入力の処理
- データベースクエリ
- ファイルシステム操作
- 外部 API 呼び出し
- 暗号操作
- 決済または金融コード

## レビュー重大度レベル (Review Severity Levels)

| Level | Meaning | Action |
|-------|---------|--------|
| CRITICAL | Security vulnerability or data loss risk | **BLOCK** - Must fix before merge |
| HIGH | Bug or significant quality issue | **WARN** - Should fix before merge |
| MEDIUM | Maintainability concern | **INFO** - Consider fixing |
| LOW | Style or minor suggestion | **NOTE** - Optional |

## Agent の利用 (Agent Usage)

コードレビューには次の agent を使用する:

| Agent | Purpose |
|-------|---------|
| **code-reviewer** | General code quality, patterns, best practices |
| **security-reviewer** | Security vulnerabilities, OWASP Top 10 |
| **typescript-reviewer** | TypeScript/JavaScript specific issues |
| **python-reviewer** | Python specific issues |
| **go-reviewer** | Go specific issues |
| **rust-reviewer** | Rust specific issues |

## レビューワークフロー (Review Workflow)

```
1. Run git diff to understand changes
2. Check security checklist first
3. Review code quality checklist
4. Run relevant tests
5. Verify coverage >= 80%
6. Use appropriate agent for detailed review
```

## よくある問題 (Common Issues to Catch)

### セキュリティ (Security)

- ハードコードされた credential（API keys、passwords、tokens）
- SQL injection（クエリ内の文字列連結）
- XSS 脆弱性（エスケープされていないユーザー入力）
- Path traversal（サニタイズされていないファイルパス）
- CSRF 保護の欠如
- 認証バイパス

### コード品質 (Code Quality)

- 大きな関数（>50 行）— 小さく分割する
- 大きなファイル（>800 行）— モジュールを抽出する
- 深いネスト（>4 レベル）— early return を使用する
- エラーハンドリングの欠如 — 明示的に処理する
- Mutation パターン — 不変操作を優先する
- テストの欠如 — テストカバレッジを追加する

### パフォーマンス (Performance)

- N+1 クエリ — JOIN または batching を使用する
- ページネーションの欠如 — クエリに LIMIT を追加する
- 無制限クエリ — 制約を追加する
- キャッシュの欠如 — 高コスト操作をキャッシュする

## 承認基準 (Approval Criteria)

- **Approve**: CRITICAL または HIGH の問題なし
- **Warning**: HIGH の問題のみ（注意してマージ）
- **Block**: CRITICAL の問題あり

## 他ルールとの統合 (Integration with Other Rules)

本ルールは次と連携します:

- [testing.md](testing.md) - Test coverage requirements
- [security.md](security.md) - Security checklist
- [git-workflow.md](git-workflow.md) - Commit standards
- [agents.md](agents.md) - Agent delegation
