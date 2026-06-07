# ECC v2.0.0-rc.1 公開証跡 - 2026-05-15 (ECC v2.0.0-rc.1 Publication Evidence - 2026-05-15)

これはリリース準備証跡のみです。GitHub release、npm publication、plugin tag、marketplace submission、announcement post は作成しません。

## ソースコミット (Source Commit)

| 項目 (Field) | エビデンス (Evidence) |
| --- | --- |
| Upstream main base | `1949d75e18e59a37de269d88b188fc701f5cf122` |
| Evidence branch | `codex/rc1-agentshield-86-evidence` |
| Evidence scope | PR #1932、#1933、#1934、#1935、#1936 後の現在の `main`; AgentShield #86; ECC-Tools #75 |
| Git remote | `https://github.com/affaan-m/everything-claude-code.git` |
| Local status caveat | この docs refresh 前、working tree に無関係な未追跡 `docs/drafts/` ディレクトリが存在 |

実際のリリース担当者は、公開前に最終リリースコミットから clean checkout で、すべての publish-facing チェックを繰り返す必要があります。

## キューとディスカッション状態 (Queue And Discussion State)

| サーフェス (Surface) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| Trunk PRs/issues | `affaan-m/everything-claude-code` 向け `gh pr list` と `gh issue list` | open PR 0 件、open issue 0 件 |
| AgentShield PRs/issues | `affaan-m/agentshield` 向け `gh pr list` と `gh issue list` | open PR 0 件、open issue 0 件 |
| JARVIS PRs/issues | `affaan-m/JARVIS` 向け `gh pr list` と `gh issue list` | open PR 0 件、open issue 0 件 |
| ECC Tools PRs/issues | `ECC-Tools/ECC-Tools` 向け `env -u GITHUB_TOKEN gh pr list` と `env -u GITHUB_TOKEN gh issue list` | open PR 0 件、open issue 0 件 |
| ECC website PRs/issues | `ECC-Tools/ECC-website` 向け `env -u GITHUB_TOKEN gh pr list` と `env -u GITHUB_TOKEN gh issue list` | open PR 0 件、open issue 0 件 |
| Trunk discussions | GraphQL discussion count と maintainer-touch sweep | discussions 合計 58 件; 5月15日 maintainer コメント後 maintainer touch なし 0 件 |
| Other repo discussions | AgentShield、JARVIS、ECC Tools、ECC website 向け GraphQL discussion count | discussions 無効または合計 0 件 |
| Platform audit | `node scripts/platform-audit.js --json --allow-untracked docs/drafts/` | ready; open PR 0/20、open issue 0/20、maintainer touch 必要 discussion 0、conflicting open PR 0、blocking dirty file 0 |

ECC-Tools organization は、設定済み GitHub host credential で到達可能です。この shell では、エクスポートされた `GITHUB_TOKEN` がその credential を上書きし、`ECC-Tools/*` で誤った 404/403 失敗を引き起こします。その environment override がクリーンアップされるまで、ECC Tools 検証コマンドには `env -u GITHUB_TOKEN` を使用してください。

## Linear ロードマップ状態 (Linear Roadmap State)

詳細な実行ロードマップは、Linear project にあります:

<https://linear.app/itomarkets/project/ecc-platform-roadmap-52b328ee03e1>

この project には 16 の issue-level lanes と 5 milestones があります:

| マイルストーン (Milestone) | Issue |
| --- | --- |
| Security and Access Baseline | `ITO-44`, `ITO-57`, `ITO-58` |
| ECC 2.0 Preview and Publication | `ITO-45`, `ITO-46`, `ITO-47`, `ITO-56` |
| AgentShield Enterprise Iteration | `ITO-48`, `ITO-49` |
| ECC Tools Next-Level Platform | `ITO-50`, `ITO-51`, `ITO-52`, `ITO-53`, `ITO-54`, `ITO-59` |
| Legacy Audit and Salvage | `ITO-55` |

Linear に追加された project documents:

- Roadmap Index and Current Execution Baseline（ロードマップ索引と現行実行ベースライン）
- Status Update 2026-05-15（2026-05-15 ステータス更新）
- GitHub Queue Snapshot 2026-05-15（2026-05-15 GitHub キュースナップショット）
- Completion Audit Snapshot 2026-05-15（2026-05-15 完了監査スナップショット）
- Discussion Queue Evidence 2026-05-15（2026-05-15 Discussion キューエビデンス）
- ECC-Tools Access Evidence 2026-05-15（2026-05-15 ECC-Tools アクセスエビデンス）

## サプライチェーン証跡 (Supply-Chain Evidence)

| サーフェス (Surface) | エビデンス (Evidence) |
| --- | --- |
| PR #1921 | Mini Shai-Hulud/TanStack フォローアップ向け supply-chain IOC 拡張をマージ |
| Node IPC follow-up / PR #1924 | 2026-05-14 `node-ipc` 悪意ある version、hash、DNS、runtime IOC カバレッジを追加 |
| PR #1926 | `platform:audit` と `security-ioc-scan` コマンド surface および release workflow IOC ゲートを追加 |
| PR #1932 | `scripts/platform-audit.js` JSON/Markdown/file-output モードを追加し、queue、discussion、roadmap、release エビデンスを terminal-only 出力ではなく durable artifact として記録可能に |
| PR #1933 | home-scan IOC カバレッジを Claude `settings.local.json`、`.claude/hooks/hooks.json`、macOS/Linux/Windows 向け user-level VS Code / Code Insiders `tasks.json` に拡張 |
| PR #1934 | 通常 CI dependency cache を restore-only `actions/cache/restore` 使用に切り替え、test job が mutable dependency state を shared cache に書き戻さないように |
| PR #1935 | 並列 test 実行下でも Rust release-surface ゲートを維持する test-only serialized current-dir guard で `ecc2` current-directory-mutating tests を安定化 |
| PR #1940 | `.github/workflows/supply-chain-watch.yml` を追加（6 時間ごとにスケジュール）。TanStack/Mini Shai-Hulud/node-ipc IOC scan と npm signature/audit チェックが durable `supply-chain-ioc-report.json` artifact を生成 |
| PR #1941 | CI test workflow から GitHub Actions dependency cache 使用を削除、npm/pnpm/Yarn/Bun install で package-manager lifecycle scripts を無効化、既存 Actions cache を purge、unsafe install/cache パターンを拒否する validator test を追加 |
| AgentShield PR #83 | TanStack、Mistral、OpenSearch、Guardrails、UiPath、Squawk、Claude Code / VS Code persistence、dead-man switch artifact 向け Mini Shai-Hulud IOC カバレッジをマージ |
| AgentShield PR #84 | より広い Mini Shai-Hulud full-campaign affected-package table（追加 `@cap-js`、`@draftlab`、`@tallyui`、`intercom-client`、`lightning` 等）をマージ |
| AgentShield PR #85 | GitHub Action supply-chain verification、gating、evidence pack を追加し、AgentShield enterprise scanner release path に verified registry-signature surface を提供 |
| AgentShield PR #86 | 任意 environment variable と token を bundle 外に保ちつつ、whitelist 済み GitHub Actions workflow、commit、run、runtime provenance 付き `ci-context.json` を AgentShield evidence pack に追加 |
| ECC-Tools PR #75 | live Marketplace-managed test-account readback が ready になるまで public billing 主張をブロックする native GitHub payments announcement gate を強化 |
| Trunk merge commits | `f04702bdac132662c8496e817bcd850c86e2b854`, `ee85e1482e3d6322ddb2706392ea0fc97469bd26`, `13585f1092c92fa3f20ffe0d756e40c5720b0de5`, `553d507ea63bc252e815a924c0d2baea961351a1`, `c0bac4d6ced7f78a5464c6e3fd8cfbb43515a9d5`, `c2c54e7c0b84a213848b9ab3dfeb3ae16fb9844d`, `6b8a49a6eed11cc7df19d8b1f2add085b37cf466`, `1949d75e18e59a37de269d88b188fc701f5cf122`, `6951b8d5d29d13cac6b89b461104ad03838553de`, `f7035b5644ffc857879b71c39353b2141f17c3f0` |
| AgentShield merge commits | `f899b27ba3fa60ec7e0dca41cc2dadcb1a1fb75d`, `d1aa5313afd915d0b7296e57aabaeb979b1ea93b`, `908d8f3a52a6a65b21e737339b56906603eb1345`, `69a5e25b675b77666d0c96abc22639a5ba883403` |
| ECC-Tools merge commits | `6d00d67043e92cadc80f160bfe947115bfef33b1` |
| Local IOC tests | `node tests/ci/scan-supply-chain-iocs.test.js` | 15/15 合格 |
| Unicode safety | `node scripts/ci/check-unicode-safety.js` | 合格 |
| IOC scan | `node scripts/ci/scan-supply-chain-iocs.js --root <ECC-workspace> --home` | no-lifecycle install refresh 後、229 files を検査して合格 |
| npm registry verification | `npm audit signatures` | 241 registry signatures と 30 attestations を検証; `npm audit --audit-level=high` は脆弱性 0 |
| Actions cache purge | `gh cache delete --all --succeed-on-no-caches` | 完了; `gh cache list --limit 20` は cache なし |
| Rust release-surface gate | `cd ecc2 && cargo test` | 462/462 合格; 既存 14 dead-code/unused warnings のみ |
| Root suite | `node tests/run-all.js` | 2442/2442 合格, 0 failed |
| Repo sweeps | 対象 persistence path チェック | active `gh-token-monitor`、`pgsql-monitor`、`transformers.pyz`、`pgmonitor.py` artifact なし |

5月15日の IOC 拡張では、OpenSearch/Mistral/Guardrails/UiPath/Squawk スタイルの campaign variants、`opensearch_init.js`、`vite_setup.mjs`、dead-drop/session protocol strings、AI-tooling persistence surfaces のカバレッジを追加しました。secret scanners をトリガーする full high-entropy indicators はコミットしていません。
5月15日の node-ipc follow-up は、`node-ipc@9.1.6`、`9.2.3`、`10.1.1`、`10.1.2`、`11.0.0`、`11.1.0`、`12.0.1`、および `node-ipc.cjs` payload hash、malicious tarball hashes、DNS exfil domains、Socket が報告した runtime markers をブロックします。
AgentShield PR #83 は、対応する scanner-side enterprise coverage を追加します: version-pinned package detections、`.claude` / `.vscode` automation-surface discovery、`gh-token-monitor` LaunchAgent/systemd/local-bin artifact detection、network/payload IOCs、built action/CLI bundles、1758/1758 local tests、merge 前の green GitHub Actions verification。
AgentShield PR #84 は、current Wiz table で報告された追加の affected npm package scopes と unscoped packages を追加し、`dist/action.js` と `dist/index.js` を再ビルドし、merge 前に 1758/1758 local tests と full AgentShield GitHub Actions matrix を pass することで、後続の full-campaign package-table gap を閉じます。
AgentShield PR #85 と trunk PRs #1934、#1940、#1941 は、IOC detection から release-path hardening へ応答を拡張します: AgentShield は action surface の registry-signature evidence を記録し、trunk には scheduled IOC watch workflow があり、active supply-chain hardening 中は trunk CI が test install matrix で dependency caches や package-manager lifecycle scripts を使用しなくなりました。
AgentShield PR #86 は、次の evidence-pack provenance slice を完了します: `agentshield scan --evidence-pack <dir>` は `ci-context.json` を書き込み、その artifact を signed bundle digest に含め、bundle README に記載し、`GITHUB_TOKEN` などの token-bearing environment variables が long-lived security-review artifacts にコピーされないことを検証します。この PR は merge 前に local build、typecheck、lint、1764/1764 tests、Node 18、20、22 全体の full GitHub Actions matrix を pass しました。
PR #1933 は、documented な Claude Code と VS Code automation paths の実用的な workstation persistence gap を閉じます。package uninstall 後も残る user-level config files を含みます。

## Preview Pack 状態 (Preview Pack State)

`preview-pack-manifest.md` は、rc.1 preview-pack boundary を組み立てます:

- release notes、quickstart、launch checklist、publication readiness、naming matrix、5月15日 evidence;
- public Hermes 特化 surface としての `docs/HERMES-SETUP.md` と `skills/hermes-imports/SKILL.md`;
- cross-harness、harness-adapter、observability、progress-sync docs;
- release/package/plugin publication 後に final live URL が必要な X、LinkedIn、article、Telegram、demo collateral;
- GitHub release、npm `next` publish、Claude plugin、Codex plugin、ECC Tools billing/product-readiness、announcements の明示的ブロッカー。

preview pack は final clean-checkout gating 向けに組み立て済みだが、依然として publication action ではない。

## Codex Marketplace 証跡 (Codex Marketplace Evidence)

OpenAI の現在の Codex plugin docs は、repo/personal marketplace distribution と official Plugin Directory を区別している。Repo marketplace は `.agents/plugins/marketplace.json` にあり、`codex plugin marketplace add <source>` は GitHub shorthand、Git URLs、SSH URLs、local marketplace roots を追加できる。Official Plugin Directory publishing と self-serve management は coming soon として文書化されている:

- <https://developers.openai.com/codex/plugins/build#add-a-marketplace-from-the-cli>
- <https://developers.openai.com/codex/plugins/build#how-codex-uses-marketplaces>
- <https://developers.openai.com/codex/plugins/build#publish-official-public-plugins>

| サーフェス (Surface) | エビデンス (Evidence) |
| --- | --- |
| CLI shape | `codex plugin marketplace add --help` は GitHub shorthand、Git URLs、SSH URLs、local marketplace roots、`--ref`、Git-only `--sparse` をサポート |
| Repo marketplace | `.agents/plugins/marketplace.json` は marketplace root から `source.path: "./"` で `ecc@2.0.0-rc.1` を公開 |
| Local add smoke | `HOME="$(mktemp -d)" codex plugin marketplace add <local-checkout>` が marketplace `ecc` を追加し、installed marketplace root を `<local-checkout>` として記録（実 Codex config には触れない） |
| README alignment | `.codex-plugin/README.md` は stale な `codex plugin install` ではなく `codex plugin marketplace add` を使用 |
| Public-directory status | rc.1 のサポート Codex 配布パスは repo-marketplace/manual install; 公式 Plugin Directory submission は OpenAI self-serve publishing 可用性待ちのままブロック |

## 現在の公開ブロッカー (Current Publication Blockers)

- この pass では GitHub prerelease `v2.0.0-rc.1` は依然として作成されていません。
- npm `ecc-universal@2.0.0-rc.1` は `next` dist-tag にまだ公開されていません。
- Claude plugin tag と marketplace propagation は approval-gated のままです。
- Codex plugin repo-marketplace distribution は rc.1 で検証済みですが、official Plugin Directory publishing は OpenAI の coming-soon self-serve publishing surface により依然としてブロックされています。
- ECC Tools PR #73 は native GitHub payments claims 向けの fail-closed `/api/billing/readiness` `announcementGate` を追加し、ECC Tools PR #74 は operator verifier として `npm run billing:announcement-gate` を追加しましたが、public payment announcement の前に、live Marketplace-managed test-account readback が `announcementGate.ready === true` を返す必要があります。
- release notes、X、LinkedIn、longform copy は、release/package/plugin URL が存在した後、final live URL が依然として必要です。

## 結果 (Result)

queue、discussion、Linear roadmap、supply-chain evidence は、5月13日の publication evidence より新しいです。これらは readiness を改善しますが、`publication-readiness.md` が要求する final clean-checkout publish pass を置き換えるものではありません。
