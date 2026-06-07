# ECC 2.0 GA ロードマップ (ECC 2.0 GA Roadmap)

このロードマップは、アクティブな Linear プロジェクトの永続的な repo ミラーである:

<https://linear.app/itomarkets/project/ecc-platform-roadmap-52b328ee03e1>

Ito Markets ワークスペースでは Linear issue の作成が再び利用可能になった。ライブ実行の真実は次に分散している:

- Linear プロジェクトのドキュメント、issue レーン、依存関係、マイルストーン;
- この repo ドキュメント;
- マージ済み PR エビデンス;
- `~/.cluster-swarm/handoffs/` 配下の handoff。

5月19日のリリース/グロース実行マップは
[`docs/releases/2.0.0/ecc-2-hypergrowth-release-command-center.md`](releases/2.0.0/ecc-2-hypergrowth-release-command-center.md)
にある。最終的な ECC 2.0 repo アイデンティティ、video suite、
partner/sponsor funnel、consulting/talk funnel、social launch plan の
operator surface である。

## 2026-05-20 差分 (2026-05-20 Delta)

- 追跡対象の platform audit は 5月20日時点でも green のまま。`affaan-m/ECC`、`affaan-m/agentshield`、`affaan-m/JARVIS`、`ECC-Tools/ECC-Tools`、`ECC-Tools/ECC-website` 全体で open PR 0、open issue 0、discussion maintainer-touch gap 0、回答可能な Q&A gap 0、競合 PR 0、blocking dirty file 0。
- 新規 #2015 setup-location Q&A に回答し accepted にマーク済み。回答は install ガイダンスを保守的に維持: `C:\` へは install しない。通常の workspace を使い、`ecc@ecc` Claude plugin を一度だけ install し、manual rules 利用時は必要な rule folder のみコピーし、plugin と full manual install の重ね掛けは避ける。
- ECC-Tools PR #80-#88 が次の hosted-platform バッチを land: runtime receipt は failure reason を必須化。AgentShield fleet approval ID は hosted security review を通過し comments/check-runs に描画。Linear follow-up sync は deterministic external ID を再利用。hosted AgentShield remediation item は Linear に sync。hosted job observability event は queued、completed、blocked、failed、budget-blocked 状態で emit。hosted job status comment と hosted depth-plan check-run の両方が直近の observability/budget event を readback。PR #88 は operator dashboard と production smoke test 向け authenticated observability API readback を追加。
- AgentShield PR #94 が次の cross-harness adapter slice を land: Zed と VS Code が first-class adapter detection。`.zed/settings.json` と `.zed/tasks.json` が discoverable scan input。`.zed/setup.mjs` が `.vscode/setup.mjs` と同じ AI-tool persistence IOC rule をトリガー。
- AgentShield PR #95 が default-branch の残り Dependabot alert を解消: transitive `brace-expansion` 5.x lockfile entry を `5.0.6` へ移行。merge 後の Dependabot open-alert API は `[]` を返し、ローカル `npm audit --audit-level=moderate` は vulnerability 0。
- ECC PR #2019 が Marketplace Pro selected-target release-gate sync を `30f60710d4e0424fc70d9bbdc105009db141d9d8` として本 repo に merge。merge 後 main CI run `26135974576` は lint、coverage、security、validation、full OS/package-manager matrix で green 完了。
- ECC PR #2020 が selected-target announcement-gate mirror を `c2471fe5c535310f8a8008c9ed7ea9f6757b33f2` として merge。merge 後 main CI run `26136949698` は lint、coverage、security、validation、full OS/package-manager matrix で green 完了。
- ECC-Tools PR #90 が `billing:announcement-gate -- --select-ready-target` 向け selected-target official announcement gate を追加。safe production preflight は raw GitHub login を不要化し、live execution 前は local/internal `INTERNAL_API_SECRET` 入力のみで block。
- ECC-Tools PR #91 が両 billing gate script に `--env-file` サポートを追加。ignored な local operator credential file が `INTERNAL_API_SECRET`、Cloudflare auth、Wrangler auth mode、target fallback を secret 内容を出力せず供給可能。merge 前に Verify、Security Audit、Workers Builds が `72119a1` で pass。merge 後 main CI run `26137280847` 成功。
- ECC-Tools PR #92 が non-breaking `INTERNAL_OPERATOR_API_SECRET` bearer を追加。privileged internal API route が既存 `INTERNAL_API_SECRET` を rotate せず受理。merge 前に Verify、Security Audit、Workers Builds が `18d80197be779619283e0b37e2952bac53819a07` で pass。merge 済み Worker は `api.ecc.tools` に deploy。
- 5月20日の live native-payments gate は pass: vault-backed Wrangler readback が fingerprint `e953a74209fe` の ready Marketplace Pro target を選択。both key family あり、webhook evidence ready、KV blocker 0。公式 `npm run billing:announcement-gate -- --select-ready-target` は新 operator bearer path 経由で `announcementGateReady: true`、required action 0、blocker 0、audit summary 6 pass / 1 warn / 0 fail を返却。
- ECC-Tools PR #93 が app launch checklist と distribution roadmap に live billing evidence を `d3d62df83fa075660fa4530c3e0edc311a4355fe` として記録。public native-payments copy は billing evidence ではもはや block されないが、publication timing は final release、plugin、live URL、owner-approval gate の後ろに残る。
- Linear ITO-54 と ECC Platform Roadmap に 5月20日 ECC-Tools hosted observability 更新コメント `74dcc101-3be5-4173-be13-62b80d54f569` と `348ea8f5-2a2d-46d9-a0fe-ed99653e7fe5` を追加。先行 PR #84/#85 コメントは remediation sync と hosted observability event を記録。PR #88 は Linear コメント `291e2a4b-06e3-4672-a057-cdb141478161` と `b2d35de0-ca49-44cb-982a-ddec229e7691` に記録。AgentShield #94 は ITO-49 コメント `faed69dd-35f5-469d-acb5-ddde6a70d6a1` と project コメント `70187c1e-d481-4181-b418-09bd65d54b5e` に記録。AgentShield #95 は ITO-49 コメント `371fc3e4-611f-4d20-a23f-67db1260b418`、ITO-57 コメント `bd06e252-15c1-4256-b667-caa3f64f5968`、project コメント `22c2c388-2fd1-4dea-a939-6141f40c9a21` に記録。
- Linear ITO-61 と ECC Platform Roadmap に 5月20日 Marketplace Pro release-gate コメント `467d148a-712a-4777-aad9-95593e9f1739` と `7642ee9c-3107-400c-a229-53e2895a8914` を追加。ECC-Tools #89、ECC #2019、green post-merge CI run、残り internal bearer-token gate を記録。repo mirror は ECC-Tools #90 と #91 を selected-target announcement gate と billing gate env-file operator-path follow-up としても記録。

## 2026-05-19 差分 (2026-05-19 Delta)

- public repo identity は `affaan-m/ECC`。release、package、plugin、workflow、launch-copy surface は現行 public link にその URL を使う。
- 5月19日後半の queue drain は ECC `main` に deterministic `release:approval-gate` を追加し、ECC-Tools billing-announcement redaction hardening を mergeし、JARVIS Dependabot/deploy repair tail を解消。追跡 platform audit は 5 repo 全体で open PR 0、open issue 0、discussion gap 0 の green。ただし release/publication action は owner と live-URL gate のまま。
- ECC 2.0 release story は product shape を直接先頭に置く: harness-native operator system、再利用可能な skills/rules/hooks/MCP conventions、`ecc2/` alpha control plane、optional operator shell としての Hermes、business surface としての ECC Tools Pro/Sponsors/consulting。
- copy は repo rename や config-pack migration として見せない。release proof は install flow、cross-harness demo、security evidence、hosted product evidence、video suite で system を示す。

## 現在のエビデンス (Current Evidence)

2026-05-20 時点:

- GitHub キューは `affaan-m/ECC`、`affaan-m/agentshield`、`affaan-m/JARVIS`、`ECC-Tools/ECC-Tools`、`ECC-Tools/ECC-website` 全体でクリーン: 直近の `platform-audit` スイープは open PR 0、open issue 0、discussion maintainer-touch gap 0、accepted 回答のない回答可能 Q&A 0、blocking dirty file 0 を検出。現行の `scripts/work-items.js list --json` 出力も `totalCount: 0` を報告し、SQLite bridge に open または blocked なローカル work item はない。
- オーナー横断のキュー整理も要求予算内: `docs/releases/2.0.0-rc.1/owner-queue-cleanup-2026-05-18.md` は、24 件の stale dependency-bot PR と 72 件の stale legacy payments/0EM roadmap issue を close した live `gh search` スイープ、続く 9 件の stale/generated/conflicting/test-noise PR と 5 件の legacy/outreach/placeholder issue の close を記録。より広い `affaan-m` オーナー namespace は live `gh search` で open PR 0、open issue 0。close 時に触れた archived repo は archived 状態に復元済み。
- GitHub discussions は追跡 repo 全体で最新: `affaan-m/ECC` は discussion 合計 60、maintainer touch なし 0（5月19日 #2003 AURA integration proposal は core wallet/escrow coupling ではなく external-adapter proposal としてルーティング、5月20日 #2015 setup-location Q&A は回答・accepted 済み）。AgentShield、JARVIS、ECC Tools、ECC Tools website は discussions 無効または合計 0。`docs/architecture/discussion-response-playbook.md` は ITO-59 応答カテゴリ、public template、security-escalation path、将来 discussion バッチ向け readback ルールを提供。
- 現行 Linear roadmap は 16 issue レーン（`ITO-44` 〜 `ITO-59`）と 5 マイルストーン: Security and Access Baseline、ECC 2.0 Preview and Publication、AgentShield Enterprise Iteration、ECC Tools Next-Level Platform、Legacy Audit and Salvage。
- Linear live sync は 5月19日 PR #2002 merge と discussion バッチで最新: ECC platform project には post-PR #2002 sync document `ecc-may-19-post-pr-2002-sync-64cef8f668e0`、project comment `a6411e3a-8c8e-4a58-adba-687e77d4c543`、ITO-44/47/48/49/51/54/56 の issue comment がある。ITO-47、ITO-48、ITO-49、ITO-51、ITO-54、ITO-56 は各レーンに現行 implementation/evidence と残り gate/readback work があるため In Progress へ移動。ITO-57 には 5月18日 emergency supply-chain refresh comment（`3fe5b2b7-c4fe-401c-a317-b40d72119cb3`）が残る。この workspace では Linear project status update は無効のため、project document と comment がサポートされる external status surface。
- `main` 上の直近 5月18日 merge バッチには PR #1970 workflow-security validator bypass 修正、PR #1971 metrics bridge cost-reporting と warning de-dup 修正、PR #1972 `uncloud` skill activation structure、PR #1976 OpenAI/AstraFlow provider response guard、ECC-Tools Wrangler OAuth billing readback mirror evidence、`04d4d819` defensive-deny IOC scanner hardening recheck、`7911af4a` release OIDC publishing-scope hardening、`97567a91` release workflow line-ending normalization、refresh 済み operator dashboard 付き release evidence が含まれる。
- `docs/releases/2.0.0-rc.1/publication-evidence-2026-05-19.md` は
  現在の 5 月 19 日 queue-zero 状態、canonical ECC identity マージ、リリース video
  suite ゲート、partner/sponsor/talk outreach パック、owner 承認パケット
  (`owner-approval-packet-2026-05-19.md`)、現行 preview-pack smoke digest
  `eebb8a66c33e`、ローカル 2568-test スイート、PR #2001 マージと GitHub Actions 実行
  `26102500291` 成功、PR #2002 の owner-approval ダッシュボード ゲート refresh と
  GitHub Actions 実行 `26103853507`、PR #2004 の Linear readiness 証拠 sync と
  GitHub Actions 実行 `26105012698`、さらに PR #2005 の post-PR #2004
  証拠 refresh と GitHub Actions 実行 `26106321921`、PR #2008 の supply-chain
  証拠ゲート修正と GitHub Actions 実行 `26108473648`、post-PR #2006 main CI 実行
  `26109953093`、PR #2009 の project-registry hygiene GitHub Actions 実行
  `26111313938`、post-PR #2009 main CI 実行 `26111946778`、post-PR #2011
  GateGuard main CI 実行 `26113695068`、post-PR #2013 release-approval-gate
  main CI 実行 `26128749863` を記録。5 月 19 日後半の sync ターゲットには
  ECC-Tools PR #79 billing-announcement redaction 強化と JARVIS PR #15
  / PR #16 の queue/deploy 修復も含まれる。workflow 修復後は JARVIS main CI、CodeQL、Deploy は green。
  Linear external project status surface には post-PR #2002 sync document と late-pass document
  `ecc-may-19-late-queue-zero-and-release-gate-sync-1c26f65e6b3f` の両方、および project
  comment `d42bf0e2-7a8e-4934-9f3f-e281498ee805` がある。supply-chain ゲートは
  現行 npm audit/signature 検証に必要な `@types/node@25.7.0` ピン留めと `brace-expansion` lock refresh も
  記録する。
- 5 月 20 日の ECC-Tools hosted-platform pass は PR #80
  から PR #88 まででその証拠を拡張。いずれも green な GitHub Verify/Security Audit/Workers
  Builds チェック後にマージ済み。最終 depth-plan observability スライスのローカル検証は
  focused hosted depth-plan route テスト、フル route スイート
  (89/89)、typecheck、lint、フル ECC-Tools Vitest スイート (683/683)、
  `git diff --check` を通過。PR #88 はさらに operator
  ダッシュボードと production smoke テスト向け `/api/analysis/observability` で認証済み hosted
  observability readback を公開。ローカル検証は
  typecheck、lint、フル ECC-Tools Vitest スイート (686/686)、
  `git diff --check` を通過。
- AgentShield PR #94 はローカル検証（typecheck、lint、コア scanner/rule 重点
  テスト、フル `npm test`（1822 テスト）、`npm run build`、`git diff --check`）後、
  Zed と VS Code を第一級 adapter レジストリに追加。
  GitHub チェックは GitGuardian、scan suite、self-scan、
  self-scan examples、Node 18/20/22 CI、CodeRabbit、Cubic で、一時的な GitHub artifact-upload 失敗の
  再実行後に通過。
- AgentShield PR #95 は脆弱な `brace-expansion` 5.x
  transitive lockfile エントリを `5.0.6` に更新することで Dependabot #20 / `GHSA-jxxr-4gwj-5jf2` /
  `CVE-2026-45149` を解消。ローカル検証は
  `npm audit --audit-level=moderate`、typecheck、lint、フル `npm test`
  （1822 テスト）、build、空白チェックがすべて通過。GitHub チェックは
  Verify Node 18/20/22、self-scan、self-scan examples、Test GitHub Action、
  GitGuardian、CodeRabbit、Cubic で通過。
- `docs/releases/2.0.0-rc.1/operator-readiness-dashboard-2026-05-20.md` は
  ライブプラットフォーム監査証拠から ITO-44 prompt-to-artifact ダッシュボードを
  再生成: PR キュー、issue キュー、discussion キュー、ローカル worktree ゲート、
  ダッシュボード生成、supply-chain ループは最新; ダッシュボードは `$1,728/mo` から
  `$10,000/mo` の hypergrowth ベースライン、リリース video-suite レーン、
  partner/sponsor/talk outbound パック、owner 承認パケットも追跡; publication、
  plugin、billing、AgentShield、ECC Tools、Linear リリース
  gate sync、最終 outbound 承認が次の作業として残る。
- `docs/releases/2.0.0-rc.1/publication-evidence-2026-05-17.md` は
  5 月 17 日の queue-zero 状態、日本語ローカライズのマージ、Dependabot TypeScript
  と Node type のマージ、マージ後の ja-JP lint 修復、Mini Shai-Hulud/TanStack
  ローカル保護の再チェック、npm audit/signature チェック、現行オペレーター
  ダッシュボード、`99dd6ac0` の GitHub CI 成功を記録。
- `docs/releases/2.0.0-rc.1/publication-evidence-2026-05-16.md` は
  キュー、discussion、Linear ロードマップ、ECC Tools アクセス、Mini Shai-Hulud/TanStack
  フルキャンペーンのフォローアップ、スケジュール済み supply-chain watch カバレッジ、no-lifecycle
  CI インストール強化、GitHub Actions キャッシュのパージ、AgentShield #85
  registry-signature 検証、AgentShield #86 evidence-pack CI provenance、
  AgentShield #87 plugin-cache runtime-confidence 分類、AgentShield
  #88 evidence-pack inspect/readback、AgentShield #89 evidence-pack fleet
  ルーティング、AgentShield #90 fleet レビュー項目、AgentShield #91
  checksum バックアップ policy エクスポート、AgentShield #92 checksum 検証済み policy
  プロモーション、ECC-Tools #75 billing-gate 強化、
  ECC-Tools #76 AgentShield fleet-summary 消費、ECC-Tools #77 hosted
  finding 証拠パス、ECC-Tools #78 harness policy-route リンク、PR #1947
  supply-chain 保護、5 月 16 日の release-evidence
  更新を記録。
- `npm run harness:audit -- --format json` は現行 `main` で 80/80 を報告。
- `npm run observability:ready` は現行 `main` で 21/21 の readiness を報告。
  GitHub/Linear/handoff/roadmap progress-sync 契約を含む。
- GitHub CI 実行 `26017368895` は
  `04d4d81938b20ac2bac1f0025145ab77d6a59f5f` で正常完了。Validate Components、
  Coverage、Lint、Security Scan、フル Node/package-manager マトリックスを含む。
- Supply-Chain Watch 実行 `26009825837` は
  `3b7e0ba30a027ffd3319c2f145c63076c296d80a` で正常完了。no-lifecycle インストール、
  npm audit/signature 検証、scanner fixtures、advisory-source
  fixtures、IOC/advisory アーティファクト生成、workflow-security 検証を含む。
- PR #1846 は `797f283036904128bb1b348ae62019eb9f08cf39` としてマージされ、
  npm registry signature 検証を永続的な workflow-security ゲートにした:
  `npm audit` を実行するワークフローは `npm audit signatures` が必須。
- PR #1848 は `cbecf5689d8d1bd5915e7031697a1d56aac538f2` としてマージされ、
  `docs/security/supply-chain-incident-response.md` を追加。さらに workflow-security
  validator ルールで `pull_request_target` ワークフローが共有依存キャッシュを
  復元または保存することをブロック。
- PR #1940 は `6951b8d5d29d13cac6b89b461104ad03838553de` としてマージされ、
  永続的な IOC レポートを出力するスケジュール済み supply-chain watch ワークフローを追加。
- PR #1941 は `f7035b5644ffc857879b71c39353b2141f17c3f0` としてマージされ、
  パッケージマネージャーの lifecycle スクリプトを無効化し、Actions 依存キャッシュの使用を削除し、
  validator カバレッジを追加することで lifecycle-hook 侵害に対する CI 依存インストールを強化。
  これらのパターンが静かに再導入されないようにした。
- PR #1850 は `248673271455e9dc85b8add2a6ab76107b718639` としてマージされ、
  読み取り専用 analyzer エージェントと zh-CN コピーから shell アクセスを削除。
  オペレーターエージェントを変更せずにその表面の AgentShield 高重要度 finding を削減。
- PR #1851 は `209abd403b7eaa968c6d4fa67be82e04b55706d6` としてマージされ、
  書き込み権限を持つワークフローでの `actions/checkout` に
  `persist-credentials: false` を必須化。
- PR #1860 は `c2762dd5691a33aaa7f84a0a4901a5bab7980fc8` としてマージされ、
  Ruby/Rails 言語パック表面、インストールエイリアス、
  selective-install コンポーネント、集中 install-manifest executor テストを追加して #1859 をクローズ。
- AgentShield PR #78 は `1b19a985d6ae1346244089a78806a7d5eaaf270e` としてマージされ、
  write/id-token リリースパスで `persist-credentials: false` と
  `npm ci --ignore-scripts` によりリリースワークフローを強化。
- AgentShield PR #79 は `86a823c5f2c35ee97e6ecf6f99e9ac301d54119a` としてマージされ、
  baseline/watch/remediation fingerprint を共有ハッシュ化
  evidence fingerprint ヘルパーに移行。新しい baseline は raw finding 証拠を省略し、
  古い raw-evidence baseline は引き続き比較可能。
- AgentShield PR #80 は `8ed379d1de067b25640ac6273aa4d9f8e6735d43` としてマージされ、
  失敗した corpus gate に優先度付き corpus 精度推奨を追加。
  カテゴリ、欠落ルール、config ID ごとにミスをマッピングし、エンタープライズ
  scanner-regression 作業に実行可能な改善計画を提供。
- AgentShield PR #81 は `6583884e74ba2e896942113e1ce3146230e6fb76` としてマージされ、
  remediation plan に順序付き remediation ワークフローフェーズを追加。
  安全な auto-fix、手動レビュー、検証を安定した finding
  fingerprint 経由でルーティングし、raw 証拠をコピーしない。
- AgentShield PR #82 は `51336ba074ad5e9fed2c0aa3237422be22147e76` としてマージされ、
  組み込み attack corpus を env proxy hijack シナリオで拡張。
  proxy/runtime 変更、env-token 流出、DNS 流出、
  credential-store アクセス、clipboard アクセスをカバー。
- AgentShield PR #87 は `26bb44650663816d07180e0d20c1895e431a326c` としてマージされ、
  インストール済み Claude plugin-cache runtime confidence を追加。キャッシュされた plugin
  finding は `runtimeConfidence: plugin-cache` を出力し、non-secret スコア影響は
  意図した `0.5x` を維持。リポジトリローカルの非 Claude `plugins/cache`
  パスはダウングレードされず、キャッシュされた hook 実装は
  アクティブなトップレベル `hook-code` として表示されなくなった。
- AgentShield PR #88 は `65ed6e2a87545dc99d962b58413f49096a4d70ec` としてマージされ、
  ダウンストリームコンシューマー向けに `agentshield evidence-pack inspect` を追加。
  evidence-pack バンドルは report score、
  finding 件数、runtime confidence、policy、baseline、supply-chain、CI
  コンテキスト、remediation フェーズ、不正アーティファクトエラーのコンパクトな JSON/text readback を持つ。
  すべてのバンドルファイルを手動で開く必要はない。
- AgentShield PR #89 は `521ada9091bb6d818511ab8589ae675b920c106a` としてマージされ、
  ダウンストリーム fleet ルーティング向けに `agentshield evidence-pack fleet <dirs...> [--json]` を追加。
  複数の検証済み evidence pack は finding、policy、baseline、
  supply-chain、remediation の合計とともに
  ready、security-blocker、policy-review、baseline-regression、
  supply-chain-review、invalid ルートに集約される。
- JARVIS PR #13 は `127efabbfb5033ae53d7a53e1546aa3c33d6f962` としてマージされ、
  npm registry signature 検証で CI/deploy ワークフローを強化。
  書き込み権限ジョブで永続化 checkout 認証情報を無効化し、
  `latest` の代わりに Vercel CLI インストールをピン留め。
- ECC-Tools PR #53 は `99018e943d03f024de8c9d278c91f66393d4f1ee` としてマージされ、
  CI の既存 production 依存監査の前に npm registry signature 検証を追加。
- ECC-Tools PR #54 は `05df89721f49c1e19d8502c545e26f5694806998` としてマージされ、
  `open-pr-drafts` が使われない場合に `/ecc-tools followups sync-linear` が
  Linear/project バックログでコピー可能な PR ドラフトを追跡するようにした。
  追加の PR シェルを開かずに有用な stale-PR salvage 作業を保持。
- ECC-Tools PR #55 は `5d8c112cce4794cfa089d5b0ea661ba87a178be1` としてマージされ、
  `/ecc-tools analyze` コメントに analysis-depth readiness を追加。
  CI/CD、security、harness、reference/eval、AI routing/cost-control、
  team handoff 証拠を使い、commit-history のみのリポジトリと
  evidence バックアップおよび deep-ready リポジトリを分離。
- ECC-Tools PR #56 は `5b729c88641eafe80f65364bab3fc74d0270f57b` としてマージされ、
  analysis-depth readiness を CI diagnostics、
  security evidence review、harness compatibility、reference-set evaluation、
  AI routing/cost review、team backlog routing の具体的な hosted job に
  マッピングする認証済み `/api/analysis/depth-plan` 契約を追加。
- ECC-Tools PR #57 は `4cc61112a4cc9feec7b07af09321f360e34af6a4` としてマージされ、
  最初の実行可能 hosted analysis job を追加:
  `/api/analysis/jobs/ci-diagnostics` は CI/CD readiness でゲートし、
  workflow/test-runner/failure-evidence アーティファクトを検査し、CI 強化
  finding と 次アクション を返し、成功した実行後にのみ usage を課金。
- ECC-Tools PR #58 は `ce09dd8d9b46f65c6b88dc4f48cfb6b6227ae0bf` としてマージされ、
  2 番目の実行可能 hosted analysis job を追加:
  `/api/analysis/jobs/security-evidence-review` は security-evidence
  readiness でゲートし、上限付き AgentShield evidence-pack、policy、baseline、
  SBOM、SARIF、security-scan アーティファクトを検査し、supply-chain 証拠
  finding と 次アクション を返し、成功した実行後にのみ usage を課金。
- ECC-Tools PR #59 は `505b372dbd8f75f996d9e2ed079effd30cec5ba5` としてマージされ、
  3 番目の実行可能 hosted analysis job を追加:
  `/api/analysis/jobs/harness-compatibility-audit` は harness-config
  readiness でゲートし、上限付き Claude、Codex、OpenCode、MCP、plugin、
  cross-harness ドキュメントアーティファクトを検査し、ローカルの secret 含有 config
  パスを fetch から除外し、portability finding と 次アクション を返し、
  成功した実行後にのみ usage を課金。
- ECC-Tools PR #60 は `b75e0a49ba5672b1ec9a2a4880ddcfa2d07dc557` としてマージされ、
  4 番目の実行可能 hosted analysis job を追加:
  `/api/analysis/jobs/reference-set-evaluation` は reference-evidence
  readiness でゲートし、analyzer corpus、RAG/evaluator、PR salvage/review、
  harness、security、CI failure-mode 証拠を評価し、明らかな
  secret 含有 fixture パスを fetch から除外し、reference coverage
  finding と 次アクション を返し、成功した実行後にのみ usage を課金。
- ECC-Tools PR #61 は `7b01b67cae0b80774b311cb515b7eca0aa038c65` としてマージされ、
  5 番目の実行可能 hosted analysis job を追加:
  `/api/analysis/jobs/ai-routing-cost-review` は AI routing/cost
  readiness でゲートし、model routing、token budget、usage-limit、rate-limit、
  billing/entitlement、cost-regression、cost-policy 証拠を評価し、
  明らかな secret 含有パスを fetch から除外し、cost-control finding と
  次アクション を返し、成功した実行後にのみ usage を課金。
- ECC-Tools PR #62 は `781d6733e56f7556edb43fb96bdfb00b1f0a3aa6` としてマージされ、
  6 番目の実行可能 hosted analysis job を追加:
  `/api/analysis/jobs/team-backlog-routing` は team handoff/project
  tracking readiness でゲートし、roadmap、runbook、handoff、release-plan、
  issue-template、ownership、project-tracker、backlog、follow-up 証拠を評価し、
  明らかな secret 含有パスを fetch から除外し、team-routing
  finding と 次アクション を返し、成功した実行後にのみ usage を課金。
- ECC-Tools PR #63 は `fb9e4c5ceb9ccde50da74c7a69c3fa4bd321fc07` としてマージされ、
  キュー済み PR 分析で hosted 実行計画をオペレーター可視化:
  キューは PR head SHA に非ブロッキング `ECC Tools / Hosted Depth Plan`
  check-run を公開し、ready/blocked hosted executor コマンドと
  次アクションテキストを含む。check-run 公開はベストエフォートのままなので
  bundle 生成と分析コメントはブロックされない。
- ECC-Tools PR #64 は `72020ef94db94840812977ea7ac37e9344036668` としてマージされ、
  PR 向け hosted job ディスパッチ制御を追加:
  `/ecc-tools analyze --job ...` コメントは
  PR head SHA に対して hosted job をキューし、既存の hosted readiness/evidence
  ゲート経由で実行し、artifacts/findings/次アクション を PR に投稿し、
  idempotency key を job id でスコープして hosted job が bundle
  分析と衝突しないようにした。
- ECC-Tools PR #65 は `bacd4adf6a3a629e8d403865456d15f127baaf4e` としてマージされ、
  hosted job 結果履歴/check-run サマリーを追加:
  キュー済み hosted job は完了またはブロック済み実行の最新結果と不変 run レコードの両方を
  キャッシュし、PR head SHA に非ブロッキング per-job check-run を
  artifacts、findings、readiness ブロッカー、次アクション とともに公開。
- ECC-Tools PR #66 は `4e1db48252d068ea5dcf4308b0bc11b0dfe0c9ce` としてマージされ、
  読み取り専用 hosted ステータスコマンドを追加:
  `/ecc-tools analyze --job status` は現行 PR head の #65 最新結果キャッシュを読み取り、
  次の hosted job コマンド付きのコンパクトな 完了/blocked/未実行 テーブルを投稿。
  作業をキューせず usage も課金しない。
- ECC-Tools PR #67 は `f20e6bec2b0bf49e4cc36e08b7285c795973b73d` としてマージされ、
  hosted depth-plan check-run をステータス対応にした:
  キュー済み PR 分析は `ECC Tools / Hosted Depth Plan` 公開時に
  #65/#66 最新結果キャッシュを読み取り、plan テーブルに最新 hosted 実行ステータスを含み、
  再実行前に次の未実行 ready job を推奨。
- ECC-Tools PR #68 は `2cde524b5ef8f34ab7bb1af973248fe4be4359f8` としてマージされ、
  決定論的 hosted promotion readiness を追加:
  オープン/同期済み PR は非ブロッキング
  `ECC Tools / Hosted Promotion Readiness` check-run を公開し、変更
  ファイルをチェックイン済み evaluator/RAG corpus と比較し、欠落
  hosted-job promotion 証拠を警告。`PR_HOSTED_PROMOTION_READINESS_CHECK_MODE=off` で無効化可能。
- ECC-Tools PR #69 は `d0112dac7cef807ae27def41f057682ef0772cce` としてマージされ、
  決定論的出力スコアリングで hosted promotion readiness を拡張:
  チェックは現行 PR head のキャッシュ済み完了 hosted job 結果を読み取り、
  artifacts と findings を evaluator/RAG corpus
  期待値に対してスコアリングし、ギャップ報告前に一致する hosted artifact を promotion 証拠として扱う。
- ECC-Tools PR #70 は `7001d805ac981fe220b4575159f469fbea9dbb76` としてマージされ、
  hosted promotion 向け retrieval 計画を追加:
  チェックはキャッシュ済み hosted artifact、
  hosted finding、期待証拠パス、変更ソースパスからランク付き retrieval 候補を出力し、
  後続 hosted judge に変更パスのみから promote しないよう指示する
  model prompt seed も出力。
- ECC-Tools PR #71 は `d41e59ff00fe1bd0b0c96386e56bc5269d7b9c15` としてマージされ、
  最初の model バックアップ hosted promotion judge 契約を追加:
  チェックはプロバイダー中立の `hosted-promotion-judge.v1` リクエスト
  契約を出力し、hosted retrieval 証拠、entitlement、
  残予算、プロバイダー設定が揃わない限りフェイルクローズ。ライブ model 呼び出しはまだ行わない。
- ECC-Tools PR #72 は `973bc51e5436dd279ae5a890cce9811485eef0b5` としてマージされ、
  明示的ゲートの背後で hosted promotion model judge を実行:
  `PR_HOSTED_PROMOTION_MODEL_JUDGE_MODE=execute` は hosted retrieval 証拠、entitlement、budget、プロバイダー、
  executor ゲート通過後にのみ設定済みプロバイダーを呼び出す。チェックは非ブロッキング、strict-JSON-only のまま、
  引用なしまたは hosted 外 model 出力を拒否し、raw レスポンスはエコーしない。
- ECC-Tools コミット `05d4e8296e37ba72e471beaa23ea4c81eb2aa31f` は
  hosted promotion model judging にオペレーター可読の監査トレースを追加:
  check-run は受理決定とともに決定論的リクエスト fingerprint と
  allowed-citation 件数をレンダリングし、raw プロバイダー出力は公開しない。
- ECC-Tools PR #73 は `7d0538c9354e18adbfc72ef00d858949a817fa48` としてマージされ、
  `/api/billing/readiness` にフェイルクローズ native-payments announcement gate を追加:
  公開 payment 主張は Marketplace 管理テストアカウントから
  `announcementGate.ready === true` が必要。launch コピーが release review を超える前に。
- ECC-Tools コミット `91a441b92342b842832ac28b018ee46f0c4a906f` は
  `npm run billing:announcement-gate -- --preflight` を追加し、オペレーターが
  特権 readback 呼び出し前に Marketplace テストアカウント、内部 API トークンの存在、
  billing-readiness エンドポイントを検証できるようにした。
- ECC-Tools コミット `eb6941290b2fa70db01a51084e9e79a160238468` は
  最初のライブ production readback 状態を記録: Cloudflare Worker secret
  名に `INTERNAL_API_SECRET` を含むが、Marketplace 管理アカウントはまだ
  announcement gate を通過できなかった。
- ECC-Tools コミット `95d0bec69dbcf364ed084e983a40d0a94d443d16` は
  `npm run billing:kv-readback` による反復可能な集約 production KV readback を追加:
  最新の API 認証実行は 253 件の
  `account-billing:*` レコードと 253 件の `billing-state:*` レコードを検出したが、
  Marketplace 管理 Pro `billing-state:*` レコードは 0 件。native-payments コピーは
  `--require-ready` と公式 internal
  announcement gate の通過までブロックされたまま。
- ECC-Tools コミット `285967807ea7b5eb3146bc984fb2229db67d4290` は
  native-payments announcement readiness 通過前に Pro billing-state レコードへの
  GitHub Marketplace webhook provenance を必須化。CI 実行
  `26013559229` は push された head で成功。
- ECC-Tools コミット `42653f9140c232961280d961ed76a6142433cfa1` は
  `npm run billing:kv-readback -- --wrangler` を追加し、オペレーターが
  別途 Cloudflare API トークン/global key を要さず認証済み Wrangler OAuth
  セッション経由で集約 production KV readback を実行できるようにした。CI
  実行 `26016223013` は成功。最新ライブ readback は 253 件の
  `account-billing:*` レコードと 253 件の `billing-state:*` レコードを検出。194 件の
  marketplace/free 状態、59 件の Stripe/pro 状態、0 件の Marketplace Pro 状態、0 件の
  ready-like Marketplace Pro 状態、0 件の parse 失敗。native-payments
  コピーは実際の Marketplace 管理 Pro webhook が
  billing-state provenance を作成し、`--require-ready` と公式 internal
  announcement gate が通過するまでブロックされたまま。
- ECC-Tools コミット `632e059e51b6e1297ba118807c8b5b2adbac74ce` は
  `npm run billing:kv-readback -- --account <github-login> --require-ready` による
  ターゲットアカウント billing readback を追加。
  レポートはアカウント login と raw KV キーをマスキングし、安定した
  fingerprint とサニタイズ済み readiness ブール値のみを出力。ターゲット
  Marketplace Pro テストアカウントが native-payments announcement
  readback gate を通過するには `account-billing:<login>` と `billing-state:<login>` の両方が必要。
  CI 実行 `26018941515` は成功。2026-05-18 のライブ再チェックは
  ターゲットアカウント blocker として Linear ITO-61 を切り出した。
- ECC-Tools コミット `d5f60db` は `npm run billing:kv-readback` に
  サニタイズ済み Marketplace-source provenance 件数を追加。含む項目:
  `marketplaceSourceRecords`、`marketplaceSourceWithWebhookEvidence`、
  `marketplaceSourceWithoutWebhookEvidence`、`byMarketplacePlanName`、
  `byMarketplaceEventAction`。2026-05-18 のライブ Wrangler OAuth readback は
  動作し、256 件の account-billing レコード、256 件の billing-state レコード、197 件の
  Marketplace-source レコード、59 件の Stripe-source レコード、53 件の Pro レコード、0 件の
  Marketplace Pro レコード、4 件の Marketplace webhook-provenance レコード、すべて
  `Open Source` 購入、193 件の webhook provenance なし Marketplace-source レコードを検出。
  native-payments コピーは Linear ITO-61 によりブロックされたまま。実際の Marketplace 管理 Pro webhook が
  ターゲットアカウント provenance を作成し、
  `billing:kv-readback -- --wrangler --wrangler-bin ./node_modules/.bin/wrangler --account <github-login> --require-ready`
  と公式 internal announcement gate が通過するまで。
- ECC-Tools コミット `13cd3fc` は billing-state キーの大文字小文字を正規化し、
  Marketplace webhook 書き込みと announcement readback が GitHub login
  のケースで一致するようにした。現行 head CI `26037611421` は通過。コード側 readback 強化は
  グリーンのままだが、ライブ Marketplace Pro 状態は作成しない。
- ECC-Tools コミット `69ca535` は hosted team-learning フィードバック制御を表示:
  harness compatibility と team-backlog routing は adaptive 推奨が
  チーム所有キューにルーティングされる前に retention 日数、
  deletion route/SLA、opt-out route を表示。Linear ITO-52 は完了。CI `26054455434`。
- ECC-Tools コミット `e56fc1a` は
  `brace-expansion@5.0.6` の lockfile を更新し、CVE-2026-45149 の Dependabot alert 44 を修正。
  GitHub API は `2026-05-18T19:10:15Z` に `state: fixed` を報告。現行 head
  CI `26054671308` は通過。
- ECC-Tools PR #89 は `512bca6b99cdaa67058a6aa9a4e7e7f0b1d9873a` としてマージされ、
  `npm run billing:kv-readback -- --select-ready-target --require-ready` を追加し、
  オペレーターが login を渡したり表示したりせずに ready Marketplace Pro アカウントを証明できるようにした。
  2026-05-20 の production Wrangler OAuth readback は webhook provenance 付きの
  ready 相当 Marketplace Pro レコードと 0 件の parse 失敗を検出。
  選択ターゲットレポートは安定 fingerprint のみを出力し、両キーファミリー、`marketplace` source、`pro` tier、シート準備完了、
  webhook エビデンス準備完了、自動超過課金無効、blocker 0 件を確認。
  旧「Marketplace 管理 Pro ターゲット billing-state なし」blocker は解消。Linear
  コメント `f14ed2fe-a219-470c-8119-63429e197027` はマスキング済み readback
  件数を記録。
- ECC-Tools PR #90 は
  `16a5bb33ee5ce7c31d2ad8d041e5afac03308f05` としてマージ。Verify、Security Audit、
  Workers Builds 通過後。`/api/billing/readiness?selectReadyTarget=1` と
  `npm run billing:announcement-gate -- --select-ready-target` による
  選択ターゲット公式 announcement gate を追加し、オペレーターは公式
  native-payments gate で raw GitHub login を渡したり表示したりする必要がなくなった。
  2026-05-20 の安全な本番 preflight は選択済み ready ターゲットを要求し、
  残り blocker を欠落しているローカル/internal `INTERNAL_API_SECRET` bearer トークンに絞った。
  native-payments コピーはそのトークンパスが利用可能になり、ライブ
  `billing:announcement-gate -- --select-ready-target` 呼び出しが通過するまでブロックされたまま。
- ECC-Tools PR #91 は `72119a1acc6f5a0cd3bb5d90afd6e87fd1fefd05` としてマージ。
  Verify、Security Audit、Workers Builds 通過後。announcement
  gate と KV readback スクリプト向け `--env-file` サポート付き billing
  gate env-file オペレーターパスを追加。読み込んだ secret と
  アカウント login が表示されないことを証明する sentinel テストも追加。
- ECC-Tools PR #92 は `18d80197be779619283e0b37e2952bac53819a07` としてマージ。
  Verify、Security Audit、Workers Builds 通過後。オプションの
  `INTERNAL_OPERATOR_API_SECRET` recovery bearer を追加し、オペレーターは主要 `INTERNAL_API_SECRET` を
  置き換えずに特権 internal readiness gate を実行可能。マージ済み Worker は
  ライブ gate 実行前に `api.ecc.tools` にデプロイ済み。
- ECC-Tools PR #93 は `d3d62df83fa075660fa4530c3e0edc311a4355fe` としてマージ。
  Verify、Security Audit、Workers Builds 通過後。app launch チェックリストとロードマップに
  ライブ 2026-05-20 billing 証拠を記録:
  選択済み ready Marketplace Pro ターゲット、fingerprint `e953a74209fe`、KV
  blocker 0 件、preflight 準備完了、`announcementGateReady: true`、必須アクション 0 件、
  blocker 0 件、監査サマリー 6 pass / 1 warn / 0 fail。native-payments コピーは
  billing 証拠ではブロックされなくなったが、最終 announcement タイミングは依然として
  release、plugin、live URL、owner-approval gate を要する。
- Handoff `ecc-supply-chain-audit-20260513-0645.md`（
  `~/.cluster-swarm/handoffs/` 配下）は
  5 月 13 日の supply-chain スイープを記録: TanStack/Mini Shai-Hulud 指標に対する
  アクティブ lockfile/manifest ヒットなし。アクティブ npm lockfile 全体で
  npm audit/signature チェックはクリーン。`ecc2` の `cargo audit` はクリーン。trunk `pip-audit`
  はクリーン。JARVIS backend pinned-graph Python 監査はサポート対象
  Python 3.12 ターゲットでクリーン。
- PR #1861 検証は `node scripts/harness-audit.js --format json` を
  70/70、`npm run observability:ready` を 21/21 で更新。
- PR #1862 は JARVIS backend Python 監査をサポート対象 Python 3.12 pinned graph に対して
  再実行した後、このロードマップを更新。
- `docs/architecture/harness-adapter-compliance.md` は Claude Code、Codex、
  OpenCode、Cursor、Gemini、Zed-adjacent、dmux、Orca、Superset、Ghast、
  terminal-only サポートをインストールパス、検証コマンド、リスク
  ノートにマッピング。
- `npm run harness:adapters -- --check` は公開 adapter
  マトリックスが
  `scripts/lib/harness-adapter-compliance.js` のソースデータと一致することを検証。
- `docs/releases/2.0.0-rc.1/publication-readiness.md` は GitHub release、
  npm dist-tag、Claude plugin、Codex plugin、OpenCode package、billing、
  announcement 公開を新鮮な証拠フィールドでゲート。
- `docs/releases/2.0.0-rc.1/naming-and-publication-matrix.md` は
  rc.1 命名決定を記録: Everything Claude Code (ECC) として出荷、npm は
  `ecc-universal` を維持、Claude/Codex plugin slug は `ecc` を維持、より広い repo/package 改名は
  リリースパイプラインが実証されるまで延期。
- `docs/releases/2.0.0-rc.1/publication-evidence-2026-05-12.md` は
  dry-run 公開証拠パスを記録: npm pack/publish dry-run、temp install
  smoke、Claude plugin validation/tag preflight、Codex marketplace CLI 形状、
  OpenCode build、残りの承認ゲート付きリリース blocker。
- `docs/releases/2.0.0-rc.1/publication-evidence-2026-05-13.md` は
  リリース readiness 証拠更新を記録: 70/70 harness audit、adapter compliance
  合格、16/16 observability readiness、2376/2376 root Node テスト、markdownlint、
  release-surface と npm publish-surface テスト、462/462 `ecc2` Rust テスト。
- `docs/releases/2.0.0-rc.1/publication-evidence-2026-05-13-post-hardening.md` は
  PR #1850 と
  PR #1851 後の強化後リリース readiness 更新を記録: 70/70 harness audit、adapter compliance 合格、18/18 observability
  readiness、2380/2380 root Node テスト、markdownlint、release-surface と
  npm publish-surface テスト、462/462 `ecc2` Rust テスト、npm audit/signature
  チェック、Rust advisory 監査、TanStack/Mini Shai-Hulud IOC チェック。
- デタッチ済みクリーン worktree
  `bfacf37715b39655cbc2c48f12f2a35c67cb0253` は Claude plugin tag
  dry-run（`--force` なし）、ローカル marketplace 検出、temp-home ローカル
  インストール、有効 plugin 一覧、クリーンアンインストールを `ecc@ecc`
  `2.0.0-rc.1` で検証。
- `docs/architecture/evaluator-rag-prototype.md` と
  `examples/evaluator-rag-prototype/` は最初の読み取り専用
  自己改善 harness プロトタイプを定義: scenario spec、trace、report、
  candidate playbook、verifier 結果、受理 maintainer-salvage、
  billing-readiness、CI-failure-diagnosis、harness-config-quality
  candidate、AgentShield policy-exception シナリオ、拒否された
  unsafe candidate。
- npm パッケージ表面は package `files` 否定ルールと publish-surface 回帰テストにより
  Python bytecode/cache アーティファクトを除外するようになった。
- `docs/legacy-artifact-inventory.md` は現行 checkout に `_legacy-documents-*`
  ディレクトリが存在しないことを記録し、2 つの兄弟
  workspace レベル `_legacy-documents-*` repo をサニタイズ済み抽出ソースとして棚卸し、
  `legacy-command-shims/` をオプトイン archive/no-action
  表面として分類。
- `docs/stale-pr-salvage-ledger.md` は stale PR salvage 結果、
  スキップ PR、置き換え済み作業、残りの #1687、#1609、#1563、#1564、
  #1565 translator/manual レビュー末尾（現在 Linear ITO-55 に紐付け）を記録。
- AgentShield PR #53 は context-rule の誤検知を 2 件削減し、残りの AgentShield issue をクローズした。
- AgentShield PR #55 は GitHub Action に組織ポリシー強制を追加した: `policy` / `fail-on-policy` 入力、`policy-status` / `policy-violations` 出力、job-summary エビデンス、ポリシー違反アノテーション。
- AgentShield PR #56 は組織ポリシー違反向けの SARIF/code-scanning 出力を `agentshield-policy/*` 結果として追加した。
- AgentShield PR #57 は OSS、team、enterprise、regulated、high-risk-hooks/MCP、CI-enforcement policy-pack preset と `agentshield policy init --pack` を追加した。
- AgentShield PR #58 は MCP パッケージ provenance フィールドと、npm と git、pinned と unpinned、known-good、registry 由来の supply-chain エビデンスに関するレポートレベルの件数を追加した。
- AgentShield PR #59 はリスク姿勢、critical/high 優先度の finding、カテゴリ露出、README/API docs、built-CLI スモーク検証、1,704 テストカバレッジを含む自己完結型 HTML エグゼクティブサマリーを追加した。
- AgentShield PR #60 はカテゴリレベルの built-in corpus ベンチマーク出力、`readyForRegressionGate` シグナル、ターミナル `--corpus` カテゴリカバレッジ、README/API docs、built-CLI スモーク検証、1,705 テストカバレッジを追加した。
- AgentShield PR #61 はローカル typecheck、フルテスト、lint、build、remote self-scan/action 検証の後、lockfile のみの `postcss` 8.5.6 -> 8.5.14 更新で残りの Dependabot security/bugfix PR を解消した。
- AgentShield PR #62 は組織ポリシー例外ライフサイクル監査エビデンスを追加した: アクティブ、期限間近、期限切れの exception 件数; owner、ticket、scope、expiry、期限までの日数レポート; ターミナル出力と GitHub Action job-summary エビデンス; README ドキュメント; 再ビルドした action bundle; 1,708 テスト検証。
- AgentShield PR #63 は GitHub Action で baseline drift を公開した: `baseline` / `save-baseline` 入力、baseline drift 出力、job-summary エビデンス、regression アノテーション、README/API docs、再ビルドした action bundle、正常な remote action/self-scan/Node 検証。
- AgentShield PR #64 は第一級の `agentshield baseline write` CLI コマンドを追加した: 重大度フィルタリング、JSON メタデータ出力、README/API docs、再ビルドした CLI bundle、ローカル TDD カバレッジ、正常な remote action/self-scan/Node 検証。
- AgentShield PR #65 は release/security CI 強化のため workflow action をピン留めした。
- AgentShield PR #66 は release publish job でのキャッシュ利用を無効化し、release 公開が可変の復元済みビルド状態に依存しないようにした。
- AgentShield PR #67 は最初のポータブル enterprise evidence-pack bundle を追加した: `agentshield scan --evidence-pack <dir>` は決定論的 manifest、README、JSON、HTML、SARIF、policy-evaluation、baseline-comparison、supply-chain artifact をデフォルト redaction とオプション policy/baseline エビデンス向けの `not-run` マーカー付きで書き出す。
- AgentShield PR #68 は enterprise credential ファミリー向けに evidence-pack redaction を強化した: GitHub fine-grained PAT、GitLab PAT、npm token、Linear API key、Stripe key、Google API key、Hugging Face token、Vercel token、AWS access key ID、JWT 形状の credential を含む。
- AgentShield PR #69 は決定論的 harness adapter registry を追加した。scan レポートは JSON、markdown、terminal、HTML 出力で Claude Code、OpenCode、Codex、Gemini、dmux、汎用ターミナルエージェント、プロジェクトローカル template のローカル marker エビデンスを表示する。
- AgentShield PDF エクスポート判断: 当面はネイティブ PDF writer を延期する。自己完結型 HTML エグゼクティブレポートはエクスポート可能な購入者向けアーティファクトとして残り、必要時には PDF へ印刷できる。ネイティブ PDF 生成は、明示的な enterprise/compliance 需要または HTML レポートの印刷忠実度ギャップが判明するまで待つべきである。
- `docs/architecture/agentshield-enterprise-research-roadmap.md` は次の AgentShield enterprise シグナルを特定する: scanner/report/policy gate から baseline drift、evidence pack、multi-harness adapter、corpus accuracy gate、remediation routing、threat intelligence、ECC-Tools/GitHub App integration を備えたチーム制御プレーンへ移行する。
- ECC PR #1778 は有用な stale #1413 network/homelab architect-agent 概念を回収した。
- ECC-Tools PR #26 は AI routing、Claude/model call、usage limit、quota、analysis-budget 変更向けに cost/token-risk 予測型フォローアップを追加した。対象は budget、quota、rate-limit、cost validation エビデンスを欠く変更である。
- ECC-Tools PR #27 は非ブロッキングの `ECC Tools / PR Risk Taxonomy` check-run を追加した: Security Evidence、Harness Drift、Install Manifest Integrity、CI/CD Recommendation、Cost/Token Risk、Agent Config Review バケット向け。
- ECC-Tools PR #28 は plan limit、entitlement、Marketplace plan shape、subscription source、seat、overage metering 向けの billing readiness 監査チェックを追加した。
- ECC-Tools PR #29 は analyzer、skill、agent、command、harness-guidance 変更向けに deterministic Reference Set Validation シグナルを追加した。対象は eval、golden trace、benchmark、reference-set エビデンスを欠く変更である。
- ECC-Tools PR #30 は フォローアップ生成を run あたり新規 GitHub issue 3 件と draft PR 1 件に上限設定し、残りの決定論的 finding を tracker を溢れさせず Linear/ステータス追跡向けの project sync backlog として出力する。
- ECC-Tools PR #31 は analysis completion コメントに review follow-up シグナルを追加した: 未解決の change request、未解決または古い review thread、明示的な承認のない review activity 向け。
- ECC-Tools PR #32 は workflow と test-runner 変更向けに CI failure-mode 予測型フォローアップ を追加した。対象は failure fixture、captured log、troubleshooting note、dry-run エビデンス、regression coverage を欠く変更である。
- ECC-Tools PR #33 は MCP、plugin、agent、hook、command、harness config 変更向けに harness-config quality 予測型フォローアップ を追加した。対象は harness audit、adapter matrix、cross-harness docs、compatibility regression エビデンスを欠く変更である。
- ECC-Tools PR #34 は skill-quality 予測型フォローアップ と Skill Quality PR-risk バケットを追加した。対象は skill、agent、command、rule guidance 変更で examples、validation、eval、reference エビデンスを欠くものである。
- ECC-Tools PR #35 は RAG/evaluator 予測型フォローアップ と RAG/Evaluator Evidence PR-risk バケットを追加した。対象は retrieval、embedding、ranking、evaluator 変更で reference-set comparison、golden trace、benchmark、fixture、eval-run エビデンスを欠くものである。
- ECC-Tools PR #36 は deep-analyzer 予測型フォローアップ、Deep Analyzer Evidence PR-risk バケット、延期フォローアップ作業向けの Linear-ready project sync backlog テーブルを追加した。
- ECC-Tools PR #37 は 維持済み analyzer corpus fixture、corpus validation tests、将来の 予測型フォローアップ と PR-risk taxonomy checks 向けの同一配置 analyzer reference-set エビデンス認識を追加した。
- ECC-Tools PR #38 は PR review/stale-salvage 予測型フォローアップ、PR Review/Salvage Evidence taxonomy バケット、stale-closure salvage、reviewer-thread、reopen-flow エビデンス向けの 維持済み corpus fixtures を追加した。
- ECC-Tools PR #39 は延期フォローアップ backlog items 向けのオプトイン native Linear GraphQL sync を追加した。GitHub object caps を維持しつつ、`LINEAR_API_KEY` と `LINEAR_TEAM_ID` が設定されているとき Linear issue を作成または再利用する。
- ECC-Tools PR #40 は チェックイン済み evaluator/RAG corpus contract を追加した: stale-PR salvage、billing readiness、CI failure diagnosis、harness config quality、AgentShield policy exceptions、skill-quality evidence、deep-analyzer evidence、RAG/evaluator comparison evidence をカバーし、各シナリオは missing-evidence と evidence-backed diff を検証する。
- ECC-Tools PR #41 は supply-chain dependencies を強化した。
- ECC-Tools PR #42 は AgentShield evidence-pack ギャップ予測を追加し、欠落する policy/baseline/allowlist/suppression/supply-chain エビデンスを PR-risk taxonomy、フォローアップ draft、Linear-ready backlog テーブルへルーティングした。
- ECC-Tools PR #43 は具体的な AgentShield #67 evidence-pack artifact contract を認識した。canonical bundle files が taxonomy を満たし、生成された follow-up PR はメンテナーに `agentshield scan --evidence-pack <dir>` を案内する。
- ECC-Tools PR #55 は最初の hosted/deeper-analysis readiness シグナルを追加した: 分析コメントは、CI、AgentShield、harness、reference-set、RAG/evaluator、AI-routing、cost-control、Linear/project-tracking レーンへ作業をルーティングする前に、repo を commit-history-only、evidence-backed、deep-ready に分類する。
- ECC-Tools PR #56 はそのシグナルを hosted execution-plan contract に変換した: `/api/analysis/depth-plan` は analysis usage を課金せず bundle PR を作成せずに ready/blocked jobs と次アクションテキストを返す。
- ECC-Tools PR #57 は最初のジョブ固有 hosted executor を実装した: `/api/analysis/jobs/ci-diagnostics` は depth-readiness gate、internal API auth、installation ownership、repo-access billing checks、capped workflow file reads、usage accounting を再利用し、具体的な CI 強化 finding を返す。
- ECC-Tools PR #58 は 2 番目のジョブ固有 hosted executor を実装した: `/api/analysis/jobs/security-evidence-review` は同じ hosted gates を AgentShield evidence-pack、policy、baseline、SBOM、SARIF、security scanner artifacts に適用する。
- ECC-Tools PR #59 は 3 番目のジョブ固有 hosted executor を実装した: `/api/analysis/jobs/harness-compatibility-audit` は同じ hosted gates を Claude、Codex、OpenCode、MCP、plugin、cross-harness エビデンスに適用し、secret を含むローカル harness config fetch を避ける。
- ECC-Tools PR #60 は 4 番目のジョブ固有 hosted executor を実装した: `/api/analysis/jobs/reference-set-evaluation` は同じ hosted gates を analyzer corpus、RAG/evaluator、PR salvage、harness、security、CI failure-mode reference エビデンスに適用し、明らかに secret を含む fixture fetch を避ける。
- ECC-Tools PR #61 は 5 番目のジョブ固有 hosted executor を実装した: `/api/analysis/jobs/ai-routing-cost-review` は同じ hosted gates を model-routing、token-budget、usage-limit、rate-limit、billing/entitlement、cost-regression、cost-policy エビデンスに適用し、明らかに secret を含む path fetch を避ける。
- ECC-Tools PR #62 は 6 番目のジョブ固有 hosted executor を実装した: `/api/analysis/jobs/team-backlog-routing` は同じ hosted gates を roadmap、runbook、handoff、release-plan、issue-template、ownership、project-tracker、backlog、follow-up エビデンスに適用し、明らかに secret を含む path fetch を避ける。
- ECC-Tools PR #63 はキュー済み PR 分析完了後に hosted depth-plan check-run を公開し、6 つの hosted executor コマンドを PR head SHA 上で可視化する。check をマージブロッカーにはしない。
- ECC-Tools PR #64 はそれらのコマンドを queue に接続した: メンテナーは PR に `/ecc-tools analyze --job ci-diagnostics`、`security-evidence`、`harness-compatibility`、`reference-set-evaluation`、`ai-routing-cost`、`team-backlog` とコメントでき、hosted job 結果を PR コメントで受け取れる。
- ECC-Tools PR #65 は完了済みとブロック済みの hosted job 結果を 30 日間 analysis cache に永続化し、非ブロッキングの `ECC Tools / Hosted Job: ...` check-run を公開する。メンテナーは古いコメントを読み直さず PR checks 画面から hosted 結果を確認できる。
- ECC-Tools PR #66 は PR コメントから `/ecc-tools analyze --job status` でキャッシュ済み結果を公開する。PR head の完了済み、ブロック済み、未実行 hosted jobs を要約し、次の hosted job コマンドを推奨する。
- ECC-Tools PR #67 はキャッシュ済み結果を hosted depth-plan check-run にフィードバックする。キュー済み分析は静的 readiness 順を繰り返す代わりに、cache state から次の未実行 ready hosted job を推奨する。
- ECC-Tools PR #68 は最初の evaluator-backed hosted promotion gate を追加した: open/synchronize された PR は非ブロッキングの Hosted Promotion Readiness check-run を受け取る。変更ファイルが fixture シナリオに一致しながら期待される evidence artifacts を欠く場合、evaluator/RAG corpus を warning に変換する。
- ECC-Tools PR #69 はその gate を拡張し、現行 PR head のキャッシュ済み完了 hosted job 出力をスコアリングする。hosted artifacts が corpus evidence 期待を満たしてから check が promotion gap を報告できる。
- ECC-Tools PR #76 は hosted security review で AgentShield PR #89 fleet 出力を消費する: `agentshield-evidence/fleet-summary.json` は `evidence-pack-fleet` に分類され、invalid packs と security-blocker routes は high-severity hosted findings となり、policy、baseline、supply-chain routes はオーナー向け review findings を生成する。
- ECC-Tools PR #77 は `31fd883b3f0cee135aee4839b01d34855b7867f6` としてマージされ、hosted job PR コメントと check-run details に `Evidence` 列を追加した。finding ごとに最大 3 つの source evidence path を表示し、AgentShield fleet 由来の findings がオペレーターを正確な bundle artifact へ戻せるようにする。
- ECC-Tools PR #78 は `0d4eb949aa56f56da88e6654273a22ffb95983a1` としてマージされ、AgentShield fleet routes を hosted harness compatibility review にリンクした: fleet summaries は harness エビデンスとして収集され、target paths は Claude、Codex、OpenCode、MCP、plugin、cross-harness owners にマッピングされ、ルーティング済み findings はオペレーターレビュー向けに source evidence paths を保持する。
- ECC-Tools PR #79 は `67ee247ae1b7b50ecc1261ed5d62d65cc8390da8` としてマージされ、billing announcement gate の account 出力を秘匿した: billing preflight と live readback は生のアカウント login や KV key names の代わりに安定した account fingerprint とサニタイズ済み readiness ブール値を出力する。
- ECC-Tools PR #80 は `4efc8cc858022f84c844690f3298633b081c4398` としてマージされ、harness runtime receipts が hosted observability エビデンスとして数えられる前に runtime receipt failure reasons を要求する。
- ECC-Tools PR #81 は `1fbf635f492284f75ba7166c029c39eb8cc15794` としてマージされ、hosted security review 全体で AgentShield fleet approval IDs を保持し、policy-promotion follow-up の owner-review identity を安定させる。
- ECC-Tools PR #82 は `7a7b4d096a176ae80b3a2076c09d45601e36013a` としてマージされ、hosted コメントと check-run に AgentShield fleet approval IDs を表示する。hosted security review から AgentShield policy-promotion review items への直接ブリッジをオペレーターに提供する。
- ECC-Tools PR #83 は `b6b107f33961bef18a85fb619f3a976eb5d752dd` としてマージされ、Linear follow-up sync が title fallback の前に deterministic external IDs を再利用する。繰り返しの `/ecc-tools followups sync-linear` 実行で延期 backlog issue が重複しない。
- ECC-Tools PR #84 は `73bac7058071c55cb30c6b8ac6db779b3660c02c` としてマージされ、workspace token/team が設定されているとき hosted AgentShield remediation items を Linear に同期する。hosted result コメントに作成/再利用済み Linear remediation links を含める。
- ECC-Tools PR #85 は `1637e0f2bfa0a889387f2c20675680ccc5528123` としてマージされ、queued、completed、blocked、failed、budget-blocked 状態の hosted job observability events を `ANALYSIS_CACHE` に出力する。budget snapshots と result counts を含む。
- ECC-Tools PR #86 は `5a9e94d3ff860307c3e7fd9fd065f0de2bd633dd` としてマージされ、`/ecc-tools analyze --job status` で直近の hosted observability events を読み取る。status コメントは直近の job 実行とともに budget snapshots、blocked results、budget-blocked outcomes を表示する。
- ECC-Tools PR #87 は `508fbc02b63cf1fcb5af2f3624608fa66e53b5d4` としてマージされ、同じ hosted observability readback を hosted depth-plan check-run に追加した。PR check 画面を status コメントと整合させる。
- ECC-Tools PR #88 は `c836ac3fb24ed7e2ae38cd61e41c9651ac9c00f8` としてマージされ、authenticated hosted observability API readback を `/api/analysis/observability` で公開した。event type と job 別に直近の hosted events を要約し、不正な stale KV レコードをスキップする。deployment runbook に operator/dashboard readback 向け production smoke コマンドを追加した。
- AgentShield PR #90 は `6d1c57c92000541d65a3b6bc366f0322d7d0dacc` としてマージされ、durable fleet `reviewItems` を追加した: `agentshield evidence-pack fleet --json` は route、severity、repository/target context、source evidence paths、reason、recommendation を含む オーナー向け review items を返す。テキスト CLI もオペレーター向けに同じルーティング済み follow-up リストを出力する。
- AgentShield PR #91 は `73e1e3586dc4513a462e39c9799f75eea104e110` としてマージされ、durable policy pack export を追加した: `agentshield policy export` は選択 pack ごとに 1 つの JSON policy と checksum 付き `manifest.json` を書き出す。pack selection、owners、name prefixes、branch-protection review または downstream policy promotion 向け JSON 出力を含む。
- AgentShield PR #92 は `e7e259dc6212b63a8e03a253ca6b8c1e3c2abff7` としてマージされ、それら bundle 向けの protected promotion gate を追加した: `agentshield policy promote` は export manifest と選択 policy の SHA-256 digest を検証し、改ざんされた policy JSON を拒否し、multi-pack manifest では明示的 pack selection を要求し、active `.agentshield/policy.json` を書き込む前に dry-run JSON review をサポートする。
- AgentShield PR #94 は `4caee27acfadb50a4cd024e738b5c3cbd4b0bb03` としてマージされ、Zed と VS Code 向け editor-native adapter coverage を追加した。Zed `.zed/settings.json`、`.zed/tasks.json`、`.zed` hook-code files が scan inputs となり、adapter reports は Zed MCP/tool-permission/task metadata と VS Code workspace/task/extension metadata を公開し、`.zed/setup.mjs` は AI-tool persistence IOC rule でカバーされる。
- AgentShield PR #95 は `25d91f0002214c408da4ceaac7def20bad40ca10` としてマージされ、`brace-expansion` Dependabot alert を解消した。lockfile は脆弱な transitive 5.x コピーを `5.0.6` に解決する。残りの 1.x コピーは advisory 範囲外である。
- AgentShield main commit `87aec47fb55d04ea28d494852d4f664c268c5601` は policy promotion を拡張し、manifest digest エビデンス、policy-owner approval、protected rollout PR handoff、runtime smoke testing 向けの durable `reviewItems` を追加した。ローカル検証は `npm run typecheck`、`npm run lint`、`npm test` を通過した。GitHub Actions run `25985170621` は Node 18、20、22 と self-scan examples で成功し、兄弟の AgentShield Self-Scan/Test GitHub Action runs も成功した。
- AgentShield main commit `28d08c7f9961eaa54804b26e6352d23b64ae2776` は `.npmrc`、`.pnpmrc`、`.yarnrc`、`.yarnrc.yml`、`pnpm-workspace.yaml`、`pnpm-workspace.yml` 向けの package-manager hardening drift detection を追加した。plaintext registry credential detection、explicit lifecycle-script enablement、missing または weak release-age cooldown findings を含む。ローカル検証は focused rule/scanner tests、`npm run typecheck`、`npm run lint`、`npm run build`、full `npm test -- --run`、`git diff --check` を通過した。GitHub Actions run `25986170958` は成功し、兄弟の AgentShield Self-Scan と Test GitHub Action runs も通過した。
- AgentShield main commit `659f569190f85f6f0808353e096d66c0a6d7817e` はすべての workflow action pin を現行 SHA-pinned の `actions/checkout@v6.0.2` と `actions/setup-node@v6.4.0` に更新した。GitHub Actions run `25986221319` は成功し、以前の Node 20 action-runtime deprecation annotation は最終 CI watch 出力から消えた。
- AgentShield main commit `ee585cd` は、ローカル検証で npm `10.9.4` が `min-release-age` を拒否することが判明した後、package-manager hardening guidance を修正した。npm configs は lifecycle/token drift と unsupported release-age keys をスキャンし、enforceable cooldown findings は pnpm `minimumReleaseAge` / `minimum-release-age` と Yarn `npmMinimalAgeGate` に残す。ローカル検証は package-manager/scanner tests、`npm run typecheck`、`npm run lint`、`npm run build`、`git diff --check` を通過した。GitHub Actions run `25986719058`、Test GitHub Action run `25986719054`、AgentShield Self-Scan run `25986719066` は成功した。
- AgentShield main commit `1124535345d7040242ecd3803f65bcd4dcaf6ec2` は GitHub Action 経由で package-manager hardening を公開し、CI/hosted consumers が generic finding counts とは別に registry credential、lifecycle-script、release-age gate drift をルーティングできるようにした。ローカル検証は focused action tests、`npm run typecheck`、`npm run lint`、`npm run build`、full `npm test`、`git diff --check` を通過した。GitHub Actions CI run `25994354007`、Test GitHub Action run `25994354011`、AgentShield Self-Scan run `25994354026` は成功した。
- ECC PR #1803 は、メンテナー向けクリーンアップ、現行 `main` との整合、完全なローカル検証、および不完全な ja-JP と zh-CN Quarkus 翻訳の作者による削除を保持したうえで、コントリビューターの Quarkus handling ブランチを取り込んだ。
- ECC PR #1812 は stale PR #1310 から有用な Django reviewer、Django build resolver、Django Celery guidance を回収した。メンテナー所有ブランチ、ソース PR へのクレジット、カタログ同期、完全なローカル/リモート検証付き。
- ECC PR #1813 は #1325、#1414、#1478、#1504、#1603 の回収元マッピングで stale PR salvage ledger を拡張し、それらの有用な stale コントリビューションが後続 maintainer PR で既に保持されていることを確認した。
- ECC PR #1815 は有用な stale #1304 コスト追跡と #1232 skill-scout 作業を現行 command/skill 規約と現行カタログ同期、完全なローカル/リモート検証付きで回収した。
- ECC PR #1816 は有用な stale #1659 フロントエンド設計ガイダンスを正規 ECC skill レイアウトに回収した。公式 Anthropic `frontend-design` skill は外部由来のままというガードレールを保持する。
- ECC PR #1817 は有用な stale #1658 code-reviewer 偽陽性ガードレールを回収した。HIGH/CRITICAL finding 向けの証明ゲート、一般的な偽陽性除外、回帰テストを追加した。
- ECC PR #1818 は 5月12日 stale-salvage ギャップパスを記録し、既存作業、スキップ作業、翻訳者/手動レビュー残件を分類した。

## 運用ルール (Operating Rules)

- 公開 PR と issue を 20 未満に保ち、リリースレーンではゼロを優先する。
- 各 GA readiness バッチ後に harness audit 80/80 と observability readiness 21/21 を維持する。
- GitHub release、npm/package state、billing state、plugin submission surface が新しい証拠で検証されるまで、リリースやソーシャル告知を公開しない。
- クローズ済み stale PR を破棄済みとみなさない。各クリーンアップバッチに salvage パスを組み合わせ、クローズされた diff を調査し、有用な互換作業を maintainer 所有 branch に移植し、ソース PR にクレジットを付ける。
- この workspace ではプロジェクトステータス更新が無効のため、プロジェクトレベル更新には Linear project documents/comments を使う。レーンに永続的な実行オーナーが必要なときは issue を作成または更新する。

## プロンプトからアーティファクトへの実行チェックリスト (Prompt-To-Artifact Execution Checklist)

この表は、長いオペレータープロンプトを具体的なアーティファクトに結びつけ続ける。証拠列が存在し、新たに検証されていない限り、ステータスは完了ではない。

| Prompt 要件 (Prompt requirement) | 必須 artifact または gate (Required artifact or gate) | 現在の証拠 (Current evidence) | ステータス (Status) |
| --- | --- | --- | --- |
| 公開 PR を 20 未満に保つ (Keep public PRs below 20) | リポジトリファミリー PR 再確認 (Repo-family PR recheck) | 2026-05-19 後半の platform audit 時点で、ECC PR #2013、ECC-Tools PR #79、JARVIS PR #15、JARVIS PR #16 のマージ後、`ECC`、`AgentShield`、`JARVIS`、`ECC-Tools/ECC-Tools`、`ECC-Tools/ECC-website` 全体でオープン PR 0 件 | 完了 |
| 公開 issue を 20 未満に保つ (Keep public issues below 20) | リポジトリファミリー issue 再確認 (Repo-family issue recheck) | 2026-05-19 のライブ platform audit 更新後、`ECC`、`AgentShield`、`JARVIS`、`ECC-Tools/ECC-Tools`、`ECC-Tools/ECC-website` 全体でオープン issue 0 件 | 完了 |
| リポジトリ discussion を管理する (Manage repository discussions) | リポジトリファミリー discussion 再確認および response playbook (Repo-family discussion recheck plus response playbook) | platform audit は discussion maintainer-touch gap 0 件、accepted answer 欠落の回答可能 Q&A 0 件を報告。trunk は #2003 が maintainer response でルーティングされた後、discussion 合計 59 件。`docs/architecture/discussion-response-playbook.md` はサポート、メンテナー調整、stale/concluded、リリース、informational、security-sensitive の response path を区別する | 完了 |
| PR discussion を管理する (Manage PR discussions) | PR レビュー/コメントのクロージャと merge/close 状態 | ECC #1990-#2013 が harness audit、canonical identity、release video suite、growth outreach、evidence refresh、visual QA、suite-count、owner-approval packet、owner-approval dashboard gate、Linear readiness evidence、supply-chain evidence gate、per-project Claude Code adapter、continuous-learning project-registry hygiene、GateGuard quoted git introspection、deterministic release-approval gate バッチまでマージ済み。ECC-Tools #79 と JARVIS #15/#16 もマージ済み。追跡対象のオープン PR は残っていない | 完了 |
| 有用な stale 作業を salvage する (Salvage useful stale work) | `docs/stale-pr-salvage-ledger.md` および `docs/legacy-artifact-inventory.md` | ledger は回収済み、置き換え済み、スキップ済み、手動レビュー残件を記録。#1815-#1818 が cost tracking、skill scout、frontend design guidance、code-reviewer false-positive guardrails、5月12日ギャップパスを追加。#1687、#1609、#1563、#1564、#1565 の localization 残件は言語オーナー review のため Linear ITO-55 に紐づけられ、自動 import がリリースをブロックするものは残っていない | 完了; リリース前に legacy scan を繰り返す |
| ECC 2.0 preview pack の準備完了 (ECC 2.0 preview pack ready) | リリースドキュメント、クイックスタート、公開 readiness、リリースノート (Release docs, quickstart, publication readiness, release notes) | `docs/releases/2.0.0-rc.1/` と readiness docs がリポジトリ内にある。5月19日/20日の証拠は queue-zero state、canonical ECC identity、release video suite、growth outreach pack、owner approval packet、local 2568-test suite、PR #2001 マージと GitHub Actions run `26102500291`、PR #2002 owner-approval dashboard gate refresh と GitHub Actions run `26103853507`、PR #2004 Linear readiness evidence sync と GitHub Actions run `26105012698`、PR #2008 supply-chain evidence gate CI run `26108473648`、post-PR #2006 main CI run `26109953093`、PR #2009 project-registry hygiene GitHub Actions run `26111313938`、post-PR #2009 main CI run `26111946778`、post-PR #2011 GateGuard main CI run `26113695068`、post-PR #2013 release-approval main CI run `26128749863`、post-PR #2019 main CI run `26135974576`、post-PR #2020 main CI run `26136949698`、ECC-Tools #91 main CI run `26137280847`、5月20日 operator dashboard、`owner-approval-packet-2026-05-19.md`、`release-approval-gate.js`、preview-pack smoke digest `eebb8a66c33e` を記録 | 最終リリース承認が必要 |
| Hermes 専用 skill の安全な取り込み (Hermes specialized skills included safely) | Hermes setup/import docs と sanitized skill surface | Hermes setup と import playbook は公開済み。secret はローカルに留める | 最終リリースレビューが必要 |
| 命名と rename readiness (Naming and rename readiness) | package/plugin/docs/social surface 横断の naming matrix | `docs/releases/2.0.0-rc.1/naming-and-publication-matrix.md` が現在の package、repo、Claude plugin、Codex plugin、OpenCode、npm availability 証拠を記録 | rc.1 では完了。rc.1 後の rename は今後の作業 |
| Claude と Codex plugin 公開 (Claude and Codex plugin publication) | 必須アーティファクトとステータスを含む連絡/提出パス | publication readiness、naming matrix、5月12日 dry-run 証拠が plugin validation、clean-checkout Claude tag/install smoke、Codex marketplace CLI 形状を文書化 | 実 tag/push と marketplace submission には明示的承認が必要 |
| 記事、tweet、告知 (Articles, tweets, and announcements) | X thread、LinkedIn copy、GitHub release copy、push checklist、partner/sponsor/talk pack | rc.1 release docs 配下にドラフト launch collateral と承認ゲート付き outreach copy が存在 | 投稿・送信前に URL 根拠の更新と人間による承認が必要 |
| AgentShield enterprise 反復 (AgentShield enterprise iteration) | Policy gates、SARIF、packs、provenance、corpus、HTML reports、exception lifecycle audit、baseline drift Action/CLI surfaces、evidence-pack redaction、harness adapter registry、editor-native Zed/VS Code adapter coverage、Dependabot alert closure、enterprise research roadmap、supply-chain hardened release path、CI-safe baseline fingerprints、corpus accuracy recommendations、remediation workflow phases、env proxy hijack corpus coverage、Mini Shai-Hulud full-campaign package IOCs、CI-provenance evidence packs、plugin-cache runtime-confidence triage、evidence-pack consumer readback、fleet-level evidence-pack routing、fleet review items、fleet review ticket payloads、checksum-backed policy export、checksum-verified policy promotion、policy promotion review items、package-manager hardening drift detection、npm age-gate guidance correction、workflow action-runtime pin refresh、package-manager hardening Action outputs、policy-promotion Action outputs、ECC-Tools による promotion Action output の hosted 消費、ECC-Tools オペレーター可視 promotion output 値、ECC-Tools hosted promotion judge 監査 trace | PRs #53、#55-#64、#67-#69、#78-#92、#94、#95 が test evidence 付きで マージ済み。ECC-Tools #76 が hosted security review で fleet-summary output を消費、#77 が hosted finding output に source evidence path を表示、#78 が fleet route を harness owner review にリンク。AgentShield #91 が branch-protection review と downstream promotion 向け `agentshield policy export` bundle を追加。AgentShield #92 が digest verification、tamper rejection、explicit pack selection、dry-run review、active policy 書き込み前の JSON output を備えた `agentshield policy promote` を追加。AgentShield #94 が Zed/VS Code adapter detection、`.zed/settings.json` と `.zed/tasks.json` scan discovery、`.zed/setup.mjs` AI-tool persistence IOC coverage を追加。AgentShield #95 が patched lockfile で `brace-expansion` Dependabot alert を解消し、マージ後オープン Dependabot alert 0 件。AgentShield commit `87aec47` が digest evidence、owner review、protected rollout PR handoff、runtime smoke testing 向け `reviewItems` を追加し、ローカル/リモート CI は成功。AgentShield commit `28d08c7` が plaintext registry credentials、lifecycle-script enablement、weak pnpm/Yarn release-age cooldown 向け package-manager hardening drift detection を追加し、ローカル/リモート CI は成功。AgentShield commit `659f569` が全 workflow action runtime pin を SHA-pinned checkout v6.0.2 と setup-node v6.4.0 に更新し、remote CI は成功、action-runtime deprecation annotation は残っていない。AgentShield commit `ee585cd` が unsupported npm age key を検出し、enforceable cooldown finding を pnpm/Yarn に残す形で npm release-age guidance を修正し、ローカル/リモート CI は成功。AgentShield commit `1124535` が registry credentials、lifecycle scripts、release-age gates 向け package-manager hardening status/count output と redacted job-summary section を公開し、ローカル/リモート CI は成功。AgentShield commit `1593925` が owner approval、protected rollout、runtime smoke 向け policy-promotion status/count/digest output と job-summary review items を公開し、同一 Action job が promoted policy で scan したとき runtime smoke verified とマーク。AgentShield commit `840952a` が Linear/operator-ready fleet review ticket payload を追加し、現行 Mini Shai-Hulud IOC breadcrumb を拡張、ローカル/リモート CI は成功。ECC-Tools commit `8658951` が policy-promotion Action output を hosted security review finding と Hosted Promotion Readiness scoring にルーティング。ECC-Tools commit `16c537f` が hosted security job comments/check-runs に policy-promotion status、pack、review item count、action-required count、digest を表示。ECC-Tools commit `05d4e82` が raw provider output なしで hosted promotion judge request fingerprint と allowed-citation count を表示。明示的なエンタープライズ需要が出るまで native PDF export は自己完結型 HTML と print-to-PDF を優先して延期。`docs/architecture/agentshield-enterprise-research-roadmap.md` に baseline drift、evidence-pack bundle、redaction、adapter-registry、supply-chain hardening、hashed baseline fingerprints、corpus accuracy recommendation、remediation workflow、env proxy hijack corpus、Mini Shai-Hulud full-campaign package-table、`ci-context.json` provenance、`plugin-cache` confidence、`evidence-pack inspect` readback、`evidence-pack fleet` routing、fleet `reviewItems`、fleet review ticket payloads、policy export、policy promotion、policy promotion `reviewItems`、package-manager hardening Action outputs、policy-promotion Action outputs、promotion Action output の hosted 消費、オペレーター可視 promotion output 値、hosted promotion judge 監査 trace、editor-native adapter coverage、Dependabot closure がマージ済み | 次のワークフロー自動化は Marketplace/payment gate 後にライブ operator approval/readback を深めるべき |
| ECC Tools 次世代アプリ (ECC Tools next-level app) | Billing audit、PR checks、deep analyzer、sync backlog、evaluator/RAG corpus、hosted promotion judge audit trace、native-payments readback、ready Marketplace Pro target selection、selected-target announcement gate、billing gate env-file operator path、hosted observability、AgentShield fleet-summary hosted routing、hosted finding evidence paths、harness-route policy linking、policy-promotion Action-output hosted telemetry、operator-visible promotion output values | PR #26-#43 および #53-#93 が hosted 分析、hosted promotion readiness、model-judge 実行、native-payments 告知ゲート、AgentShield エビデンス消費、hosted 修復/Linear 同期、hosted observability 読み戻し、ready Marketplace Pro ターゲット選択、selected-target 公式告知ゲート、env-file オペレーター読み込み横断でテストエビデンス付きマージ済み。ECC-Tools #89 は Verify、Security Audit、Workers Builds 通過後 `512bca6` としてマージ。2026-05-20 production Wrangler OAuth readback は webhook provenance 付き ready-like Marketplace Pro レコードを検出し、両キーファミリーを持つターゲットを選択、login を出力せず blocker 0 件を報告。ECC-Tools #90 は Verify、Security Audit、Workers Builds 通過後 `16a5bb3` としてマージ。production preflight は生 login なしで `/api/billing/readiness?selectReadyTarget=1` を要求。ECC-Tools #91 は無視されるローカル billing credential 向け `--env-file` サポートと sentinel no-secret/no-login 出力テスト付き `72119a1` としてマージ。ECC-Tools #92 は非破壊的 `INTERNAL_OPERATOR_API_SECRET` path を `api.ecc.tools` にデプロイし `18d8019` としてマージ。2026-05-20 live selected-target gate は required action 0、blocker 0 で `announcementGateReady: true` を返却。ECC-Tools #93 は live billing エビデンスを app launch checklist と roadmap に記録し `d3d62df` としてマージ | ローンチ直前に KV readback と selected-target announcement gate を繰り返す。native-payments コピーは最終リリース、plugin、live URL、owner-approval gate の内側に置く |
| GitGuardian/Dependabot/CodeRabbit 型チェック (GitGuardian/Dependabot/CodeRabbit-style checks) | 非ブロッキング taxonomy、決定論的 follow-up チェック、ローカル supply-chain ゲート (Non-blocking taxonomy, deterministic follow-up checks, and local supply-chain gates) | ECC-Tools risk taxonomy check と follow-up signal が マージ済み。Skill Quality、Deep Analyzer Evidence、Analyzer Corpus Evidence、RAG/Evaluator Evidence、PR Review/Salvage Evidence、AgentShield evidence-pack evidence を含む。#1846 が npm registry signature gate を追加。#1848 が supply-chain incident-response playbook と `pull_request_target` cache-poisoning validator guard を追加。#1851 が privileged checkout credential-persistence guard を追加。AgentShield #78、JARVIS #13、ECC-Tools #53 が trunk 外で同一強化を適用 | 現在の supply-chain gate は完了。より深い hosted review feature は今後の作業 |
| Harness 非依存学習システム (Harness-agnostic learning system) | Audit、adapter matrix、observability、traces、promotion loop | audit/adapters/observability gate に加え、`docs/architecture/evaluator-rag-prototype.md`、`examples/evaluator-rag-prototype/`、ECC-Tools PR #40 が read-only stale-salvage、billing-readiness、CI-failure-diagnosis、harness-config-quality、AgentShield policy-exception、skill-quality evidence、deep-analyzer evidence、RAG/evaluator comparison シナリオを trace、report、playbook、verifier、predictive-check artifact 付きで定義。ECC-Tools PRs #68-#72 がその corpus を cached hosted-output scoring、ranked retrieval candidates、model prompt seed、fail-closed hosted model-judge request contract、strict hosted-evidence gate 背後の opt-in live model execution を備えた deterministic PR check-run gate に変換 | 決定論的 hosted PR チェック、キャッシュ出力スコアリング、検索計画、判定契約、ゲート付きモデル実行を統合済み |
| Linear roadmap の詳細化 (Linear roadmap is detailed) | Linear project document/comments と repo mirror | repo mirror が存在し issue creation が再び動作。5月19日 sync が post-PR #2002 document `ecc-may-19-post-pr-2002-sync-64cef8f668e0`、project comment `a6411e3a-8c8e-4a58-adba-687e77d4c543`、ITO-44/47/48/49/51/54/56 issue comment、ITO-47/48/49/51/54/56 の進行中状態を追加。late-pass バッチが document `ecc-may-19-late-queue-zero-and-release-gate-sync-1c26f65e6b3f`、project comment `d42bf0e2-7a8e-4934-9f3f-e281498ee805`、PR #2013、ECC-Tools #79、JARVIS #15/#16 向け ITO-44/50/54/56/61 comment を追加。workspace ではプロジェクトステータス更新が無効 | 各重大なマージバッチ後の定期的なドキュメント/コメント更新が必要 |
| フロー分離と進捗追跡 (Flow separation and progress tracking) | オーナーアーティファクトと更新頻度を持つ flow lane (Flow lanes with owner artifacts and update cadence) | 本 roadmap が下記 lane を定義し、`docs/architecture/progress-sync-contract.md` が GitHub/Linear/handoff/roadmap sync を readiness gate の一部にする | アクティブ |
| リアルタイム Linear sync (Realtime Linear sync) | レーン更新向け project document/comment と issue comment | ECC-Tools #39 が延期フォローアップバックログ項目向けオプトイン Linear API 同期を実装。ECC-Tools #54 が draft PR シェルを開かない場合にコピー可能 PR 草案をそのバックログに追加。`docs/architecture/progress-sync-contract.md` がローカルファイルベース realtime 境界を定義。5月18日と5月19日の live connector comment がプロジェクトステータス更新無効返却後に ECC platform project とレーン issue に投稿 | hosted issue 同期には workspace 設定/product rollout が必要 |
| 自己利用向け Observability (Observability for self-use) | ローカル readiness gate、trace、ステータススナップショット、HUD/status contract、risk ledger、progress-sync contract | `npm run observability:ready` が 21/21 を報告 | ローカル gate では完了 |
| 適切なリリースと通知 (Proper release and notifications) | リリース tag、npm publish 状態、plugin 状態、ソーシャル投稿 | publication readiness gate が 5月12日 dry-run と 5月13日 readiness エビデンス付きで存在 | 未完了。承認/ライブ URL が必要 |

## 実行レーンと追跡契約 (Execution Lanes And Tracking Contract)

Linear issue キャパシティが解放されるまで、本ドキュメントが永続的な実行台帳となり、Linear にはプロジェクトステータス更新のみが届く。同期契約は `docs/architecture/progress-sync-contract.md` にある。キャパシティが利用可能になったら、下記各レーンはリポジトリエビデンスとマージコミットにリンクした少数の Linear issue に分割すべきである。

| レーン (Lane) | 正 (Source of truth) | 次に追跡する artifact (Next tracked artifact) | 更新頻度 (Update cadence) |
| --- | --- | --- | --- |
| キュー衛生と salvage (Queue hygiene and salvage) | GitHub PR/issue 状態、salvage ledger | 将来の stale クロージャごとに ledger エントリを追記 | 各クリーンアップバッチ |
| リリースと公開 (Release and publication) | rc.1 release docs、publication readiness doc | naming matrix と plugin 提出/連絡チェックリスト | いかなる tag 前 |
| Harness OS コア (Harness OS core) | Audit、adapter matrix、observability docs、`ecc2/` | HUD/session-control acceptance spec | GA まで週次 |
| Evaluation と RAG (Evaluation and RAG) | Reference-set validation、harness audit、traces、ECC-Tools corpus | read-only evaluator/RAG prototype に加え、stale-salvage、billing-readiness、CI-failure-diagnosis、harness-config-quality、AgentShield policy-exception、skill-quality evidence、deep-analyzer evidence、RAG/evaluator comparison fixture。ECC-Tools #68 が corpus を hosted promotion readiness check-run として公開、#69 が同一 corpus に対して cached hosted job output をスコアリング、#70 が ranked retrieval candidate と model prompt seed を出力、#71 が fail-closed hosted model-judge request contract を追加、#72 が明示的に有効化され hosted retrieval citation で裏付けられた場合のみ judge を実行。ECC-Tools `16c537f` が hosted security comments/checks に policy-promotion Action output value を表示。ECC-Tools `05d4e82` が request fingerprint と allowed-citation count 付き hosted model-judge audit trace を追加 | webhook provenance 付き Marketplace Pro billing-state の検証 |
| AgentShield enterprise (AgentShield enterprise) | AgentShield PR evidence と roadmap note | #88 で evidence-pack inspect/readback がリリースされた後、#89 で fleet routing がマージ済み。#90 が fleet `reviewItems` を出力。#91 が checksum-backed policy bundle をエクスポート。#92 が checksum-verified policy を active policy file に昇格。#94 が Zed と VS Code adapter detection、Zed project scan discovery、`.zed/setup.mjs` persistence IOC coverage を追加。#95 が `brace-expansion` Dependabot alert をクローズし、マージ後 open alert 0 件。AgentShield `87aec47` が policy promotion `reviewItems` を追加。`28d08c7` が package-manager hardening drift detection を追加。`659f569` が workflow action runtime pin を更新。`ee585cd` が unsupported npm release-age guidance を修正し、enforceable cooldown finding を pnpm/Yarn に残す。`1124535` が CI/hosted routing 向け package-manager hardening Action output を公開。`1593925` が policy-promotion Action output と runtime-smoke job-summary evidence を公開。`840952a` が fleet review ticket payload と現行 Mini Shai-Hulud IOC breadcrumb を追加。ECC-Tools #76 が fleet summary を消費、#77 が hosted finding に source evidence path を表示、#78 が fleet route を harness owner にリンク。ECC-Tools `8658951` が policy-promotion Action output を消費。ECC-Tools `16c537f` が operator-visible output value を表示 | Marketplace/payment gate 後に live operator approval/readback を深める |
| ECC Tools アプリ (ECC Tools app) | ECC-Tools PR evidence、billing audit、risk taxonomy、evaluator/RAG corpus | ECC-Tools #53 が supply-chain workflow hardening branch を公開。#54 が Linear/project backlog でコピー可能 PR 草案を追跡。#55 が analysis-depth readiness を分類。#56 が hosted execution plan を公開。#57 が最初の hosted CI diagnostics job を実行。#58 が hosted security evidence review job を実行。#59 が hosted harness compatibility audit を実行。#60 が hosted reference-set evaluation を実行。#61 が hosted AI routing/cost review を実行。#62 が hosted team backlog routing を実行。#63 が hosted depth-plan check-run を公開。#64 が PR comment から hosted job を dispatch。#65 が hosted result history/check-run を永続化。#66 が PR comment から hosted job status を公開。#67 が depth-plan recommendation を cache-aware に。#68 が evaluator/RAG corpus から hosted promotion readiness を公開。#69 が cached hosted job output をその corpus に対してスコアリング。#70 が ranked retrieval candidate と model prompt seed を出力。#71 が live model call なしで gated `hosted-promotion-judge.v1` contract を出力。#72 が hosted-evidence と strict JSON/citation gate 背後の opt-in live model-judge execution を追加。#73 が billing readiness に fail-closed native-payments `announcementGate` を追加。#74 が operator verification 向け `npm run billing:announcement-gate` を追加。#75 が live Marketplace readback 向け billing announcement gate を厳格化。#76 が AgentShield fleet-summary evidence を hosted security finding にルーティング。#77 が hosted finding output に source evidence path を追加。#78 が AgentShield fleet target path を hosted harness owner finding にリンク。`8658951` が AgentShield policy-promotion Action output を hosted security review と promotion readiness にルーティング。`16c537f` が hosted security comments/checks に policy-promotion status/pack/count/digest value を表示。`05d4e82` が hosted promotion judge request fingerprint と allowed-citation audit trace を表示。`91a441b` が required readback input 向け billing announcement preflight output を追加。`eb69412` が initial production readback state を記録。`95d0bec` が aggregate `billing:kv-readback` evidence を追加。`2859678` が billing readiness に Marketplace webhook provenance を要求。`42653f9` が live aggregate production count 付き Wrangler OAuth readback を追加。`632e059` が exact Marketplace test account 向け sanitized target-account billing readback を追加。ECC-Tools #89 が selected-ready-target KV readback を追加。ECC-Tools #90 が raw login input なしで selected-target official announcement gating を追加。ECC-Tools #91 が secret や login を出力せず ignored local billing credentials 向け `--env-file` support を追加 | エクスポート済み env または無視される `--env-file` 経由で local/internal `INTERNAL_API_SECRET` bearer-token path を取得またはローテーションし、ライブ selected-target billing announcement gate を実行 |
| Linear 進捗 (Linear progress) | Linear プロジェクトステータス更新、`docs/architecture/progress-sync-contract.md`、生成 `operator:dashboard` 出力、本ミラー | キュー/エビデンス/欠落 gate を含むステータス更新 | 各重要マージバッチ |

プロジェクトステータス更新には常に以下を含める:

1. 現在の公開 PR と issue 件数。
2. 前回更新以降のマージ済み証拠。
3. 理由付きの延期またはブロック項目。
4. 次の 1〜2 実装スライス。
5. まだ証拠に裏付けられていないリリースまたは公開ゲート。

## 参照プレッシャー (Reference Pressure)

GA roadmap は次の参照 surface に基づく:

- worktree ネイティブな並列 agent UX、review loop、workspace preset 向けに `stablyai/orca` と `superset-sh/superset`。
- ターミナル/worktree 多重化、session グループ化、lifecycle hook 向けに `standardagents/dmux` と `aidenybai/ghast`。
- 常時可視の status、tool、agent、todo、context telemetry 向けに `jarrodwatts/claude-hud`。
- 評価駆動の harness 改善、trace、playbook、promotion loop 向けに `stanford-iris-lab/meta-harness` と `greyhaven-ai/autocontext`。
- オペレーター shell、gateway、memory、skill、マルチプラットフォーム command pattern 向けに `NousResearch/hermes-agent`。
- adapter 期待値向けに `anthropics/claude-code`、アクティブな `sst/opencode` / `anomalyco/opencode`、Zed、Codex、Cursor、Gemini、ターミナル専用 workflow。

この参照作業の成果は、第二の戦略メモではなく、具体的な ECC の差分であるべきである。

## マイルストーン (Milestones)

### 1. GA リリース、命名、Plugin 公開 Readiness (GA Release, Naming, And Plugin Publication Readiness)

目標 (Target): 2026-05-24

受け入れ条件 (Acceptance):

- naming matrix が製品名、npm package、Claude plugin、Codex plugin、OpenCode package、marketplace metadata、docs、移行コピーをカバーする。
- GitHub release、npm dist-tag、plugin publication、announcement gate が新しい command evidence にマッピングされる。
- release notes、migration guide、known issues、quickstart、X thread、LinkedIn post、GitHub release copy は準備済みだが、release URL が存在する前に投稿しない。
- Claude と Codex の plugin publication/contact path が owner、必須アーティファクト、submission status 付きで文書化される。

### 2. Harness Adapter 準拠マトリクスと Scorecard オンプランプ (Harness Adapter Compliance Matrix And Scorecard Onramp)

目標 (Target): 2026-05-31

受け入れ条件 (Acceptance):

- adapter matrix が Claude Code、Codex、OpenCode、Cursor、Gemini、Zed-adjacent surface、dmux、Orca、Superset、Ghast、terminal-only use をカバーする。
- 各 adapter にサポート対象 asset、非サポート surface、install path、検証コマンド、リスクメモがある。
- harness audit は 80/80 を維持し、チームが scorecard を使う方法を説明する公開オンプランプを得る。
- reference finding が具体的な adapter、observability、オペレーター surface 差分に変換される。

### 3. ローカル Observability、HUD/Status、Session コントロールプレーン (Local Observability, HUD/Status, And Session Control Plane)

目標 (Target): 2026-06-07

受け入れ条件 (Acceptance):

- observability readiness は 21/21 を維持し、JSONL trace、status snapshot、risk ledger、exportable handoff contract で裏付けられる。
- HUD/status model が context、tool call、active agent、todo、check、cost、risk、queue state をカバーする。
- worktree/session control が create、resume、status、stop、diff、PR、merge queue、conflict queue をカバーする。
- Linear/GitHub/handoff sync model がリアルタイム進捗追跡に十分明示的である。

### 4. 自己改善 Harness 評価ループ (Self-Improving Harness Evaluation Loop)

目標 (Target): 2026-06-10

受け入れ条件 (Acceptance):

- scenario spec、verifier contract、trace、playbook、regression gate が文書化され、少なくとも 1 つの read-only prototype が存在する。
- ループが observation、proposal、verification、promotion を分離する。
- チームと個人の setup を、config を盲目的に変更せずにスコアリング・改善できる。
- RAG/reference-set design が vetted ECC pattern、team history、CI failure、diff、review outcome、harness config quality をカバーする。

### 5. AgentShield Enterprise セキュリティプラットフォーム (AgentShield Enterprise Security Platform)

目標 (Target): 2026-06-14

受け入れ条件 (Acceptance):

- org baseline、exception、owner、expiration、severity、audit trail、期限切れ間近の可視性、期限切れ exception の強制向けの正式な policy schema と evaluation output が存在する。
- SARIF/code-scanning output が実装・テスト済みである。
- GitHub Action policy gate が branch-protection と CI evidence 向けに organization policy status と violation count を公開する。
- OSS、team、enterprise、regulated、high-risk hooks/MCP、CI enforcement 向け policy pack が定義される。
- supply-chain intelligence が MCP package provenance をカバーし、npm/pip reputation、CVE、typosquat、dependency risk 向け extension path を持つ。
- prompt-injection corpus と regression benchmark が category-level coverage と regression-gate output 付きで継続的 rule hardening に備えている。
- enterprise report に JSON と、risk posture、priority finding、category exposure、policy-exception lifecycle evidence を terminal/CI summary に含む自己完結型 HTML エグゼクティブ出力がある。
- 自己完結型 HTML レポートと browser print-to-PDF path の代わりに生成 PDF ファイルを要求するエンタープライズ/コンプライアンス workflow がない限り、native PDF export は GA blocker ではない。

### 6. ECC Tools Billing、Deep Analysis、PR Checks、Linear Sync (ECC Tools Billing, Deep Analysis, PR Checks, And Linear Sync)

目標 (Target): 2026-06-21

受け入れ条件 (Acceptance):

- native GitHub Marketplace billing announcement が検証済み実装と docs で裏付けられる。
- internal billing readiness audit が plan limit、seat、entitlement mapping、Marketplace plan shape、subscription state、overage hook、failure mode をカバーする。
- deep analyzer が diff pattern、CI/CD workflow、dependency/security surface、PR review behavior、failure history、harness config、skill quality、dedicated analyzer corpus evidence、co-located analyzer reference set、PR review/stale-salvage evidence、RAG/evaluator comparison、reference-set validation をカバーする。
- PR check suite taxonomy に Security Evidence、Harness Drift、Install Manifest Integrity、CI/CD Recommendation、Cost/Token Risk、Reference Set Validation、Deep Analyzer Evidence、RAG/Evaluator Evidence、PR Review/Salvage Evidence、Skill Quality、Agent Config Review が含まれる。
- Evaluator/RAG billing readiness fixture `examples/evaluator-rag-prototype/billing-marketplace-readiness/` が、launch copy がそれらの claim を live として扱う前に、Marketplace、App、subscription、seat、entitlement、plan language 向け read-only claim-verification path を記録する。
- cost/token-risk 予測型 follow-up が budget evidence 欠落時に AI routing、model-call、usage、quota、budget 変更をフラグする。
- reference-set validation フォローアップが eval、golden trace、benchmark、維持済み reference-set エビデンスを欠く analyzer、skill、agent、command、harness-guidance 変更をフラグする。
- deep-analyzer フォローアップが analyzer corpus、snapshot、fixture、benchmark エビデンスを欠くリポジトリ、コミット、アーキテクチャ、パターン、分析パイプライン変更をフラグする。
- analyzer corpus エビデンスに現行アーキテクチャとコミット analyzer 出力向け維持済み fixture と test、および同一配置 `src/analyzers/{fixtures,goldens,reference-sets,benchmarks,evals}/` エビデンスパスが含まれる。
- RAG/evaluator フォローアップが reference-set comparison、golden trace、benchmark、fixture、eval-run エビデンスを欠く retrieval、embedding、ranking、evaluator 変更をフラグする。
- Evaluator/RAG corpus contract が local prototype シナリオを stale-PR salvage、billing readiness、CI failure diagnosis、harness config quality、AgentShield policy exception、skill-quality evidence、deep-analyzer evidence、RAG/evaluator comparison 向け ECC-Tools fixture と test にミラーする。
- PR review/stale-salvage follow-up が stale-salvage fixture、reviewer-thread case、reopen-flow reference evidence を欠く review、triage、stale-closure、pull-request automation 変更をフラグする。
- PR analysis comment が requested change、未解決または古い review thread、承認欠落向け review follow-up signal を要約する。
- CI failure-mode 予測型 follow-up が failure fixture、captured log、troubleshooting note、dry-run evidence、regression coverage を欠く workflow と test-runner 変更をフラグする。
- harness-config quality 予測型 follow-up が audit、adapter matrix、cross-harness doc、compatibility regression evidence を欠く MCP、plugin、agent、hook、command、harness config 変更をフラグする。
- Linear sync が延期された backlog finding を GitHub を溢れさせず Linear issue にマッピングし、設定時は完全一致タイトルの Linear issue を作成または再利用し、credential や team configuration がない場合はスキップされた同期を報告する。
- Linear/project backlog 同期が `/ecc-tools followups sync-linear` を `open-pr-drafts` なしで使うときコピー可能 PR 草案を含み、stale-PR salvage 作業を余分な PR シェルを開かず追跡し続ける。
- フォローアップ生成が自動 GitHub オブジェクト作成に上限を設け、あふれた finding をコピー可能プロジェクト同期バックログに残す。

### 7. Legacy Audit と Stale 作業 Salvage クロージャ (Legacy Audit And Stale-Work Salvage Closure)

目標 (Target): 2026-06-15

受け入れ条件 (Acceptance):

- legacy directory と orphaned handoff が棚卸しされる。
- 各有用 artifact がマージ済み、Linear/プロジェクト追跡済み、salvage ブランチ、archive/no-action のいずれかにマークされる。
- workspace レベルの legacy repo はサニタイズ済みメンテナーブランチ経由でのみ発掘する。生コンテキスト、secret、個人パス、ローカル設定、非公開草案を一括インポートしない。
- stale PR salvage ポリシーが継続: まず stale/競合 PR をクローズし、salvage ledger 項目を記録し、帰属付きでメンテナーブランチに有用な互換コンテンツを移植する。
- #1687 localization 残件は盲目的な cherry-pick ではなく翻訳者/手動レビューのみで扱う。

## 次のエンジニアリングスライス (Next Engineering Slices)

1. `docs/architecture/agentshield-enterprise-research-roadmap.md` から AgentShield enterprise 制御プレーン シーケンスを継続する: PR #63
   が GitHub Action baseline 出力と job-summary エビデンスを公開。PR #64
   が `agentshield baseline write` 経由で第一級 baseline スナップショット作成を公開。PR #67
   が evidence-pack bundle を公開。PR #68 が evidence-pack redaction を強化。PR #69
   が multi-harness adapter registry を公開。PR #78 が現在の supply-chain incident class
   向け release workflow を強化。PR #79 が baseline/watch/remediation fingerprint を
   hashed evidence に移行し、新 baseline への raw evidence 書き込みを停止。PR #80
   が failed regression gate 向け prioritized corpus accuracy recommendation を追加。PR #81
   が ordered remediation workflow phase を追加。PR #82 が env proxy hijack と
   out-of-band exfiltration 向け corpus coverage を拡張。PRs #83-#85 が Mini Shai-Hulud IOC
   coverage と release-path supply-chain verification を強化。PR #86 が whitelisted
   `ci-context.json` workflow、commit、run、runtime provenance を evidence pack に追加。PR
   #87 が installed Claude plugin cache を active top-level runtime config（cached hook
   implementation 含む）から分類。PR #88 が下流コンシューマー向け `agentshield evidence-pack
   inspect` JSON/text readback を追加。PR #89 が複数 inspected bundle 横断の
   `agentshield evidence-pack fleet` summary/routing を追加。ECC-Tools PRs #42/#43 が
   evidence pack をルーティングおよび認識。ECC-Tools PR #76 が hosted security review で
   fleet summary を消費。ECC-Tools PR #77 が hosted PR comment と check-run に source
   evidence path を表示。ECC-Tools PR #78 が AgentShield fleet target path を hosted harness
   owner finding にリンク。AgentShield PR #90 が source evidence path と オーナー向け
   recommendation 付き fleet `reviewItems` を出力。AgentShield PR #91 が branch-protection
   review と downstream policy promotion 向け checksum-backed policy bundle をエクスポート。AgentShield
   PR #92 が dry-run JSON review 付きで checksum-verified policy bundle を active policy file
   に昇格。AgentShield commit `87aec47` が digest evidence、owner-review、protected-rollout PR
   handoff、runtime smoke testing 向け policy promotion `reviewItems` を追加。AgentShield
   commit `28d08c7` が package-manager hardening drift detection を追加。AgentShield commit
   `659f569` が current SHA-pinned v6 action で action-runtime deprecation warning を解消。AgentShield
   commit `ee585cd` が unsupported npm age key を finding とし、enforceable cooldown finding を
   pnpm/Yarn に残す形で npm release-age guidance を修正。AgentShield commit `1124535` が
   registry credential、lifecycle-script drift、release-age gate drift 向け package-manager
   hardening Action output を公開。AgentShield commit `1593925` が owner approval、protected
   rollout、digest evidence、runtime-smoke review item 向け policy-promotion Action output を
   公開。ECC-Tools commit `8658951` が hosted security review と Hosted Promotion Readiness
   scoring でそれらの output を消費。ECC-Tools commit `16c537f` が hosted security
   comments/check-runs に promotion status、pack、review item count、remaining action count、digest
   を表示。AgentShield commit `840952a` が Linear/operator-ready fleet review ticket payload を
   追加し、現行 Mini Shai-Hulud IOC breadcrumb を拡張。ローカル/リモート CI は成功。AgentShield
   commit `4e36aab` が拡張 Mini Shai-Hulud refresh 後の CI package install を強化。CI、Test
   GitHub Action、Self-Scan、Dependabot Update workflow は成功。ECC-Tools commit `05d4e82` が
   raw provider output を公開せず、deterministic request fingerprint と allowed-citation count
   付き hosted promotion judge audit trace を追加。ECC-Tools commit `91a441b` が privileged API
   call 前に Marketplace readback input を確認する billing announcement preflight command を追加。ECC-Tools
   commit `2859678` が native-payments 告知 readiness が通過する前に billing-state に Marketplace
   webhook provenance を要求。ECC-Tools commit `42653f9` が Wrangler OAuth KV readback を追加し、
   現在の blocker は Cloudflare read access ではなく webhook provenance 付き ready-like
   Marketplace Pro billing-state record の欠如であることを確認。ECC-Tools commit `632e059` が
   sanitized target-account readback を追加。PRs #89/#90/#91 が最終 operator path を
   selected-target readback、selected-target announcement gating、account login や raw KV key
   name を出力しない ignored env-file credential loading に移行。ECC-Tools PR #79 が billing
   announcement gate アカウント出力をマスキング。PR #80 が runtime receipt に障害理由を要求。PRs
   #81/#82 が AgentShield fleet approval ID を保持・表示。PR #83 が外部 ID により Linear
   フォローアップ同期をべき等に。PR #84 が hosted AgentShield 修復項目を Linear に同期。PR #85
   が budget-blocked outcome を含む hosted job observability event を出力。PRs #86/#87 がそれらの
   event を hosted status comment と hosted depth-plan check-run に読み戻す。PR #88 が operator
   dashboard 向け authenticated hosted observability API readback を公開。
2. `npm run billing:announcement-gate -- --preflight
   --select-ready-target` を実行する。ローカル bearer token が無視されるオペレーターファイルに
   保存されている場合は `--env-file /path/to/ecc-tools.env` を追加し、続けて `--preflight` なしで
   同一コマンドを実行し、native GitHub payments 告知の前に `announcementGate.ready === true` を
   要求する。
3. workspace issue キャパシティが解放されるか Linear workspace がアップグレードされた後、マージ済み
   Linear バックログ同期パスを有効化・設定し、PR 草案 salvage 項目が期待プロジェクトに着地することを
   検証する。
4. より深い hosted retrieval、vector storage、automated check-run promotion を追加する前に、
   ECC-Tools evaluator/RAG corpus を promotion gate として使う。
