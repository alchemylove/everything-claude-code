# ECC v2.0.0-rc.1 公開証跡 - 2026-05-13 Post-Hardening (ECC v2.0.0-rc.1 Publication Evidence - 2026-05-13 Post-Hardening)

これはリリース準備証跡のみです。GitHub release、npm publication、plugin tag、marketplace submission、announcement post は作成しません。

## ソースコミット (Source Commit)

| 項目 (Field) | エビデンス (Evidence) |
| --- | --- |
| Upstream main base | `209abd403b7eaa968c6d4fa67be82e04b55706d6` |
| Evidence branch | `docs/post-hardening-release-evidence-20260513` |
| Evidence scope | PR #1850 と PR #1851 後の現在の `main` |
| Git remote | `https://github.com/affaan-m/everything-claude-code.git` |
| Local status caveat | working tree に無関係な未追跡 `docs/drafts/` ディレクトリが存在 |

実際のリリース担当者は、公開前に最終リリースコミットから clean checkout でこれらのチェックを繰り返す必要があります。

## キューとリリース状態 (Queue And Release State)

| サーフェス (Surface) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| GitHub PRs and issues | trunk、AgentShield、JARVIS 向け `gh pr list` / `gh issue list` | アクセス可能な `affaan-m` repo で open PR 0 件、open issue 0 件 |
| Trunk discussions | `affaan-m/everything-claude-code` 向け GraphQL discussion count | open discussions 0 件 |
| Dependabot alerts | trunk、AgentShield、JARVIS 向け Dependabot alert API | open alerts 0 件 |
| Release state | `gh release view v2.0.0-rc.1` | 未作成のまま; release は approval-gated |

ECC-Tools organization repo counts は、この pass では現在の GraphQL token がそれらの org repos を解決できないため再チェックしていません。以前の post-#42 ローカル checkout handoff では、ECC-Tools の両 repo が 0 open PRs と 0 open issues と記録されています。

## 前回証跡以降に取り込まれた Hardening (Hardening Landed Since Previous Evidence)

| PR | マージコミット (Merge commit) | エビデンス (Evidence) |
| --- | --- | --- |
| #1850 | `248673271455e9dc85b8add2a6ab76107b718639` | read-only analyzer agents と zh-CN コピーから `Bash` tool access を削除; その surface の AgentShield high findings は 21 -> 18 に減少、新規 high findings なし |
| #1851 | `209abd403b7eaa968c6d4fa67be82e04b55706d6` | write-permission workflows で `actions/checkout` credential persistence を無効化し、workflow-security validator ルールを追加してそのガードを維持 |

## 必須コマンド証跡 (Required Command Evidence)

| エビデンス (Evidence) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| Harness audit | `npm run harness:audit -- --format json` | `overall_score: 70`, `max_score: 70`, top actions なし |
| Adapter scorecard | `npm run harness:adapters -- --check` | `Harness Adapter Compliance: PASS`; 11 adapters |
| Observability readiness | `npm run observability:ready -- --format json` | `overall_score: 21`, `max_score: 21`, `ready: true`, top actions なし; Release Safety 3/3 を含む |
| Workflow security validator | `node scripts/ci/validate-workflow-security.js` | 7 workflow files を検証 |
| Workflow validator tests | `node tests/ci/validate-workflow-security.test.js` | 14/14 合格 |
| Release surface | `node tests/docs/ecc2-release-surface.test.js` | 18/18 合格 |
| Package surface | `node tests/scripts/npm-publish-surface.test.js` | 2/2 合格 |
| Root suite | `node tests/run-all.js` | 2381/2381 合格, 0 failed |
| Markdown lint | `npx markdownlint-cli '**/*.md' --ignore node_modules --ignore docs/drafts` | 合格 |
| Rust surface | `cd ecc2 && cargo test` | 462/462 合格; unused functions/fields の warnings のみ |
| GitGuardian Security Checks | post-hardening security PR 上の GitHub check | マージ前に合格 |

## サプライチェーン証跡 (Supply-Chain Evidence)

| サーフェス (Surface) | コマンドまたはチェック (Command or check) | 結果 (Result) |
| --- | --- | --- |
| Local npm vulnerability audit | `npm audit --json` | 0 vulnerabilities |
| Local npm signature audit | `npm audit signatures` | 241 verified registry signatures と 30 verified attestations |
| Rust advisory audit | `cd ecc2 && cargo audit -q` | 無出力で合格 |
| TanStack / Mini Shai-Hulud IOC check | 影響 package namespace、payload ファイル名、既知 commit marker の grep | 影響 package への runtime または lockfile dependency なし; worm IOC マッチなし |
| GitGuardian Security Checks | post-hardening security PR 上の GitHub check | マージ前に合格 |

## 外部アドバイザリマッピング (External Advisory Mapping)

2026年5月の TanStack インシデントは、3つの workflow クラスを通じて ECC リリースリスクにマッピングされます:

- untrusted PR code を実行または checkout する `pull_request_target` workflows;
- fork、base、release workflow の信頼境界を横断する shared dependency caches;
- writable token または OIDC token が後続の process execution に露出する release jobs。

ECC の現在の guardrails は、次を通じてそれらのクラスをカバーしています:

- `workflow_run` と `pull_request_target` workflows での untrusted checkout refs の拒否;
- `pull_request_target` と `id-token: write` workflows での shared caches の拒否;
- workflows が `npm audit` を実行する場合の必須 `npm audit signatures`;
- write permissions を持つ workflows での必須 `npm ci --ignore-scripts`;
- write permissions を持つ workflows での `actions/checkout` に対する必須 `persist-credentials: false`。

## 承認または外部アクションが依然必要なブロッカー (Blockers Still Requiring Approval Or External Action)

- GitHub prerelease `v2.0.0-rc.1` を作成または検証する。
- npm dist-tag `next` で `ecc-universal@2.0.0-rc.1` を公開する。
- 明示的な承認後にのみ Claude plugin tag を作成して push する。
- ライブ Claude/Codex/OpenCode marketplace submission パスを確認するか、手動 submission の担当者とステータスを記録する。
- launch copy で使用する前に ECC Tools billing/App/Marketplace の主張を検証する。
- リリースと package/plugin URL が存在した後、live URL で announcement copy を更新する。
