# ECC v2.0.0-rc.1 公開証跡 - 2026-05-16 (ECC v2.0.0-rc.1 Publication Evidence - 2026-05-16)

これはリリース準備の証跡のみです。GitHub リリース、npm 公開、plugin tag、marketplace 提出、またはアナウンス投稿は作成しません。

## ソースコミット (Source Commit)

| 項目 (Field) | エビデンス (Evidence) |
| --- | --- |
| Upstream main | `6bced468d76b269243a6f0bd28472853aa78e0e4` |
| Git remote | `https://github.com/affaan-m/everything-claude-code.git` |
| Evidence scope | PR #1944、PR #1945、issue #1946 トリアージ、PR #1947 supply-chain protection、AgentShield PR #87、AgentShield PR #88、AgentShield PR #89、AgentShield PR #90、AgentShield PR #91、AgentShield PR #92、ECC-Tools PR #76、ECC-Tools PR #77、ECC-Tools PR #78、日本語ローカライズトリアージ、ITO-57 同期、operator dashboard 更新後の現在の `main` |
| Local status caveat | `git status --short --branch` は `## main...origin/main` と無関係な未追跡 `docs/drafts/` を表示 |

実際のリリースオペレーターは、公開前に最終リリースコミットからすべての公開向けチェックを、厳密にクリーンな checkout で繰り返す必要があります。

## キューとディスカッション状態 (Queue And Discussion State)

| サーフェス (Surface) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| Trunk PRs | `gh pr list --state open --json number,title,url --limit 20` | open PR 6 件: Dependabot #1959-#1963 および PR #1953。後者は日本語ローカライズパリティについて changes requested のまま open |
| Trunk issues | `gh issue list --state open --json number,title,url --limit 20` | open issue 3 件: #1951（保留中のローカライズ PR にリンク）、#1957 と #1958（次のキューバッチ待ち） |
| Platform audit | `node scripts/platform-audit.js --json --allow-untracked docs/drafts/` | ready; open PR 6 件、open issue 3 件、discussion maintainer-touch gaps 0、discussion missing-answer gaps 0、クリーン checkout 上の blocking dirty files 0; 現在の branch 生成では mirror 編集を local dirty work として検出 |
| Operator dashboard | `npm run operator:dashboard -- --json --allow-untracked docs/drafts/` | `dashboardReady: true`、`platformReady: true`、head `6bced468d76b269243a6f0bd28472853aa78e0e4` |

## マージとトリアージバッチ (Merge And Triage Batch)

| 項目 (Item) | 結果 (Result) |
| --- | --- |
| PR #1944 | statusline ANSI palette 更新を `50ac061f9e72d7daa137f1bd08760cf74e9b577d` としてマージ; マージ前に `node tests/hooks/ecc-statusline.test.js` と `node scripts/ci/validate-hooks.js` がパス |
| PR #1945 | `recsys-pipeline-architect` コミュニティ skill を `9e973b29fb1a2a0aeb9e6980017b67c3ddb05201` としてマージ; メンテナーパッチで catalog 件数を同期し、Unicode safety でブロックされた emoji を削除 |
| Issue #1946 | 修正済みメンテナーコメントでトリアージ済みとしてクローズ; Linear `ITO-60` が GateGuard proactive fact-forcing preflight UX を追跡 |
| PR #1947 | スケジュール済み supply-chain watch/advisory-source 証跡を `4093d1bb7a14db1b4d4ea5bd00f2073baf94bfb0` としてマージ; trunk に TanStack/Mini Shai-Hulud/node-ipc IOC scan と advisory-source report surfaces が scheduled watch 証跡に組み込まれた |
| AgentShield PR #87 | plugin-cache runtime-confidence 分類を `26bb44650663816d07180e0d20c1895e431a326c` としてマージ; インストール済み Claude plugin cache の findings は `runtimeConfidence: plugin-cache` を出力し、`plugins/cache` は `.claude` 配下の Claude cache にのみマップされ、cached hook 実装は active `hook-code` と誤ラベル付けされなくなった |
| AgentShield PR #88 | evidence-pack inspect/readback を `65ed6e2a87545dc99d962b58413f49096a4d70ec` としてマージ; `agentshield evidence-pack inspect` が report、policy、baseline、supply-chain、CI context、remediation、malformed artifact errors の検証済み JSON/text サマリーを出力 |
| AgentShield PR #89 | evidence-pack fleet routing を `521ada9091bb6d818511ab8589ae675b920c106a` としてマージ; `agentshield evidence-pack fleet <dirs...> [--json]` が複数の検証済み bundle を ready、security-blocker、policy-review、baseline-regression、supply-chain-review、invalid routes に集約し、finding、policy、baseline、supply-chain、remediation の合計を出力 |
| AgentShield PR #90 | fleet review items を `6d1c57c92000541d65a3b6bc366f0322d7d0dacc` としてマージ; `agentshield evidence-pack fleet --json` が route、severity、repository/target context、source evidence paths、reason、owner-ready recommendation を含む `reviewItems` を出力し、text CLI は `Review items` ブロックを表示 |
| AgentShield PR #91 | checksum-backed policy export を `73e1e3586dc4513a462e39c9799f75eea104e110` としてマージ; `agentshield policy export` が選択した pack ごとに 1 つの JSON policy ファイルと SHA-256 digest を含む `manifest.json` を書き込み、pack 選択、繰り返し owners、name prefixes、JSON 出力をサポート |
| AgentShield PR #92 | checksum-verified policy promotion を `e7e259dc6212b63a8e03a253ca6b8c1e3c2abff7` としてマージ; `agentshield policy promote` が export manifest と選択した policy digest を検証し、改ざん JSON を拒否し、multi-pack manifest では明示的な pack 選択を要求し、dry-run JSON review をサポートし、検証後にのみ active policy を書き込む |
| ECC-Tools PR #76 | AgentShield fleet-summary 消費を `5bde2328d15f584481fb6334e6960716dbf3e16f` としてマージ; hosted `security-evidence-review` が `agentshield-evidence/fleet-summary.json` を認識し、`evidence-pack-fleet` として分類し、invalid/security-blocker/policy/baseline/supply-chain fleet outcomes を hosted findings にルーティングし、malformed fleet JSON で fail closed |
| ECC-Tools PR #77 | hosted finding source-evidence 出力を `31fd883b3f0cee135aee4839b01d34855b7867f6` としてマージ; hosted job PR comments と check-run details に finding あたり最大 3 つの source evidence paths を含む `Evidence` 列を追加（AgentShield fleet 由来の findings を含む） |
| ECC-Tools PR #78 | AgentShield fleet-route harness review を `0d4eb949aa56f56da88e6654273a22ffb95983a1` としてマージ; hosted `harness-compatibility-audit` が fleet summaries を収集し、route target paths を Claude/Codex/OpenCode/MCP/plugin harness owners にマップし、source evidence paths 付きの owner-review findings を出力 |
| ITO-57 | PR #1947 advisory-source 証跡、マージ後ソース更新、IOC scan、npm audit/signature チェック、OpenAI app 更新 caveat で更新 |
| ITO-49 | AgentShield PR #87、#88、#89、#90、#91、#92 マージ証跡、ローカルテスト証跡、CI ステータス、ライブ `~/.claude` scan 分類件数、ローカル Mini Shai-Hulud protection scan 結果、policy promotion 検証で更新 |
| ITO-50 | ECC-Tools PR #76、PR #77、PR #78 マージ証跡、hosted security review 動作、hosted finding evidence-path 動作、harness fleet-route owner-review 動作、ローカルテスト証跡、リモート Verify/Security Audit/Workers build チェックで更新 |
| ITO-44 | キュークリーンアップ、dashboard 更新、残りの macro gaps で更新 |

## リリースゲートコマンド (Release Gate Commands)

| ゲート (Gate) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| Root suite | `npm test` | 2469 合格、0 失敗 |
| Rust `ecc2` suite | `cd ecc2 && cargo test` | 462 合格、0 失敗; 既存 dead-code/unused warnings のみ |
| Release surface | `node tests/docs/ecc2-release-surface.test.js` | 20 合格 |
| Harness adapters | `npm run harness:adapters -- --check` | PASS; adapter 11 件 |
| Harness audit | `npm run harness:audit -- --format json` | 70/70、top action なし |
| Observability readiness | `npm run observability:ready` | 21/21、ready yes |
| Supply-chain IOC scan | `npm run security:ioc-scan` | 合格; 227 ファイルを検査 |
| Advisory source refresh | `npm run security:advisory-sources -- --refresh --json` | ready; active source 9 件; Linear payload は sync 向けに引き続き `ITO-57` を指す |
| npm audit | `npm audit --audit-level=moderate` | 脆弱性 0 |
| npm signatures | `npm audit signatures` | registry signature 241 件検証済み; attestation 30 件検証済み |
| Dashboard renderer | `node tests/scripts/operator-readiness-dashboard.test.js` | 7 合格、0 失敗 |

## 現在の公開ブロッカー (Current Publication Blockers)

- このパスでは GitHub prerelease `v2.0.0-rc.1` はまだ作成されていない。
- npm `ecc-universal@2.0.0-rc.1` はまだ `next` dist-tag に公開されていない。
- Claude plugin tag と marketplace 伝播は approval-gated のまま。
- Codex repo-marketplace 配布は rc.1 で検証済みだが、公式 Plugin Directory 公開は OpenAI の coming-soon self-serve publishing surface によりブロックされている。
- ECC Tools billing/native-payments コピーは、ライブ Marketplace-managed test-account readback が announcement-ready gate を返すまでブロックされている。
- リリースノート、X、LinkedIn、GitHub release、longform コピーは、リリース/package/plugin URL が存在した後の最終ライブ URL がまだ必要。
- ローカル checkout には無関係な未追跡 `docs/drafts/` が残っているため、実際の公開前には厳密なクリーン checkout によるリリースパスが依然必要。

## 結果 (Result)

公開 PR キュー、issue キュー、discussion キューはクリアであり、rc.1 preview pack は 2026年5月16日に主要な Node、Rust、release-surface、harness、observability、supply-chain ゲートをパスした。これは公開準備を改善するが、`publication-readiness.md` にある approval-gated なリリース、package、plugin、アナウンス手順の代替にはならない。
