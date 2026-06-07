# ECC v2.0.0-rc.1 公開証跡 - 2026-05-18 (ECC v2.0.0-rc.1 Publication Evidence - 2026-05-18)

これはリリース準備の証跡のみです。GitHub リリース、npm 公開、plugin tag、marketplace 提出、またはアナウンス投稿は作成しません。

## ソースコミット (Source Commit)

| 項目 (Field) | エビデンス (Evidence) |
| --- | --- |
| Upstream main | `4470e2e6702f17099d6feb137ba03ff00582c202` |
| Git remote | `https://github.com/affaan-m/everything-claude-code.git` |
| Evidence scope | PR #1970 workflow-security validator bypass 修正、PR #1971 metrics bridge cost-reporting 修正、PR #1972 `uncloud` skill マージ、PR #1973 stale script クリーンアップ、issue #1974 cost-reporting 検証/クローズ、PR #1976 OpenAI/AstraFlow provider response guards、PR #1978 review/クローズ、catalog/operator dashboard 更新、ECC-Tools Wrangler OAuth billing readback ミラー、AgentShield `840952a` fleet-ticket と Mini Shai-Hulud IOC 証跡ミラー、Mini Shai-Hulud/TanStack protection 再チェック、defensive-deny IOC scanner 強化、release name/plugin publication checklist、readiness/smoke gate によるその checklist 強制、release OIDC publishing-scope 強化、workflow line-ending 正規化、current-head CI/security scan、work-items sync、Linear progress sync、ITO-46 publication-path dry-run 更新、ITO-46 Linear クローズ、クローズ後 operator dashboard 更新後の現在の `main` |
| Local status caveat | dashboard 生成時点で `git status --short --branch` はクリーン; 生成された証跡ファイルは記述するソーススナップショット後にコミットされる |

実際のリリースオペレーターは、公開前に最終リリースコミットからすべての公開向けチェックを、厳密にクリーンな checkout で繰り返す必要があります。

## キューとディスカッション状態 (Queue And Discussion State)

| サーフェス (Surface) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| Trunk PRs | `gh pr list --limit 100 --json number,title,state,author,updatedAt,url` | open PR 0 件 |
| Trunk issues | `gh issue list --limit 100 --json number,title,state,updatedAt,url,labels` | open issue 0 件 |
| Discussion audit | `npm run discussion:audit -- --json` | Ready; `affaan-m/everything-claude-code` で 58 discussions をサンプル、maintainer touch 必要 0 件、accepted 回答欠落の回答可能 discussion 0 件、fetch error 0 件 |
| Platform audit | `node scripts/platform-audit.js --json --allow-untracked docs/drafts/` | Ready; 追跡 repo は open PR 0 件、open issue 0 件、discussion maintainer-touch gap 0 件、accepted 回答欠落の回答可能 Q&A 0 件、blocking dirty file 0 件を報告 |
| Work-items sync | 5 追跡 repo 向け `node scripts/work-items.js sync-github --repo <tracked-repo>`; `node scripts/status.js --json`; `node scripts/work-items.js list --json` | 5 追跡 repo すべて同期、open PR/issue 0 件、changed work item なし; local status は open 0、blocked 0、closed work item 0 を報告 |
| Operator dashboard | `npm run operator:dashboard -- --markdown --write docs/releases/2.0.0-rc.1/operator-readiness-dashboard-2026-05-18.md` | `4470e2e6702f17099d6feb137ba03ff00582c202` で再生成; dashboard ready true、release/npm/plugin/billing/announcement gate が approval-gated のため publication ready false; 追跡 repo 全体で PR 0、issue 0、discussion gap 0; AgentShield enterprise evidence に `840952a` を含む; ECC Tools native-payments gate は絞り込まれた ITO-61 blocker を明示: webhook provenance 付き Marketplace-managed Pro target billing-state を作成または検証し、target account と `INTERNAL_API_SECRET` を設定したうえで target readback と live announcement gate を再実行 |

platform audit と work-items sync で追跡されたリポジトリ:

- `affaan-m/everything-claude-code`
- `affaan-m/agentshield`
- `affaan-m/JARVIS`
- `ECC-Tools/ECC-Tools`
- `ECC-Tools/ECC-website`

## マージとトリアージバッチ (Merge And Triage Batch)

| 項目 (Item) | 結果 (Result) |
| --- | --- |
| PR #1970 | quoted `write-all` と `refs/pull/*` checkout bypass 向け workflow-security validator 修正をマージ; main にはそのスライスから `e06d0382` と `7bb31720` を含む |
| PR #1971 | metrics bridge cost-reporting 修正、full costs-file scan 動作、hook subprocess 間の persistent warning 重複排除をマージ; main には `9b1d8918` までのコミットを含む |
| PR #1972 | activation structure と uncloud command 参照付き `skills/uncloud/SKILL.md` をマージ; main には `8b6aed0`、`2e5f30f`、`caee7cf` を含む |
| PR #1973 | active hook が `scripts/hooks/suggest-compact.js` であることを確認後、stale `skills/strategic-compact/suggest-compact.sh` 削除をマージ; remote main には `812d4d06` を含む |
| Issue #1974 | 現在の `origin/main` が最新の累積 metrics bridge cost row を既に読み取り、focused cost/metrics テストがパスすることを確認後にクローズ |
| Catalog/operator refresh | #1973/#1974 後に生成 catalog 件数、URL ledger、operator dashboard 状態を更新する `81fca2ce` を push |
| PR #1976 | OpenAI-compatible と AstraFlow provider 向け provider response 強化をマージ; main には empty/filtered provider choices、欠落 OpenAI `response.usage`、共有 filtered-response error text、credential-less provider construction 検証向け `eb0d8939` フォローアップ guards を含む |
| Provider guard validation | `uv run --extra dev pytest -q tests/test_provider_tools.py tests/test_astraflow_provider.py`、`uv run --extra dev pytest -q`、`node tests/run-all.js`、`git diff --check` が #1976 フォローアップを main にマージする前にパス: 11 provider-focused Python tests、76 full Python tests、2509 Node tests、クリーンな whitespace チェック |
| Defensive-deny IOC scanner hardening | `04d4d819` を push し、明示的な Claude `permissions.deny` IOC エントリを defensive controls として扱い、同じ IOC が hooks、tasks、scripts、locks、payload files にある場合は引き続き fail; ローカル `npm test` は 2511/2511 パス、current-head CI `26017368895` は 37/37 パス |
| Release name/plugin publication checklist | `6c0fbfb6` を push し、`docs/releases/2.0.0-rc.1/release-name-plugin-publication-checklist-2026-05-18.md` を追加; artifact は rc.1 を Everything Claude Code / ECC として固定し、npm `ecc-universal`、Claude/Codex plugin slug `ecc` を維持し、現在の Anthropic/OpenAI plugin publication paths を引用し、最終リリース証跡が存在するまで rename/npm publish/plugin tag/submission/billing/social actions をブロック; GitHub Actions CI `26034898420` がパス |
| Dashboard and preview-pack checklist enforcement | `680aeff0` を追加し、`scripts/operator-readiness-dashboard.js` と `scripts/preview-pack-smoke.js` が release-name/plugin publication checklist を要求; ローカル dashboard と smoke テストがパスし、preview-pack smoke は 26 required artifacts を強制 |
| AgentShield enterprise evidence mirror | `2ba0c62d` を追加し、dashboard generator/GA roadmap/AgentShield enterprise roadmap を更新し、ECC リリース証跡が AgentShield `840952a` fleet review ticket payloads と現在の Mini Shai-Hulud IOC breadcrumb カバレッジを名指し |
| PR #1978 | レビュー後、広範/失敗した outside Excel harness PR をクローズ; 将来のより小さな Excel harness proposal、install-target/tooling PR、plugin-runtime PR、translation-automation PR 向けの修正済み split path を記録 |
| Announcement draft tracking | `docs/drafts/release-1.10.1-announcement.md` を追加し、stabilization announcement draft をリリースブロッキングな未追跡ローカル状態のまま残さず追跡 |
| Clean-worktree preview-pack smoke | `680aeff0fb9a8598858e3105ba4742973ef386ab` で detached worktree; `node scripts/preview-pack-smoke.js --root <worktree> --format json` は digest `0ed831dbd0cf` で 5/5 パス; 26 required artifacts、final verification commands、Hermes public sanitization boundary、approval-gated publication blockers はすべて保持 |
| Public queues | マージと issue クローズバッチ後に再チェック; tracked repos 全体で 0 PRs、0 issues、0 discussion gaps |
| Release OIDC publishing scope | `7911af4a` を push し、release workflow の trusted-publishing path を unrelated jobs 全体への OIDC permissions 拡大ではなく release publication にスコープ; ローカル workflow security 検証がパス |
| Release workflow normalization | `97567a91` を push し、OIDC hardening スライス後に release workflow line endings を正規化; current-head CI `26050727969` は `97567a91e79e1ee4c291eb78f5f9c30c2046ac94` でパス |
| Operator readiness evidence refresh | `0f1775e3`、`fe7b4f2b`、`67e63e63` を push し、blocker 証跡を更新、operator dashboard を再生成、publication readiness を最新 CI/security 証跡に整合; `4470e2e6` を push して ITO-46 publication-path 証跡をクローズし、`4470e2e6702f17099d6feb137ba03ff00582c202` で dashboard を再生成; current-head CI `26057806361` は `4470e2e6702f17099d6feb137ba03ff00582c202` でパス |

## サプライチェーンとセキュリティ証跡 (Supply-Chain And Security Evidence)

| ゲート (Gate) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| Repo IOC scan | `npm run security:ioc-scan` | 合格; 198 files を検査 |
| Home persistence IOC scan | `node scripts/ci/scan-supply-chain-iocs.js --home --json` | 合格; 200 files を検査; `findings: []` |
| ECC workspace IOC recheck | `node scripts/ci/scan-supply-chain-iocs.js --root <local ECC root> --home --json` | 合格; 1,212 files を検査; `findings: []`; 正確な local path は public release evidence から除外 |
| Narrow active persistence sweep | user-level Claude、VS Code、LaunchAgent/systemd、local-bin、`/tmp`、`/private/tmp` campaign paths 向け targeted search | 既存 active targets: 2; campaign marker hits なし |
| Scanner fixture tests | `node tests/ci/scan-supply-chain-iocs.test.js` | 20 合格、0 失敗（defensive Claude deny-wall pass と hook-with-same-IOC fail-closed カバレッジを含む） |
| Advisory source refresh | `node scripts/ci/supply-chain-advisory-sources.js --refresh --json` | Ready、sources 9; live refresh は Node fetch から OpenAI URL warning 1 件（TanStack、GitHub advisory、StepSecurity、Wiz、Socket、npm、CISA sources は OK） |
| No-lifecycle install | `npm ci --ignore-scripts` | クリーン完了; 213 packages installed、脆弱性 0 |
| npm audit | `npm audit --audit-level=high` | 脆弱性 0 |
| npm signatures | `npm audit signatures` | 213 verified registry signatures と 17 verified attestations |
| Workflow security | `node scripts/ci/validate-workflow-security.js` | release OIDC publishing-scope hardening 後、8 workflow files を検証 |
| AgentShield project scan | `npx --no-install ecc-agentshield scan --format json` | Grade A / 99; critical 0、high 0、medium 0; low docs-example skill telemetry/governance findings 6 件 |
| Current-head CI security scan | `gh run view 26057806361 --repo affaan-m/everything-claude-code --json status,conclusion,headSha,jobs,url` | `4470e2e6702f17099d6feb137ba03ff00582c202` で正常完了; CI jobs 37/37 合格（lint、workflow/component validation、coverage、cross-platform package-manager tests、npm audit、supply-chain IOC scan を含む） |
| Latest Supply-Chain Watch | `gh run view 26010432490 --repo affaan-m/everything-claude-code --json status,conclusion,headSha,url` | `25ac57ac40e9fc5a0606e76e6339e72c79748c99` で正常完了; 公開前に final release commit から再実行 |

## ITO-46 公開パス更新 (ITO-46 Publication Path Refresh)

| ゲート (Gate) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| Clean publication-path baseline | `git status --short --branch`; `git rev-parse HEAD`; `git remote get-url origin` | `67e63e63f9bfd074bd6a21bf6bac71f3dfefa58b` で clean `main`; remote `https://github.com/affaan-m/everything-claude-code.git` |
| Package/plugin identity readback | `node -p "JSON.stringify({pkg, claude, codex, opencode}, null, 2)"` | `ecc-universal@2.0.0-rc.1`; Claude plugin `ecc@2.0.0-rc.1`; Codex plugin `ecc@2.0.0-rc.1`; OpenCode package `ecc-universal@2.0.0-rc.1` |
| Name availability | `npm view ecc name version description repository.url --json`; `npm view @affaan-m/ecc name version --json`; `npm view ecc-universal name version dist-tags --json` | `ecc` は無関係な `ecc@0.0.2` に占有; `@affaan-m/ecc` は 404; `ecc-universal` registry latest は `1.10.0` のまま、`next` dist-tag なし |
| Plugin manifest tests | `node tests/plugin-manifest.test.js` | 54 合格、0 失敗 |
| Release surface tests | `node tests/docs/ecc2-release-surface.test.js` | 21 合格、0 失敗 |
| Claude plugin validation | `claude plugin validate .claude-plugin/plugin.json`; `claude plugin validate .`; `claude plugin tag .claude-plugin --dry-run` | Claude Code `2.1.143`; manifest validation 合格; full plugin validation 合格（想定内 root `CLAUDE.md` context warning 1 件）; tag dry run は `ecc--v2.0.0-rc.1` を作成 |
| Claude marketplace source help | `claude plugin marketplace add --help`; `claude plugin marketplace update --help` | Marketplace add は URL、local path、GitHub repo、`--scope`、`--sparse` をサポート; update は targeted または all-marketplace refresh をサポート |
| Codex marketplace help | `codex plugin marketplace add --help` | Codex CLI `0.131.0`; marketplace add は local paths、`owner/repo[@ref]`、HTTPS Git URL、SSH Git URL、`--ref`、`--sparse` をサポート |
| Codex local marketplace smoke | `HOME="$(mktemp -d)" codex plugin marketplace add ./` | 実 Codex config に触れず local checkout から marketplace `ecc` を追加 |
| Codex GitHub-ref marketplace smoke | `HOME="$(mktemp -d)" codex plugin marketplace add affaan-m/everything-claude-code --ref "$(git rev-parse HEAD)"` | 実 Codex config に触れず、`67e63e63f9bfd074bd6a21bf6bac71f3dfefa58b` に pin した公開 GitHub repo から marketplace `ecc` を追加 |
| npm package dry-run | `NPM_CONFIG_USERCONFIG=/dev/null npm pack --dry-run --json`; `NPM_CONFIG_USERCONFIG=/dev/null npm publish --tag next --dry-run` | Pack は `ecc-universal-2.0.0-rc.1.tgz`、2,228 files、4,348,504 bytes packed、13,024,929 bytes unpacked、shasum `29d6a17029d80f5cb1df068880ba86c55a5d60f1` を生成; publish dry-run は tag `next` で `ecc-universal@2.0.0-rc.1` を公開 |
| OpenCode package build | `npm run build:opencode` | 合格 |
| Preview pack smoke | `npm run preview-pack:smoke` | ready yes; digest `0ed831dbd0cf`; 5 合格、0 失敗 |
| Official docs check | Anthropic `https://code.claude.com/docs/en/plugins` と `https://code.claude.com/docs/en/plugin-marketplaces`; OpenAI `https://developers.openai.com/codex/plugins/build` | Anthropic は self-hosted marketplace sources を文書化; OpenAI は repo/personal marketplaces と official Plugin Directory を文書化。ECC はこの pass で実 release tag、official listing、npm publication を作成していない |
| ITO-46 closure | Linear ITO-46 comment `9ef92056-ab23-4eed-bfdb-932dddc2b056`; Linear issue status `Done`; GitHub Actions `26057806361` | Publication-path docs が全チャネル、name conflicts、package/plugin dry-run commands、blocker register を記録; Codex repo-marketplace 配布は検証済みだが、OpenAI submission/listing エビデンス前に official Plugin Directory listing は主張しない |

## Linear 進捗同期 (Linear Progress Sync)

| サーフェス (Surface) | エビデンス (Evidence) |
| --- | --- |
| ITO-57 issue comments | `0b9931b9-1556-4ebc-a70c-f3635557625d` は 5月18日 queue counts、#1970/#1971/#1972/#1976 merge evidence、supply-chain verification、current-head CI URL、deferred gates、next slices を記録; reply `6fa15367-d994-4e53-ade3-9462477e1100` は拡張 TanStack/Mini Shai-Hulud recheck、defensive-deny scanner fix、current-head CI `26017368895`、post-push platform audit を記録; comment `3fe5b2b7-c4fe-401c-a317-b40d72119cb3` は `97567a91` 向け final emergency refresh、AgentShield `4e36aab`、clean ECC/Ito/Documents workspace IOC scans、dead-man/persistence artifact 不在、package-manager/Claude deny-wall posture を記録; comment `43837404-c01c-4aaa-b5e2-1e784c136d69` は ECC-Tools `brace-expansion` alert 44 が `e56fc1a` で修正、CI `26054671308`、Dependabot API `state: fixed` を記録 |
| ITO-52 issue status | `f2e5a208-de91-4a3a-960b-5362d12aa5a4` は ECC-Tools `69ca535` team-learning feedback controls、local verification、CI `26054455434` を記録; Linear ITO-52 は Done |
| ITO-61 issue status | `6904e4fb-bec7-4787-90e2-759f077a628c` は絞り込まれた native-payments readback blocker を記録: Wrangler OAuth は動作、aggregate readback は clean だが、webhook provenance 付き Marketplace-managed Pro target billing-state がまだなく、local announcement preflight に target account と `INTERNAL_API_SECRET` が欠けている |
| ECC platform project comment | `e32e5b7a-287b-4bf4-9ed7-314389a157e1` は project レベルで以前の current public queue、security、#1976、remaining-gate 状態を記録; フォローアップ ITO-44 comments `a01eeef3-c69b-48c0-8804-a4682acfc1ef` と `6b0885cc-c4e9-40db-899b-f7b88b4aa046` は ITO-52 完了と修正済み ECC-Tools Dependabot alert を記録 |
| Project status update caveat | Linear は "Project status updates are not enabled for this workspace" を返却; サポートされる status surface として project comment を使用 |

## 現在の公開ブロッカー (Current Publication Blockers)

- このパスでは GitHub prerelease `v2.0.0-rc.1` はまだ作成されていない。
- npm `ecc-universal@2.0.0-rc.1` はまだ `next`
  dist-tag に公開されていない。
- Claude plugin tag と marketplace 伝播は approval-gated のまま。
- Codex repo-marketplace 配布は rc.1 で検証済みだが、公式
  Plugin Directory 公開は OpenAI の self-serve publishing
  surface によりブロックされている。
- ECC Tools billing/native-payments コピーは、Marketplace
  Pro purchase/webhook パスが target Marketplace test account 向けに ready な production `billing-state:*`
  provenance を書き込み、その後
  `npm run billing:kv-readback -- --account <github-login> --require-ready`
  が動作する Cloudflare API auth または修復済み Wrangler OAuth で実行され、続けて
  `npm run billing:announcement-gate -- --account <github-login>` が
  announcement-ready gates を返すまでブロックされている。最新の Wrangler OAuth aggregate readback では
  256 `account-billing:*` records、256 `billing-state:*` records、197
  Marketplace-source records、59 Stripe-source records、53 Pro records、4
  Marketplace webhook-provenance records、すべて `Open Source`、0 Marketplace Pro
  states、0 ready-like Marketplace Pro states、0 parse failures を検出。ECC-Tools
  commit `632e059` はフォローアップ target-account readback mode を追加し、
  account login と raw KV key 名を redact し、`--require-ready` がパスするには両方の target key families が必要。ECC-Tools commit `13cd3fc` は
  billing-state key casing を正規化。最新の ITO-61 リトライは Marketplace-managed Pro state が存在せず、announcement preflight に
  target account と `INTERNAL_API_SECRET` が欠けているため失敗; Linear ITO-61 が
  正確な target-account acceptance criteria を追跡。
- リリースノート、X、LinkedIn、GitHub release、longform コピーは、リリース/package/plugin URL が存在した後の最終ライブ URL がまだ必要。
- dashboard/証跡更新後のローカル checkout はクリーンだが、実際の公開前には厳密なクリーン checkout によるリリースパスが依然必要。

## 結果 (Result)

追跡された公開 PR キュー、issue キュー、discussion キュー、ローカル work-items
bridge、release-name/plugin publication gate、Mini Shai-Hulud/TanStack
protection loop は、2026年5月18日時点で `97567a91` までの current `main` について最新であり、フォローアップ ECC Tools billing-gate 強化は `632e059`、AgentShield enterprise/security 強化は `4e36aab` まで含む。
これは公開準備を改善するが、`publication-readiness.md` にある approval-gated なリリース、package、plugin、billing、アナウンス手順の代替にはならない。
