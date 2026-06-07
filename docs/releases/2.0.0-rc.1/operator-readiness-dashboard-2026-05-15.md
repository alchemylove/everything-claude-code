# ECC オペレーター準備ダッシュボード (ECC Operator Readiness Dashboard)

このダッシュボードは `npm run operator:dashboard` により生成されます。オペレーター向けスナップショットであり、リリース承認ではありません。

生成日時 (Generated): 2026-05-17T05:08:31.916Z
コミット (Commit): 6d130cfcd5d06b42c7eb30be8e109cfa87fde197
ステータス (Status): 作業残あり (work remaining)

## 現状ステータス (Current Status)

| 領域 (Area) | ステータス (Status) | エビデンス (Evidence) |
| --- | --- | --- |
| PR キュー (PR queue) | 最新 (Current) | 追跡 repo 全体で open PR 6 件 |
| Issue キュー (Issue queue) | 最新 (Current) | 追跡 repo 全体で open issue 3 件 |
| Discussions | 最新 (Current) | maintainer touch 必要 0 件; accepted 回答欠落 0 件 |
| ローカル worktree (Local worktree) | 要対応 (Needs work) | blocking dirty file 7 件; ignored dirty entry 1 件 |
| ダッシュボード生成 (Dashboard generation) | 最新 (Current) | platform audit ready: true; GitHub skipped: false |
| 公開 (Publication) | 未完了 (Not complete) | release、npm、plugin、billing、announcement ゲートは下記で追跡 |

## プロンプトから成果物へのチェックリスト (Prompt-To-Artifact Checklist)

| 目的要件 (Objective requirement) | 成果物またはゲート (Artifact or gate) | ステータス (Status) | エビデンス (Evidence) | ギャップ (Gap) |
| --- | --- | --- | --- | --- |
| 公開 PR を 20 未満に保つ (Keep public PRs below 20) | `scripts/platform-audit.js` live GitHub sweep | 最新 (current) | 5 追跡 repo で open PR 6 件 | リリース前に繰り返す |
| 公開 issue を 20 未満に保つ (Keep public issues below 20) | `scripts/platform-audit.js` live GitHub sweep | 最新 (current) | 5 追跡 repo で open issue 3 件 | リリース前に繰り返す |
| リポジトリ discussion に応答・管理 (Respond and manage repository discussions) | `scripts/platform-audit.js` discussion summary | 最新 (current) | maintainer touch 必要 0 件; accepted 回答欠落の回答可能 discussion 0 件 | リリース前に繰り返す |
| ITO-44 完了ダッシュボードを反復可能コマンド化 (Build ITO-44 completion dashboard into a repeatable command) | `npm run operator:dashboard` | 完了 (complete) | operator:dashboard package script が存在 | 生成ダッシュボードを publication evidence に添付し続ける |
| ECC 2.0 preview pack 準備完了 (ECC 2.0 preview pack ready) | `docs/releases/2.0.0-rc.1/preview-pack-manifest.md` | 進行中 (in_progress) | preview pack manifest が in-tree | 最終 clean-checkout release approval と publish evidence が未了 |
| Hermes 専用 skill を安全に含める (Include Hermes specialized skills safely) | `docs/HERMES-SETUP.md` と `skills/hermes-imports/SKILL.md` | 進行中 (in_progress) | Hermes setup と import skill が存在 | 最終 preview-pack smoke と release review が未了 |
| 命名変更、Claude plugin、Codex plugin パス準備 (Prepare name-change, Claude plugin, and Codex plugin paths) | naming-and-publication-matrix と publication-readiness | 進行中 (in_progress) | naming matrix と plugin readiness gate が存在 | 実 tag/push、marketplace submission、最終チャネル選択は承認ゲートのまま |
| リリースノート、記事、tweet、プッシュ通知準備 (Prepare release notes, articles, tweets, and push notifications) | `docs/releases/2.0.0-rc.1/` 配下の social と release-copy ファイル | 進行中 (in_progress) | release notes、X thread、LinkedIn draft が存在 | URL 付き refresh と公開承認が未了 |
| AgentShield enterprise 反復を前進 (Advance AgentShield enterprise iteration) | AgentShield PR evidence と enterprise roadmap | 進行中 (in_progress) | AgentShield enterprise PR evidence が GA roadmap に mirror | policy promotion 出荷後、protected rollout 周辺の workflow automation とより豊富な runtime review UX が未了 |
| ECC Tools native payments と AI-native harness-agnostic app を前進 (Advance ECC Tools native payments and AI-native harness-agnostic app) | ECC Tools PR evidence、billing gate、hosted analysis lane | 進行中 (in_progress) | billing announcement gate、hosted analysis lane、AgentShield fleet-summary 消費、hosted finding evidence path、harness-route policy linking が GA roadmap に mirror | live Marketplace テストアカウント readback、hosted promotion テレメトリ、より豊富な operator review UX が未了 |
| legacy 作業を監査・整理・紐付け (Audit, prune, or attach legacy work) | `docs/stale-pr-salvage-ledger.md` と legacy inventory | 進行中 (in_progress) | legacy salvage ledger と ITO-55 追跡が存在 | 最終 translation/manual-review tail が残存 |
| Linear roadmap 詳細化と進捗追跡同期 (Keep Linear roadmap detailed and progress tracking synchronized) | Linear project mirror と progress-sync contract | 進行中 (in_progress) | repo mirror と progress-sync contract が存在 | 定期的な Linear ステータス同期と productized realtime sync が未了 |
| 自己利用向け ECC 2.0 observability (Provide ECC 2.0 observability for self-use) | observability readiness gate | 完了 (complete) | observability:ready コマンドと readiness doc が存在 | runtime/dashboard 実装は release gate 後も継続可 |
| Mini Shai-Hulud/TanStack 保護ループを最新維持 (Keep Mini Shai-Hulud/TanStack protection loop current) | supply-chain watch と runbook | 最新 (current) | スケジュール supply-chain watch が IOC と advisory-source refresh artifact を emit | 各 significant merge batch 後の Linear ステータス同期は ITO-57 フォローアップ |

## 優先アクション (Top Actions)

- `ecc-preview-pack`: 最終 clean-checkout release approval と publish evidence が未了
- `hermes-specialized-skills`: 最終 preview-pack smoke と release review が未了
- `naming-and-plugin-publication`: 実 tag/push、marketplace submission、最終チャネル選択は承認ゲートのまま
- `release-notes-and-notifications`: URL 付き refresh と公開承認が未了
- `agentshield-enterprise-iteration`: policy promotion 出荷後、protected rollout 周辺の workflow automation とより豊富な runtime review UX が未了
- `ecc-tools-next-level`: live Marketplace テストアカウント readback、hosted promotion テレメトリ、より豊富な operator review UX が未了
- `legacy-salvage`: 最終 translation/manual-review tail が残存
- `linear-roadmap-and-progress`: 定期的な Linear ステータス同期と productized realtime sync が未了

## 次の作業順序 (Next Work Order)

1. 公開エビデンスを記録する前に、最終リリースコミットからこのダッシュボードを再生成する。
2. ITO-57 を継続し、スケジュール済み supply-chain watch の advisory-source レポートについて Linear ステータス同期を行う。
3. ネイティブペイメント告知コピーを公開する前に、ECC Tools の live Marketplace テストアカウント readback を進める。
4. 生成済みダッシュボードと最終リリースゲートを更新した後にのみ、ITO-45、ITO-46、ITO-56 を再開する。
