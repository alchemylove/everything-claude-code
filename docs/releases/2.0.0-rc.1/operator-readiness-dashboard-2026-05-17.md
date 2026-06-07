# ECC オペレーター準備ダッシュボード (ECC Operator Readiness Dashboard)

このダッシュボードは `npm run operator:dashboard` により生成されます。オペレーター向けスナップショットであり、リリース承認ではありません。

生成日時 (Generated): 2026-05-18T01:50:19.099Z
コミット (Commit): 000df72d6b9c5b11feb11deef609911943b48424
ステータス (Status): 作業残あり (work remaining)

## 現状ステータス (Current Status)

| 領域 (Area) | ステータス (Status) | エビデンス (Evidence) |
| --- | --- | --- |
| PR キュー (PR queue) | 最新 (Current) | 追跡 repo 全体で open PR 0 件 |
| Issue キュー (Issue queue) | 最新 (Current) | 追跡 repo 全体で open issue 0 件 |
| Discussions | 最新 (Current) | maintainer touch 必要 0 件; accepted 回答欠落 0 件 |
| ローカル worktree (Local worktree) | 最新 (Current) | blocking dirty file 0 件; ignored dirty entry 1 件 |
| ダッシュボード生成 (Dashboard generation) | 最新 (Current) | platform audit ready: true; GitHub skipped: false |
| 公開 (Publication) | 未完了 (Not complete) | release、npm、plugin、billing、announcement ゲートは下記で追跡 |

## プロンプトから成果物へのチェックリスト (Prompt-To-Artifact Checklist)

| 目的要件 (Objective requirement) | 成果物またはゲート (Artifact or gate) | ステータス (Status) | エビデンス (Evidence) | ギャップ (Gap) |
| --- | --- | --- | --- | --- |
| 公開 PR を 20 未満に保つ (Keep public PRs below 20) | `scripts/platform-audit.js` live GitHub sweep | 最新 (current) | 5 追跡 repo で open PR 0 件 | リリース前に繰り返す |
| 公開 issue を 20 未満に保つ (Keep public issues below 20) | `scripts/platform-audit.js` live GitHub sweep | 最新 (current) | 5 追跡 repo で open issue 0 件 | リリース前に繰り返す |
| リポジトリ discussion に応答・管理 (Respond and manage repository discussions) | `scripts/platform-audit.js` discussion summary | 最新 (current) | maintainer touch 必要 0 件; accepted 回答欠落の回答可能 discussion 0 件 | リリース前に繰り返す |
| ITO-44 完了ダッシュボードを反復可能コマンド化 (Build ITO-44 completion dashboard into a repeatable command) | `npm run operator:dashboard` | 完了 (complete) | operator:dashboard package script が存在 | 生成ダッシュボードを publication evidence に添付し続ける |
| ECC 2.0 preview pack 準備完了 (ECC 2.0 preview pack ready) | `docs/releases/2.0.0-rc.1/preview-pack-manifest.md` | 最新 (current) | preview pack manifest と決定論的 smoke gate が in-tree | 公開前に clean-checkout preview-pack smoke を繰り返す |
| Hermes 専用 skill を安全に含める (Include Hermes specialized skills safely) | `docs/HERMES-SETUP.md` と `skills/hermes-imports/SKILL.md` | 最新 (current) | Hermes setup/import artifact は preview-pack smoke でカバー | リリースレビュー前に preview-pack smoke を繰り返す |
| 命名変更、Claude plugin、Codex plugin パス準備 (Prepare name-change, Claude plugin, and Codex plugin paths) | naming-and-publication-matrix と publication-readiness | 進行中 (in_progress) | naming matrix と plugin readiness gate が存在 | 実 tag/push、marketplace submission、最終チャネル選択は承認ゲートのまま |
| リリースノート、記事、tweet、プッシュ通知準備 (Prepare release notes, articles, tweets, and push notifications) | `docs/releases/2.0.0-rc.1/` 配下の social と release-copy ファイル | 進行中 (in_progress) | release notes、X thread、LinkedIn draft が存在 | URL 根拠の refresh と公開承認が未了 |
| AgentShield enterprise 反復を前進 (Advance AgentShield enterprise iteration) | AgentShield PR evidence と enterprise roadmap | 進行中 (in_progress) | AgentShield policy promotion `reviewItems` は `87aec47` で land; package-manager hardening drift detection は `28d08c7`; workflow action runtime pin は `659f569` で refresh; npm age-gate guidance は `ee585cd` で修正; package-manager hardening Action output は `1124535`; policy-promotion Action output と runtime-smoke job-summary evidence は `1593925`; ECC-Tools は `8658951` で消費、`16c537f` で operator 可読 status/pack/count/digest テレメトリ、hosted promotion judge audit trace は `05d4e82`; いずれも GA roadmap に mirror | Marketplace/payment gate 後に live operator approval/readback を深める |
| ECC Tools native payments と AI-native harness-agnostic app を前進 (Advance ECC Tools native payments and AI-native harness-agnostic app) | ECC Tools PR evidence、billing gate、hosted analysis lane | 進行中 (in_progress) | billing announcement gate、hosted analysis lane、AgentShield fleet-summary 消費、hosted finding evidence path、harness-route policy linking、policy-promotion Action-output テレメトリ、operator 可視 promotion output、hosted promotion judge audit trace、billing announcement preflight、production KV readback 状態が GA roadmap に mirror | Marketplace purchase/webhook readback を完了し、live announcement gate を実行 |
| レガシー作業を監査・整理・紐付け (Audit, organize, and link legacy work) | `docs/stale-pr-salvage-ledger.md` と legacy inventory | 最新 (current) | legacy salvage ledger と inventory が最新; すべての localization tail は手動 language-owner review 向け Linear ITO-55 に紐付け | リリース前に legacy scan を繰り返す |
| Linear roadmap を詳細化し進捗追跡を同期 (Detail Linear roadmap and sync progress tracking) | Linear project mirror と progress-sync contract | 最新 (current) | Linear live sync と project progress snapshot が最新; progress-sync contract が file-backed work-items/status path を定義 | 各 significant merge batch 後に Linear/project status update と local work-items sync を繰り返す |
| 自己利用向け ECC 2.0 observability を提供 (Provide ECC 2.0 observability for self-use) | observability readiness gate | 完了 (complete) | observability:ready command と readiness doc が存在 | runtime/dashboard 実装は release gate 後に継続可能 |
| Mini Shai-Hulud/TanStack 保護ループを最新に保つ (Keep Mini Shai-Hulud/TanStack protection loop current) | supply-chain watch、runbook、AgentShield package-manager hardening | 最新 (current) | scheduled supply-chain watch が IOC/advisory-source refresh artifact を emit; ECC scanner が gh-token-monitor token-store persistence をカバー; AgentShield が既知 AI-tool persistence IOC、npm lifecycle/token drift、unsupported npm age-key drift、pnpm/Yarn cooldown drift を検出; ITO-57 に 5月17日 Linear evidence 更新あり | 各 significant supply-chain batch 後に advisory/source refresh と Linear sync を繰り返す |

## 最優先アクション (Top Actions)

- `naming-and-plugin-publication`: 実 tag/push、marketplace submission、最終チャネル選択は承認ゲートのまま
- `release-notes-and-notifications`: URL 根拠の refresh と公開承認が未了
- `agentshield-enterprise-iteration`: Marketplace/payment gate 後に live operator approval/readback を深める
- `ecc-tools-next-level`: Marketplace purchase/webhook readback を完了し、live announcement gate を実行

## 次の作業順序 (Next Work Order)

1. publication evidence を記録する前に、最終リリースコミットからこのダッシュボードを再生成する。
2. 次の重要な merge batch または advisory-source refresh の後に ITO-57 Linear/project status sync を繰り返す。
3. ECC Tools Marketplace purchase/webhook readback を完了し、native-payments コピーを公開する前に preflight と live announcement gate を実行する。
4. 生成されたダッシュボードと最終リリースゲートが更新された後にのみ ITO-45、ITO-46、ITO-56 を再開する。
