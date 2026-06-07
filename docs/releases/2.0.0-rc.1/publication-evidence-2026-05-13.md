# ECC v2.0.0-rc.1 公開証跡 - 2026-05-13 (ECC v2.0.0-rc.1 Publication Evidence - 2026-05-13)

これはリリース準備証跡のみです。GitHub release、npm publication、plugin tag、marketplace submission、announcement post は作成しません。

## ソースコミット (Source Commit)

| 項目 (Field) | エビデンス (Evidence) |
| --- | --- |
| Upstream main base | `797f283036904128bb1b348ae62019eb9f08cf39` |
| Evidence branch | `docs/release-readiness-20260513` |
| Evidence scope | PR #1846 後の現在の `main` および markdownlint のみの zh-CN CLAUDE list-marker 正規化 |
| Git remote | `https://github.com/affaan-m/everything-claude-code.git` |
| Local status caveat | working tree に無関係な未追跡 `docs/drafts/` ディレクトリが存在 |

実際のリリース担当者は、公開前に最終リリースコミットから clean checkout でこれらのチェックを繰り返す必要があります。

## キューとリリース状態 (Queue And Release State)

| サーフェス (Surface) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| GitHub PRs and issues | trunk、AgentShield、JARVIS、ECC-Tools、ECC-website 向け `gh pr list` / `gh issue list` | 追跡 repo 全体で open PR 0 件、open issue 0 件 |
| Trunk discussions | `affaan-m/everything-claude-code` 向け GraphQL discussion sweep | 最新 100 discussions はクローズ; open discussion backlog なし |
| npm audit signature gate | PR #1846 | `797f283` としてマージ; `npm audit` を実行する workflow は `npm audit signatures` が必要 |

## 必須コマンド証跡 (Required Command Evidence)

| エビデンス (Evidence) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| Harness audit | `npm run harness:audit -- --format json` | `overall_score: 70`, `max_score: 70`, top actions なし |
| Adapter scorecard | `npm run harness:adapters -- --check` | `Harness Adapter Compliance: PASS`; 11 adapters |
| Observability readiness | `npm run observability:ready -- --format json` | `overall_score: 16`, `max_score: 16`, `ready: true`, top actions なし |
| Root suite | `node tests/run-all.js` | `2376` passed, `0` failed |
| Markdown lint | `npx markdownlint-cli '**/*.md' --ignore node_modules` | 2 件の zh-CN CLAUDE docs を asterisk bullets から dash bullets に正規化後に合格 |
| Package surface | `node tests/scripts/npm-publish-surface.test.js` | `2/2` 合格; package surface は引き続き Python bytecode/cache artifacts を除外 |
| Release surface | `node tests/docs/ecc2-release-surface.test.js` | `18/18` 合格 |
| Rust surface | `cd ecc2 && cargo test` | `462/462` 合格; unused functions/fields の warnings のみ |

## セキュリティゲート証跡 (Security Gate Evidence)

| サーフェス (Surface) | コマンドまたはチェック (Command or check) | 結果 (Result) |
| --- | --- | --- |
| Local npm signature audit | PR #1846 前の `npm audit signatures` | 241 件の registry signature と 30 件の attestation を検証 |
| Local npm vulnerability audit | PR #1846 前の `npm audit --audit-level=high` | 脆弱性 0 件 |
| Workflow security validator | `node scripts/ci/validate-workflow-security.js` | 7 workflow files を検証 |
| Workflow validator tests | `node tests/ci/validate-workflow-security.test.js` | `11/11` 合格（新しい signature-gate ケースを含む） |
| GitHub CI for #1846 | current-head PR checks | 完全 OS/package-manager matrix が合格（`windows-latest / Node 18.x / pnpm` を含む） |

## 承認または外部アクションが依然必要なブロッカー (Blockers Still Requiring Approval Or External Action)

- GitHub prerelease `v2.0.0-rc.1` を作成または検証する。
- npm dist-tag `next` で `ecc-universal@2.0.0-rc.1` を公開する。
- 明示的な承認後にのみ Claude plugin tag を作成して push する。
- ライブ Claude/Codex/OpenCode marketplace submission パスを確認するか、手動 submission の担当者とステータスを記録する。
- launch copy で使用する前に ECC Tools billing/App/Marketplace の主張を検証する。
- リリースと package/plugin URL が存在した後、live URL で announcement copy を更新する。
