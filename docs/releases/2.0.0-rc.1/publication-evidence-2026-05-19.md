# ECC v2.0.0-rc.1 公開エビデンス (Publication Evidence) - 2026-05-19

これはリリース準備エビデンスのみです。GitHub リリース、npm 公開、plugin tag、marketplace 提出、billing 告知、ソーシャル告知は作成しません。

## ソースコミット (Source Commit)

| 項目 (Field) | エビデンス (Evidence) |
| --- | --- |
| Upstream main | `c2471fe5c535310f8a8008c9ed7ea9f6757b33f2` |
| Git remote | `https://github.com/affaan-m/ECC.git` |
| Evidence scope | PR #1990 harness-audit GitHub integration scoring、PR #1991 正規 ECC identity gate、PR #1992 release video-suite gate、PR #1993 growth outreach pack、PR #1994 5月19日 publication evidence refresh、PR #1995 operator dashboard refresh、PR #1996 primary render self-eval gate、PR #1997 publish-candidate gate、PR #1998 visual QA gate、PR #1999 video dashboard evidence refresh、PR #2000 suite-count evidence refresh、PR #2001 owner approval packet addition、PR #2002 owner approval dashboard gate refresh、PR #2004 Linear readiness evidence sync、PR #2005 post-PR #2004 evidence refresh、PR #2008 release supply-chain evidence gate fix、PR #2006 per-project Claude Code adapter、PR #2009 continuous-learning project registry hygiene fix、PR #2011 GateGuard quoted git introspection fix、PR #2013 deterministic release approval gate、PR #2017 AgentShield adapter evidence sync、PR #2018 AgentShield Dependabot evidence sync、ECC-Tools #80-#91 hosted observability/readback、Marketplace Pro selected-target、selected-target announcement gate、env-file operator-path batch、AgentShield #94 Zed/VS Code adapter coverage、AgentShield #95 Dependabot alert closure、PR #2019 Marketplace Pro release-gate sync、PR #2020 selected-target announcement gate sync 後の現在の `main` |
| Local status caveat | `origin/main` を pull 後 `git status --short --branch` はクリーン; 生成された evidence files は記述するソーススナップショット後にコミットされる |

リリースオペレーターは、公開前に厳密にクリーンな checkout から、最終リリースコミットの正確な状態で、すべての公開向けチェックを繰り返す必要があります。

## キューおよびディスカッション状態 (Queue And Discussion State)

| サーフェス (Surface) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| Platform audit | `node scripts/platform-audit.js --json` | ready true; 追跡 repo 全体で open PR 0、open issue 0、discussion maintainer-touch gaps 0、answerable Q&A gaps 0、conflicting PRs 0、blocking dirty files 0 |
| Trunk PRs | `gh pr list --repo affaan-m/ECC --state open --json number,title,url,author --limit 100` | `[]` |
| Trunk issues | `gh issue list --repo affaan-m/ECC --state open --json number,title,url,author --limit 100` | `[]` |
| Discussion audit through platform audit | `node scripts/platform-audit.js --json` | `affaan-m/ECC` discussions 有効; #2015 setup-location Q&A 回答・accepted 後 60 件をサンプル; maintainer touch 必要 0; accepted 回答なしの回答可能 discussion 0 |
| Worktree | `git status --short --branch` | `## main...origin/main` |

platform audit で追跡されたリポジトリ:

- `affaan-m/ECC`
- `affaan-m/agentshield`
- `affaan-m/JARVIS`
- `ECC-Tools/ECC-Tools`
- `ECC-Tools/ECC-website`

## マージバッチ (Merge Batch)

| 項目 (Item) | 結果 (Result) |
| --- | --- |
| PR #1990 | 以前の unsafe PR レーンから GitHub integration harness-audit scoring と conflict salvage をマージ |
| PR #1991 | README、plugin/package metadata、OpenCode surfaces、Marketplace metadata、audit defaults、quickstart、release URL ledger、naming/publication matrix、release tests 全体に正規 ECC release identity gate をマージ |
| PR #1992 | release video-suite gate、production manifest、validator、package file surface、preview-pack smoke wiring、release-surface tests、compact CI JSON output をマージ |
| PR #1993 | hypergrowth outbound レーン向け partner、sponsor、consulting、conference、podcast、GitHub Discussion、video CTA pack をマージ |
| PR #1994 | 5月19日 publication-evidence refresh、platform-audit evidence gate、preview-pack smoke evidence gate、URL/readiness/roadmap 参照をマージ |
| PR #1995 | `$1,728/mo` MRR baseline、`$10,000/mo` target、release/video/outbound top actions 付き 5月19日 operator dashboard refresh をマージ |
| PR #1996 | duration、size、resolution、video stream、audio stream チェック向け primary launch render self-eval gate をマージ |
| PR #1997 | primary launch MP4/captions と wide/vertical 形式 5 本の short clip 向け publish-candidate gate をマージ |
| PR #1998 | publish candidate と black-frame segment 検出向け release video visual QA gate をマージ |
| PR #1999 | publish-candidate evidence 記録後に release video suite を current に移した operator dashboard refresh をマージ |
| PR #2000 | platform audit が stale local-suite totals を拒否する suite-count evidence refresh をマージ |
| PR #2001 | release、package、plugin、video、billing、social、outbound 承認向け最終人間判断シートをマージ; GitHub Actions run `26102500291` が成功完了 |
| PR #2002 | 最終 decision sheet が欠落または不完全な場合に operator dashboard が fail closed する owner-approval dashboard refresh をマージ; マージ前 CI 合格 |
| PR #2004 | PR #2002 後の 5月19日 Linear readiness evidence sync をマージ（roadmap、dashboard、preview-pack manifest、publication evidence、operator dashboard generator、release-surface test 更新を含む） |
| PR #2005 | post-PR #2004 evidence refresh をマージし、`main` 上の 5月19日 readiness ledger、dashboard、roadmap、release-surface 参照を最新維持 |
| PR #2008 | platform-audit readiness が current publication evidence と一致し続ける release supply-chain evidence gate fix をマージ |
| PR #2006 | per-project Claude Code adapter サポート向け `claude-project` install target をマージ; マージ前に feature branch 上で manifest schema enum を修正 |
| PR #2009 | continuous-learning project registry hygiene fix をマージ: non-git hook payloads は global のまま、no-remote linked worktrees は main worktree project ID に移行、`instinct-cli.py projects delete`、`merge`、`gc` で operator maintenance commands を提供 |
| PR #2011 | スペース付き quoted `git show` pathspecs を保持し quoted shell separators を bypass 外に保つ GateGuard read-only git introspection tokenizer fix をマージ |
| PR #2013 | owner decisions と live URL readbacks が完了するまで final publication、package、plugin、video、billing、social、outbound actions をブロックする deterministic `release:approval-gate` をマージ |
| PR #2017 | AgentShield #94 evidence mirror を `906e06406e95742944ccb05065f95a7e4dd4a036` としてマージ; full GitHub CI 後に roadmap、publication evidence、preview-pack manifest、supply-chain incident-response surfaces を同期 |
| PR #2018 | AgentShield #95 Dependabot evidence mirror を `68b4e45145968acd52e68d900f8422061ed7f4a2` としてマージ; full PR CI 後に roadmap、publication evidence、preview-pack manifest を同期 |
| PR #2019 | Marketplace Pro selected-target release-gate sync を `30f60710d4e0424fc70d9bbdc105009db141d9d8` としてマージ; full PR CI 後に roadmap、publication evidence、naming matrix、preview manifest、operator dashboard を更新 |
| PR #2020 | selected-target announcement gate sync を `c2471fe5c535310f8a8008c9ed7ea9f6757b33f2` としてマージ; full PR CI 後に roadmap、publication evidence、naming matrix、preview manifest、release URL ledger、platform audit surfaces、operator dashboard を更新 |

## キューゼロ後同期 (Post-Queue-Zero Sync) - 2026-05-19 Late Pass

| サーフェス (Surface) | エビデンス (Evidence) |
| --- | --- |
| ECC approval gate | PR #2013 を `9819626459a662773be7d0b1c18d82c1316b8c36` としてマージ; GitHub Actions run `26128749863` が正常完了; `npm run release:approval-gate -- --format json` は digest `ef8f49f727b7`、4/6 合格、owner decisions と live URL readbacks のみ失敗で意図的に blocked のまま |
| ECC platform audit | `2026-05-19T22:45:15Z` の `node scripts/platform-audit.js --json` は ready true、`affaan-m/ECC`、`affaan-m/agentshield`、`affaan-m/JARVIS`、`ECC-Tools/ECC-Tools`、`ECC-Tools/ECC-website` 全体で open PR 0、open issue 0、discussion maintainer-touch gaps 0、answerable Q&A gaps 0、dirty blockers 0 を返却 |
| ECC-Tools billing hardening | ECC-Tools PR #79 を `67ee247ae1b7b50ecc1261ed5d62d65cc8390da8` としてマージ; preflight と live billing-announcement 出力は account login を安定 fingerprint に redact し readiness blockers/actions を保持; ローカル検証は targeted tests、full test suite 678/678、lint、typecheck、manual preflight、`git diff --check` を合格; post-merge main CI run `26129253509` が正常完了 |
| JARVIS queue drain | JARVIS PR #15 は Dependabot `idna` 3.11→3.15 security bump を `4b3685d6ee23b4da1f1a7d22281c6b5d6c0a42c7` としてマージ; PR checks と post-merge CI/CodeQL が合格 |
| JARVIS deploy repair | JARVIS PR #16 を `4369c34babd21d539c420866da51c7a8365f1c9e` としてマージ; deploy workflow は無効な job-level `secrets.*` condition を使用せず、secret 不在時 Vercel deploy は clean skip、backend image build/push 成功; main CI、CodeQL、Deploy runs `26129539376`、`26129539427`、`26129539425` が正常完了 |
| Linear roadmap sync | Linear document `ecc-may-19-late-queue-zero-and-release-gate-sync-1c26f65e6b3f`、project comment `d42bf0e2-7a8e-4934-9f3f-e281498ee805`、ITO-44/ITO-50/ITO-54/ITO-56/ITO-61 issue comments が late-pass queue-zero、release-gate、billing-safety、progress-sync 状態を記録 |

## 5月20日ホステッドオブザーバビリティおよび AgentShield アダプター同期 (May 20 Hosted Observability And AgentShield Adapter Sync)

| サーフェス (Surface) | エビデンス (Evidence) |
| --- | --- |
| ECC discussion queue | Discussion #2015 に保守的 setup ガイダンスで回答し accepted マーク: `C:\` にはインストールしない; 通常 workspace を使用; Claude plugin marketplace 経由で `ecc@ecc` を 1 回だけインストール; manual rules 使用時は必要な rule フォルダのみコピー; plugin と full manual install を重ねない |
| ECC platform audit | `2026-05-20T00:25:38Z` の `node scripts/platform-audit.js --json` は ready true、open PR 0、open issue 0、discussion maintainer-touch gaps 0、answerable Q&A gaps 0、conflicting PRs 0、dirty blockers 0 を返却（`affaan-m/ECC`、`affaan-m/agentshield`、`affaan-m/JARVIS`、`ECC-Tools/ECC-Tools`、`ECC-Tools/ECC-website`） |
| ECC platform audit recheck | `2026-05-20T00:42:11Z` の `npm run platform:audit -- --json` は AgentShield #94 マージ後、同一追跡 repo セット全体で ready true、open PR 0、open issue 0、discussion maintainer-touch gaps 0、answerable Q&A gaps 0、conflicting PRs 0、GitHub errors 0、dirty blockers 0 を返却 |
| ECC-Tools #80/#81/#82 | PR #80 は runtime-receipt failure-reason enforcement を `4efc8cc858022f84c844690f3298633b081c4398` としてマージ; PR #81 は AgentShield fleet approval IDs を `1fbf635f492284f75ba7166c029c39eb8cc15794` として保持; PR #82 は hosted security review comments/check-runs に approval IDs を `7a7b4d096a176ae80b3a2076c09d45601e36013a` として表示 |
| ECC-Tools #83/#84 | PR #83 は deferred follow-ups 向け deterministic Linear external-ID reuse を `b6b107f33961bef18a85fb619f3a976eb5d752dd` としてマージ; PR #84 は hosted AgentShield remediation sync to Linear を `73bac7058071c55cb30c6b8ac6db779b3660c02c` としてマージ。マージ前ローカル検証は focused route/client tests、typecheck、lint、full ECC-Tools test suite、whitespace checks をカバー; GitHub Verify、Security Audit、Workers Builds が合格 |
| ECC-Tools #85/#86/#87 | PR #85 は hosted job observability events を `1637e0f2bfa0a889387f2c20675680ccc5528123` としてマージ; PR #86 は hosted status observability readback を `5a9e94d3ff860307c3e7fd9fd065f0de2bd633dd` としてマージ; PR #87 は hosted depth-plan observability readback を `508fbc02b63cf1fcb5af2f3624608fa66e53b5d4` としてマージ。最終 depth-plan readback slice のローカル検証は focused hosted depth-plan route test、full route suite (89/89)、typecheck、lint、full ECC-Tools Vitest suite (683/683)、`git diff --check` を合格; マージ前 GitHub Verify、Security Audit、Workers Builds が合格 |
| ECC-Tools #88 | PR #88 は authenticated hosted observability API readback を `c836ac3fb24ed7e2ae38cd61e41c9651ac9c00f8` としてマージ。`GET /api/analysis/observability` は operator/dashboard readback 向けに event type と job 別 hosted events を集約し、malformed stale KV records をスキップ; deployment runbook に production smoke command を含む。ローカル検証は typecheck、lint、full ECC-Tools Vitest suite (686/686)、`git diff --check` を合格; マージ前 GitHub Verify、Security Audit、Workers Builds が合格 |
| AgentShield #94 | PR #94 は Zed/VS Code adapter coverage を `4caee27acfadb50a4cd024e738b5c3cbd4b0bb03` としてマージ。AgentShield は Zed と VS Code を first-class harness adapters として報告し、`.zed/settings.json`、`.zed/tasks.json`、`.zed` hook-code files を発見し、`.vscode/setup.mjs` と並んで AI-tool persistence IOC rule で `.zed/setup.mjs` をフラグ。ローカル検証は typecheck、lint、focused scanner/rule tests、full `npm test` (1822 tests)、`npm run build`、`git diff --check` を合格; 一時的 artifact-upload 失敗再実行後 GitGuardian、scan suite、self-scan、self-scan examples、Node 18/20/22 CI、CodeRabbit、Cubic が合格 |
| AgentShield #95 | PR #95 は `brace-expansion` Dependabot fix を `25d91f0002214c408da4ceaac7def20bad40ca10` としてマージ。lockfile は vulnerable transitive `brace-expansion` 5.x entries を `5.0.6` に解決; ローカル `npm audit --audit-level=moderate` は脆弱性 0; `gh api repos/affaan-m/agentshield/dependabot/alerts?state=open` は `[]` を返却。ローカル検証は typecheck、lint、full `npm test` (1822 tests)、build、audit、whitespace checks を合格; Verify Node 18/20/22、self-scan、self-scan examples、Test GitHub Action、GitGuardian、CodeRabbit、Cubic が合格 |
| Linear roadmap sync | Linear ITO-54 comment `74dcc101-3be5-4173-be13-62b80d54f569` と ECC Platform Roadmap project comment `348ea8f5-2a2d-46d9-a0fe-ed99653e7fe5` は 5月20日 hosted observability status/depth-plan readback batch を記録; Linear comments `291e2a4b-06e3-4672-a057-cdb141478161` と `b2d35de0-ca49-44cb-982a-ddec229e7691` は #88 observability API readback を追加; Linear ITO-49 comment `faed69dd-35f5-469d-acb5-ddde6a70d6a1` と project comment `70187c1e-d481-4181-b418-09bd65d54b5e` は #94 AgentShield Zed/VS Code adapter エビデンスを追加; Linear ITO-49 comment `371fc3e4-611f-4d20-a23f-67db1260b418`、ITO-57 comment `bd06e252-15c1-4256-b667-caa3f64f5968`、project comment `22c2c388-2fd1-4dea-a939-6141f40c9a21` は #95 AgentShield Dependabot alert closure を追加; ITO-54、ITO-48、project 上の以前の comments は #84 hosted remediation sync と #85 hosted observability event emission batches を記録 |

## 5月20日 Marketplace Pro リリースゲート同期 (May 20 Marketplace Pro Release-Gate Sync)

| サーフェス (Surface) | エビデンス (Evidence) |
| --- | --- |
| ECC-Tools #89 | PR #89 を Verify、Security Audit、Workers Builds 合格後 `512bca6b99cdaa67058a6aa9a4e7e7f0b1d9873a` としてマージ。`billing:kv-readback -- --select-ready-target --require-ready` を追加し、login を渡したり表示したりせず内部で ready Marketplace Pro target を選択可能に |
| Live production readback | 2026-05-20 Wrangler OAuth readback は webhook provenance 付き ready-like Marketplace Pro records を検出し、both key families、seat/webhook readiness、overage なし、blockers 0 の target を選択（account details は redact）。旧 missing Marketplace Pro target-state blocker は解消 |
| ECC #2019 | PR #2019 を `30f60710d4e0424fc70d9bbdc105009db141d9d8` としてマージ; selected-target readback エビデンスを GA roadmap、rc.1 publication evidence、naming matrix、preview manifest、operator dashboard に同期 |
| ECC-Tools #90 | PR #90 を Verify、Security Audit、Workers Builds 合格後 `16a5bb33ee5ce7c31d2ad8d041e5afac03308f05` としてマージ。`/api/billing/readiness?selectReadyTarget=1` と `npm run billing:announcement-gate -- --select-ready-target` 経由で selected-target official announcement gate を追加し、raw account login を command logs 外に保持 |
| ECC #2020 | PR #2020 を `c2471fe5c535310f8a8008c9ed7ea9f6757b33f2` としてマージ; ECC-Tools #90 を roadmap、publication evidence、naming matrix、preview manifest、publication readiness、release URL ledger、platform audit surfaces、operator dashboard に同期 |
| ECC-Tools #91 | PR #91 を Verify、Security Audit、Workers Builds 合格後 `72119a1acc6f5a0cd3bb5d90afd6e87fd1fefd05` としてマージ。ignored local operator credential files 向けに billing announcement と KV readback scripts に `--env-file` を追加; tests は sentinel secrets と account logins が出力されないことを証明 |
| ECC-Tools #92 | PR #92 を Verify、Security Audit、Workers Builds 合格後 `18d80197be779619283e0b37e2952bac53819a07` としてマージ。primary `INTERNAL_API_SECRET` をローテーションせず privileged internal API routes が受け付ける non-breaking `INTERNAL_OPERATOR_API_SECRET` bearer を追加; マージ済み Worker を `api.ecc.tools` にデプロイ |
| May 20 live selected-target gate | Vault-backed Wrangler readback は Marketplace Pro state、target fingerprint `e953a74209fe`、both key families、webhook evidence、seat readiness、overage なし、blockers 0 で合格。operator bearer ローテーション後、`npm run billing:announcement-gate -- --preflight --select-ready-target` は ready、`npm run billing:announcement-gate -- --select-ready-target` は `announcementGateReady: true`、required actions 0、blockers 0、audit summary 6 pass / 1 warn / 0 fail を返却 |
| ECC-Tools #93 | PR #93 を `d3d62df83fa075660fa4530c3e0edc311a4355fe` としてマージ; launch checklist と distribution roadmap に live billing announcement gate pass を記録しつつ、最終 release/plugin/URL approval gates を保持 |
| Post-merge main CI | ECC GitHub Actions runs `26135974576`、`26136949698`、`26138015245` は `30f60710d4e0424fc70d9bbdc105009db141d9d8`、`c2471fe5c535310f8a8008c9ed7ea9f6757b33f2`、`6e25458dbc15cd07cfb7a4e1f0b06f3eda41a043` で `main` 上正常完了（lint、coverage、security、validation、full OS/package-manager matrix）。ECC-Tools main CI runs `26137280847`、`26138403065`、`26138669148` は `72119a1acc6f5a0cd3bb5d90afd6e87fd1fefd05`、`18d80197be779619283e0b37e2952bac53819a07`、`d3d62df83fa075660fa4530c3e0edc311a4355fe` で正常完了 |
| Post-merge local gates | `npm run platform:audit -- --json` は ready true、PR 0、issue 0、discussion gaps 0、dirty blockers 0; `npm run preview-pack:smoke -- --format json` は 5月20日 dashboard rollover 前 digest `531328aaaa53`、May 20 dashboard artifact 追加後 `eebb8a66c33e` で ready true; `git diff --check HEAD~1..HEAD` は clean |
| Linear roadmap sync | Linear ITO-61 comment `467d148a-712a-4777-aad9-95593e9f1739` と ECC Platform Roadmap project comment `7642ee9c-3107-400c-a229-53e2895a8914` は ECC-Tools #89、ECC #2019、green post-merge CI run、以前の internal bearer-token gate を記録; Linear ITO-44 comment `a9297467-208a-41e4-8dbb-35f0dad5fe2b`、ITO-56 comment `5008b70b-cf98-43cd-a8d4-f098ba9b9780`、ITO-61 comment `5ebf0aaf-e2d3-4537-878f-484f49dcf87a`、project reply `1c74a3d0-f8ca-4306-997e-a37c53d49f97` は ECC #2020 selected-target announcement-gate sync を記録; 新規 Linear sync は ECC-Tools #92/#93 と live gate pass を記録すべき |
| Remaining blocker | 2026-05-20 selected-target gate pass 時点で native-payments billing エビデンスは ready。起動直前に KV readback と `billing:announcement-gate -- --select-ready-target` を繰り返し、native-payments コピーは最終 release、plugin、live URL、owner-approval ゲートの背後に置く |

## リリースおよびグロースエビデンス (Release And Growth Evidence)

| ゲート (Gate) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| Release-surface tests | `node tests/docs/ecc2-release-surface.test.js` | 28 合格、0 失敗 |
| Preview-pack smoke | `npm run preview-pack:smoke -- --format json` | ready true; digest `eebb8a66c33e`; required artifacts 33; 5 合格、0 失敗 |
| Release approval gate | `npm run release:approval-gate -- --format json` | 意図的 blocked; digest `ef8f49f727b7`; 4 合格、2 失敗; owner decisions と live URL readbacks は approval-gated のまま |
| Operator dashboard | `npm run operator:dashboard -- --write docs/releases/2.0.0-rc.1/operator-readiness-dashboard-2026-05-20.md` | 5月20日 `main` baseline から再生成; platform audit ready true、追跡 PR 0、issue 0、discussion gaps 0、current MRR `$1,728/mo`、target MRR `$10,000/mo`、release video suite current、Linear release-gate sync current; plugin publication、notifications、outbound approval、AgentShield、ECC Tools billing が top actions |
| Supply-chain verification | `npm audit --audit-level=moderate`; `npm audit signatures`; `yarn install --immutable --mode=skip-build` | 現在の supply-chain refresh: npm 脆弱性 0、254 registry signatures と 30 attestations を検証; `@types/node@25.7.0` pin と `brace-expansion` `5.0.6`/`1.1.14` refresh 後 Yarn lock を受理 |
| Release video suite | `npm run release:video-suite -- --format json --summary`（`ECC_VIDEO_SOURCE_ROOT`、`ECC_VIDEO_RELEASE_SUITE_ROOT`） | ready true; source assets 15/15; render/timeline/caption/EDL/segment artifacts 13/13; publish-candidate outputs 12/12、black-frame segment 0; primary rough render self-eval 144.759 秒、1920x1080、audio stream 1、106.78 MB で合格 |
| Focused post-merge regression set | `node tests/hooks/detect-project-worktree.test.js`; `node tests/hooks/observe-subdirectory-detection.test.js`; `node tests/scripts/instinct-cli-projects.test.js`; `node tests/hooks/hooks.test.js` | PR #2009 マージ後に 10/10、6/6、5/5、237/237 合格 |
| GateGuard PR #2011 regression | `node tests/hooks/gateguard-fact-force.test.js`; `npm test`; `git diff --check main...HEAD` | PR branch で 91/91 合格; マージ前の full local suite は 2560/2560 合格; whitespace check 合格; 現在の `main` でも focused GateGuard suite が再合格 |
| Release approval gate PR #2013 validation | `npm test`; `npm run lint`; `git diff --check`; `npm run preview-pack:smoke -- --format json`; `npm run release:approval-gate -- --format json` | マージ前に 2568/2568 tests 合格; lint と whitespace 合格; preview pack は digest `531328aaaa53` で ready 維持; release approval gate は期待どおり digest `ef8f49f727b7` で blocked exit |
| Full local suite | `node tests/run-all.js` | PR #2013 マージ前に 2568 合格、0 失敗 |
| PR #1998 CI | GitHub Actions run `26099020341` | `d500de1e9f11c0446b6a1349bd98b522d31f9125` で正常完了; lint、validation、security scan、coverage、GitGuardian、CodeRabbit、Cubic、macOS/Ubuntu/Windows test matrix を含む報告チェックすべて合格 |
| PR #1999 CI | GitHub Actions run `26100148726` | `90584b6d5e5814bc2ad9a4cd651bebd043de989d` で正常完了; lint、validation、security scan、coverage、GitGuardian、CodeRabbit、macOS/Ubuntu/Windows test matrix 合格; Cubic は neutral 完了でマージをブロックしなかった |
| PR #2001 CI | GitHub Actions run `26102500291` | `8148340ad14eb32c971346f0cb4cb9431ec0f5de` で正常完了; マージ前に required checks 合格 |
| PR #2002 CI | GitHub Actions run `26103853507` | マージ前に正常完了; required checks 合格、Cubic は non-blocking のまま、PR #2002 は `main` に `c7d662c3c68719e5ef0b5305ca3f6782b3214224` としてマージ |
| PR #2004 CI | GitHub Actions run `26105012698` | 失敗した Windows Node 18 yarn job を再実行後に正常完了; required checks 合格、Cubic は non-blocking のまま、PR #2004 は `main` に `ac7434ea8f39166b11e9d06ce64b38c4fb8d9202` としてマージ |
| PR #2005 CI | GitHub Actions run `26106321921` | 37 completed jobs、0 failed jobs で正常完了、PR #2005 は `main` に `d6022d6b8dc5ef1393cf18ae40ee58f646f3754e` としてマージ |
| PR #2008 CI | GitHub Actions run `26108473648` | マージ前に required matrix 全体で正常完了; non-blocking Cubic はレビュー後 skip |
| Post-PR #2006 main CI | GitHub Actions run `26109953093` | 37 completed jobs、0 failed jobs で正常完了、`main` は `98bd517451f38fa0150a53aab4234c2239a47b7e` へ前進 |
| PR #2009 CI | GitHub Actions run `26111313938` | brittle な fake-worktree regression fixture を実際の `git worktree add` setup に置き換えた後、37 completed jobs、0 failed jobs で正常完了 |
| Post-PR #2009 main CI | GitHub Actions run `26111946778` | 37 completed jobs、0 failed jobs で正常完了、`main` は `bc519e5b8ed42f26c0a5a611756e04351c323f21` へ前進 |
| Post-PR #2011 main CI | GitHub Actions run `26113695068` | 37 completed jobs、0 failed jobs で正常完了、`main` は `14d88e517b0c56a80c1a6392b1cde2474948d29f` へ前進 |
| Post-PR #2013 main CI | GitHub Actions run `26128749863` | 正常完了、`main` は `9819626459a662773be7d0b1c18d82c1316b8c36` へ前進 |
| Post-PR #2019 main CI | GitHub Actions run `26135974576` | 正常完了、`main` は `30f60710d4e0424fc70d9bbdc105009db141d9d8` へ前進 |
| Post-PR #2020 main CI | GitHub Actions run `26136949698` | 正常完了、`main` は `c2471fe5c535310f8a8008c9ed7ea9f6757b33f2` へ前進 |
| ECC-Tools #91 main CI | GitHub Actions run `26137280847` | env-file billing gate support マージ後、`72119a1acc6f5a0cd3bb5d90afd6e87fd1fefd05` で ECC-Tools `main` 上正常完了 |
| ECC-Tools #92 main CI | GitHub Actions run `26138403065` | operator bearer path マージ後、`18d80197be779619283e0b37e2952bac53819a07` で ECC-Tools `main` 上正常完了 |
| ECC-Tools #93 main CI | GitHub Actions run `26138669148` | live billing announcement evidence マージ後、`d3d62df83fa075660fa4530c3e0edc311a4355fe` で ECC-Tools `main` 上正常完了 |
| Linear sync | Linear document `ecc-may-19-post-pr-2002-sync-64cef8f668e0` および project comment `a6411e3a-8c8e-4a58-adba-687e77d4c543`; late-pass document `ecc-may-19-late-queue-zero-and-release-gate-sync-1c26f65e6b3f` および project comment `d42bf0e2-7a8e-4934-9f3f-e281498ee805`; 5月20日 ITO-61 comment `467d148a-712a-4777-aad9-95593e9f1739` および project comment `7642ee9c-3107-400c-a229-53e2895a8914`; 5月20日 ITO-44 comment `a9297467-208a-41e4-8dbb-35f0dad5fe2b`、ITO-56 comment `5008b70b-cf98-43cd-a8d4-f098ba9b9780`、ITO-61 comment `5ebf0aaf-e2d3-4537-878f-484f49dcf87a`、project reply `1c74a3d0-f8ca-4306-997e-a37c53d49f97` | project と issue lanes は PR #2002 エビデンス、discussion #2003 routing、owner-approval dashboard gate、ITO-47/48/49/51/54/56 の In Progress 状態を記録; late-pass sync は PR #2013、ECC-Tools #79、JARVIS #15/#16 エビデンスを ITO-44/50/54/56/61 に紐付け; 5月20日 sync は ECC-Tools #89/#90、ECC #2019/#2020 の Marketplace Pro selected-target と selected-target announcement-gate エビデンス、残りの env-file/bearer-token gate を ITO-44/56/61 と project に紐付け |
| Public-path sanitization | ローカルスイートと CI 経由の `node scripts/ci/validate-no-personal-paths.js` | 合格 |
| Markdown and whitespace | PR #1999 前の focused release docs `markdownlint` と `git diff --check` | 合格 |

## プロダクトおよびポジショニングエビデンス (Product And Positioning Evidence)

| サーフェス (Surface) | エビデンス (Evidence) |
| --- | --- |
| 正規 repo identity (Canonical repo identity) | 公開 URL と release docs は必要な箇所で `https://github.com/affaan-m/ECC` を使用 |
| Release claim | release notes と launch collateral は ECC を agentic work 向け harness-native operator system として位置づけ、Claude-only config pack ではない |
| Video proof | `video-suite-production.md` が local rough render、timeline、captions、source inventory、publish-candidate clip set、self-eval、black-frame QA、no-private-path 公開ルールをゲート |
| Growth proof | `partner-sponsor-talks-pack.md` が sponsor、partner、consulting、talks、podcasts、GitHub Discussion、video CTA 向け approval-gated copy を提供 |
| Owner approval proof | `owner-approval-packet-2026-05-19.md` が release、package、plugin、video、billing、social、outbound decision gates を集約 |
| Business baseline | hypergrowth command center と partner pack は `$1,728/mo` current MRR、`$10,000/mo` target MRR、`$8,272/mo` gap を使用 |
| Operator dashboard | `operator-readiness-dashboard-2026-05-20.md` が growth baseline を queue、publication、video、outbound、AgentShield、ECC Tools billing/env-file gate、Linear、supply-chain control surface に統合 |
| Linear progress proof | Linear project document `ecc-may-19-post-pr-2002-sync-64cef8f668e0` は post-PR #2002 状態を mirror し、launch materials、AgentShield、ECC Tools deep analysis、observability、final release publication の active lanes を記録; Linear document `ecc-may-19-late-queue-zero-and-release-gate-sync-1c26f65e6b3f` は PR #2013 approval gate、ECC-Tools #79 redaction hardening、JARVIS #15/#16 queue/deploy repair エビデンスを追加; 5月20日 Linear comments（`74dcc101-3be5-4173-be13-62b80d54f569` 他）は ECC-Tools hosted observability readback、AgentShield adapter、AgentShield Dependabot alert closure、Marketplace selected-target announcement-gate エビデンスを ITO-44、ITO-49、ITO-54、ITO-56、ITO-57、ITO-61、project に追加 |

## 現在の公開ブロッカー (Current Publication Blockers)

- このパスでは GitHub prerelease `v2.0.0-rc.1` はまだ作成されていません。
- npm `ecc-universal@2.0.0-rc.1` はまだ `next` dist-tag に公開されていません。
- Claude plugin tag と marketplace 伝播は引き続き承認ゲート付きです。
- Codex repo-marketplace 配布は過去のエビデンスで検証済みですが、公式 Plugin Directory 公開は OpenAI 提出または listing エビデンス待ちのままブロックされています。
- ECC Tools billing/native-payments エビデンスは、internal bearer-token path または selected-target announcement gate によるブロックは解消されました。起動直前に `billing:kv-readback -- --select-ready-target --require-ready` と `billing:announcement-gate -- --select-ready-target` を繰り返し実行し、コピーは最終リリース、plugin、live URL、owner-approval ゲートの背後に置いたままにしてください。
  ECC-Tools PR #89 (`512bca6`) が `billing:kv-readback -- --select-ready-target --require-ready` を追加しました。2026-05-20 の production run は account login を出力せずに、旧 missing-target-state blocker を解消しました。
  ECC-Tools PR #90 (`16a5bb3`) が selected-target official announcement gate を追加したため、production preflight は raw GitHub login を必要としなくなりました。
  ECC-Tools PR #91 (`72119a1`) が `--env-file` サポートを追加し、無視されるローカル billing 認証情報を読み込んでも secret や account login を出力しません。
  ECC-Tools PR #92 (`18d8019`) が non-breaking operator bearer path を追加し、ECC-Tools PR #93 (`d3d62df`) が live gate pass を記録しました。
- リリースノート、X、LinkedIn、GitHub release、GitHub Discussion、長文コピー、sponsor outreach、partner outreach、consulting copy、conference pitches、podcast pitches は、投稿または送信前に最終 live URL と人間による承認がまだ必要です。
- Discord/community リンクは、公開ドキュメントでユーザーを誘導する前に、実際の invite または bot/guild 認証情報パスが必要です。

## 結果 (Result)

追跡対象の公開 PR キュー、issue キュー、discussion キュー、canonical ECC identity、release video suite、preview pack、growth outreach packet、per-project Claude Code adapter surface、continuous-learning project registry hygiene、GateGuard quoted git introspection fix、deterministic release approval gate、ECC-Tools billing-announcement redaction hardening、selected-target billing readback、selected-target announcement gate、billing gate env-file operator path、ECC-Tools hosted observability readback、AgentShield Zed/VS Code adapter coverage、AgentShield Dependabot alert closure、JARVIS security/deploy queue repairs は、2026年5月20日時点で ECC `main`（`c2471fe5c535310f8a8008c9ed7ea9f6757b33f2` まで）、ECC-Tools `main`（`72119a1acc6f5a0cd3bb5d90afd6e87fd1fefd05` まで）、AgentShield `main`（`25d91f0002214c408da4ceaac7def20bad40ca10` まで）で最新です。残る video 作業は owner approval、upload、public URL 添付であり、render や QA production ではありません。

これは公開準備を改善しますが、`publication-readiness.md` にある承認ゲート付きの release、package、plugin、billing、Discord、announcement 手順の代替にはなりません。
