# ECC オペレーター準備ダッシュボード (ECC Operator Readiness Dashboard)

このダッシュボードは `npm run operator:dashboard` により生成されます。オペレーター向けスナップショットであり、リリース承認ではありません。

生成日時 (Generated): 2026-05-20T03:14:39.338Z
コミット (Commit): 66733b511b70cf1cb501e8a3298b1cbd9968a9a0
ステータス (Status): 作業残あり (work remaining)

## 現状ステータス (Current Status)

| 領域 (Area) | ステータス (Status) | エビデンス (Evidence) |
| --- | --- | --- |
| PR キュー (PR queue) | 最新 (Current) | 追跡 repo 全体で open PR 0 件 |
| Issue キュー (Issue queue) | 最新 (Current) | 追跡 repo 全体で open issue 0 件 |
| Discussions | 最新 (Current) | maintainer touch 必要 0 件; accepted 回答欠落 0 件 |
| ローカル worktree (Local worktree) | 最新 (Current) | blocking dirty file 0 件; ignored dirty entry 0 件 |
| ダッシュボード生成 (Dashboard generation) | 最新 (Current) | platform audit ready: true; GitHub skipped: false |
| 公開 (Publication) | 未完了 (Not complete) | release、npm、plugin、billing、announcement ゲートは下記で追跡 |

## グロースベースライン (Growth Baseline)

| 指標 (Metric) | 現在 (Current) | 目標 (Target) | ギャップ (Gap) |
| --- | ---: | ---: | ---: |
| MRR | $1,728/mo | $10,000/mo | $8,272/mo |

グロースレーン: GitHub Sponsors と OSS パートナースポンサー; ECC Tools Pro サブスクリプション; コンサルティングと実装契約; トーク、ポッドキャスト、カンファレンスデモ、パートナーウェビナー。

## プロンプトから成果物へのチェックリスト (Prompt-To-Artifact Checklist)

| 目的要件 (Objective requirement) | 成果物またはゲート (Artifact or gate) | ステータス (Status) | エビデンス (Evidence) | ギャップ (Gap) |
| --- | --- | --- | --- | --- |
| 公開 PR を 20 未満に保つ (Keep public PRs below 20) | `scripts/platform-audit.js` live GitHub sweep と owner 横断 queue cleanup ledger | 最新 (current) | 5 追跡 repo で open PR 0 件; cleanup 後 owner 横断 open PR 0 件 | リリース前に platform:audit と owner 横断 gh search を繰り返す |
| 公開 issue を 20 未満に保つ (Keep public issues below 20) | `scripts/platform-audit.js` live GitHub sweep と owner 横断 queue cleanup ledger | 最新 (current) | 5 追跡 repo で open issue 0 件; cleanup 後 owner 横断 open issue 0 件 | リリース前に platform:audit と owner 横断 gh search を繰り返す |
| リポジトリ discussion に応答・管理 (Respond and manage repository discussions) | `scripts/platform-audit.js` discussion summary | 最新 (current) | maintainer touch 必要 0 件; accepted 回答欠落の回答可能 discussion 0 件 | リリース前に繰り返す |
| ITO-44 完了ダッシュボードを反復可能コマンド化 (Build ITO-44 completion dashboard into a repeatable command) | `npm run operator:dashboard` | 完了 (complete) | operator:dashboard package script が存在 | 生成ダッシュボードを publication evidence に添付し続ける |
| ECC 2.0 preview pack 準備完了 (ECC 2.0 preview pack ready) | `docs/releases/2.0.0-rc.1/preview-pack-manifest.md` | 最新 (current) | preview pack manifest と決定論的 smoke gate が in-tree | 公開前に clean-checkout preview-pack smoke を繰り返す |
| Hermes 専用 skill を安全に含める (Include Hermes specialized skills safely) | `docs/HERMES-SETUP.md` と `skills/hermes-imports/SKILL.md` | 最新 (current) | Hermes setup/import artifact は preview-pack smoke でカバー | リリースレビュー前に preview-pack smoke を繰り返す |
| 命名変更、Claude plugin、Codex plugin パス準備 (Prepare name-change, Claude plugin, and Codex plugin paths) | naming-and-publication-matrix、release-name-plugin-publication checklist、publication-readiness | 進行中 (in_progress) | naming matrix、release publication checklist、plugin readiness gate が存在 | 実 tag/push、marketplace submission、最終チャネル選択は承認ゲートのまま |
| リリースノート、記事、tweet、プッシュ通知準備 (Prepare release notes, articles, tweets, and push notifications) | `docs/releases/2.0.0-rc.1/` 配下の social と release-copy ファイル | 進行中 (in_progress) | release notes、X thread、LinkedIn draft、URL ledger が存在 | 最終 live release/npm/plugin/billing URL と公開承認が未了 |
| 最終 owner 承認パケット準備 (Prepare final owner approval packet) | `docs/releases/2.0.0-rc.1/owner-approval-packet-2026-05-19.md` | 最新 (current) | owner approval packet が release、package、plugin、video、billing、social、outbound 判断をカバー | 公開または outbound 前に最終 release commit から owner 承認をレビュー |
| 第2フェーズ hypergrowth リリース command center 作成 (Create a second-phase hypergrowth release command center) | `docs/releases/2.0.0/ecc-2-hypergrowth-release-command-center.md` と 5月19日 evidence | 最新 (current) | 現行 MRR、目標 MRR、gap、release claim、video レーン、distribution plan、承認境界が in-tree | 公開ローンチ前に MRR、チャネル、承認状態変更のたび refresh |
| ECC 2.0 リリース video suite 制作 (Produce the ECC 2.0 release video suite) | `docs/releases/2.0.0-rc.1/video-suite-production.md` と `npm run release:video-suite` | 最新 (current) | video-suite gate ready: ソース asset 15/15、suite artifact 13/13、publish candidate 12/12、primary self-eval、5月19日 evidence で black-frame segment 0 | 最終 owner 承認、アップロード、公開 video URL は承認ゲートのまま |
| スポンサー、パートナー、コンサル、ポッドキャスト、トーク、Discussion コピー準備 (Prepare sponsor, partner, consulting, podcast, talk, and Discussion copy) | `docs/releases/2.0.0-rc.1/partner-sponsor-talks-pack.md` | 進行中 (in_progress) | sponsor outbound、platform partner DM、consulting intro、talk/podcast pitch、GitHub Discussion 告知、CTA hook、do-not-send gate が draft 済み | publication gate 後に最終 URL を置換し、outbound または個人アカウント投稿前に明示的承認 |
| AgentShield enterprise 反復を前進 (Advance AgentShield enterprise iteration) | AgentShield PR evidence と enterprise roadmap | 進行中 (in_progress) | AgentShield policy promotion `reviewItems` は `87aec47` で land; package-manager hardening drift detection は `28d08c7`; workflow action runtime pin は `659f569` で refresh; npm age-gate guidance は `ee585cd` で修正; package-manager hardening Action output は `1124535`; policy-promotion Action output と runtime-smoke job-summary evidence は `1593925`; fleet review ticket payload と現行 Mini Shai-Hulud IOC breadcrumb は `840952a`; ECC-Tools は `8658951` で消費、`16c537f` で operator 可読 status/pack/count/digest テレメトリ、hosted promotion judge audit trace は `05d4e82`; いずれも GA roadmap に mirror | Marketplace/payment gate 後に live operator approval/readback を深める |
| ECC Tools native payments と AI-native harness-agnostic app を前進 (Advance ECC Tools native payments and AI-native harness-agnostic app) | ECC Tools PR evidence、billing gate、hosted analysis lane | 進行中 (in_progress) | billing announcement gate、selected-target announcement gate、billing gate env-file operator path、non-breaking operator bearer path、hosted analysis lane、AgentShield fleet-summary 消費、hosted finding evidence path、harness-route policy linking、policy-promotion Action-output テレメトリ、operator 可視 promotion output、hosted promotion judge audit trace、billing announcement preflight、集約 production billing KV readback、Wrangler selected-target readback、target-account billing readback、provenance 付き Marketplace billing-state gate、サニタイズ済み Marketplace plan/action provenance 件数、ready Marketplace Pro target 選択、hosted team-learning feedback 制御、ECC-Tools Dependabot alert 修復が GA roadmap に mirror | ローンチ直前に KV readback と selected-target announcement gate を繰り返す; native-payments コピーは最終 release、plugin、URL、owner-approval gate の背後に置く |
| legacy 作業を監査・整理・紐付け (Audit, prune, or attach legacy work) | `docs/stale-pr-salvage-ledger.md` と legacy inventory | 最新 (current) | legacy salvage ledger と inventory は最新; すべての localization tail は手動 language-owner レビュー向け Linear ITO-55 に紐付け | リリース前に legacy scan を繰り返す |
| Linear roadmap 詳細化と進捗追跡同期 (Keep Linear roadmap detailed and progress tracking synchronized) | Linear project mirror と progress-sync contract | 最新 (current) | Linear live sync は 5月20日 Marketplace Pro release-gate コメント（ITO-61、ECC platform roadmap）と整合; progress-sync contract が file-backed work-items/status path を定義 | 各 significant merge batch 後に Linear/project status update と local work-items sync を繰り返す |
| 自己利用向け ECC 2.0 observability (Provide ECC 2.0 observability for self-use) | observability readiness gate | 完了 (complete) | observability:ready コマンドと readiness doc が存在 | runtime/dashboard 実装は release gate 後も継続可 |
| Mini Shai-Hulud/TanStack 保護ループを最新維持 (Keep Mini Shai-Hulud/TanStack protection loop current) | supply-chain watch、runbook、AgentShield package-manager hardening | 最新 (current) | スケジュール supply-chain watch が IOC/advisory-source refresh artifact を emit; ECC scanner が gh-token-monitor token-store persistence をカバー; AgentShield が既知 AI-tool persistence IOC、npm lifecycle/token drift、unsupported npm age-key drift、pnpm/Yarn cooldown drift を検出; current-head watch evidence と ITO-57 5月18日 Linear evidence 更新は最新 | 各 significant supply-chain batch 後に advisory/source refresh と Linear sync を繰り返す |

## 優先アクション (Top Actions)

- `naming-and-plugin-publication`: 実 tag/push、marketplace submission、最終チャネル選択は承認ゲートのまま
- `release-notes-and-notifications`: 最終 live release/npm/plugin/billing URL と公開承認が未了
- `partner-sponsor-talks-pack`: publication gate 後に最終 URL を置換し、outbound または個人アカウント投稿前に明示的承認
- `agentshield-enterprise-iteration`: Marketplace/payment gate 後に live operator approval/readback を深める
- `ecc-tools-next-level`: ローンチ直前に KV readback と selected-target announcement gate を繰り返す; native-payments コピーは最終 release、plugin、URL、owner-approval gate の背後に置く

## 次の作業順序 (Next Work Order)

1. 公開エビデンスを記録する前に、最終リリースコミットからこのダッシュボードを再生成する。
2. 最終リリースコミットから owner approval packet をレビューし、各公開・アウトバウンドレーンを承認・延期・ブロックする。
3. オーナー承認済み primary launch video 候補をレビューし、最終カットを選択し、承認後にアップロードし、公開 video URL をリリースパックに添付する。
4. partner/sponsor/talk pack の最終 release、npm、plugin、billing、video URL を置き換え、アウトバウンド前に明示的承認を得る。
5. 次の significant merge batch または advisory-source refresh 後に ITO-57 Linear/project ステータス同期を繰り返す。
6. ローンチ直前に KV readback と selected-target billing announcement gate を繰り返す。ネイティブペイメントコピーは最終 release、plugin、URL、owner-approval ゲートの背後に置く。
