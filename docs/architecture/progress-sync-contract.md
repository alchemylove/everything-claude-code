# 進捗同期契約 (Progress Sync Contract)

ECC 2.0 は GitHub、Linear、ローカル handoff、リポジトリ roadmap 横断で実行状態を追跡します。この契約は、lane が current と主張する前に必要な最小 evidence を定義します。

## 真実のソース (Sources Of Truth)

| Surface | Role | Current rule |
| --- | --- | --- |
| GitHub PRs/issues/discussions | Public queue and review state | Recheck live counts before every significant merge batch and before release approval. |
| Linear project | Executive roadmap and stakeholder status update | Use project documents and project/issue comments because project status updates are disabled in this workspace; create/reuse issues for durable execution lanes. |
| Local handoff | Durable operator continuity | Update the active handoff after every merge batch, queue drain, skipped release gate, or blocked external action. |
| Repo roadmap | Auditable planning mirror | Keep `docs/ECC-2.0-GA-ROADMAP.md` aligned to merged PR evidence and unresolved gates. |
| `scripts/work-items.js` | Local tracker bridge | Sync GitHub PRs/issues into the SQLite work-items store for status snapshots and blocked follow-up. |

## フロー Lane (Flow Lanes)

リポジトリミラーは次のフロー lane を使い、ECC 作業が1つの
区別のない backlog に潰れないようにします：

- Queue hygiene and stale-work salvage
- Release, naming, plugin publication, and announcements
- Harness adapter compliance
- Local observability, HUD/status, and session control
- Evaluator/RAG and self-improving harness loops
- AgentShield enterprise security platform
- ECC Tools billing, PR-risk checks, deep analysis, and Linear sync
- Legacy artifact audit and translator/manual-review tails

各フロー lane には owner artifact 1つ、current evidence source 1つ、
next action 1つが必要です。3つのフィールドのいずれかが欠けていれば lane は current ではありません。

## 重要な Merge Batch 更新 (Significant Merge Batch Update)

重要な merge batch の後、Linear と handoff を次で更新：

1. 追跡 GitHub リポジトリの現在の公開 queue 件数。
2. マージされた PR 番号、commit ID、validation evidence。
3. 変更されたリリースゲート（あれば）。
4. 延期またはスキップされた作業と明示的な理由。
5. 次の1〜2実装スライス。

Linear project status update が利用できない場合は、placeholder issue を作る代わりに
project document と project/issue comments を使います。Issue 容量は
durable execution lane に使えますが、evidence バックドの project status の
代替に issue 容量を使わないでください。lane に durable execution owner が
必要なときのみ exact-title issue を作成または再利用し、それらの issue を
リポジトリ evidence にリンクします。

## リアルタイム境界 (Realtime Boundary)

ローカル realtime path はデフォルトで file-backed です：

- `node scripts/work-items.js sync-github --repo <owner/repo>` は現在の
  GitHub PR と issue 状態を SQLite work-items store に import します。
- `node scripts/status.js --json` と `node scripts/work-items.js list --json` は
  HUD、handoff、または将来の Linear sync 向けにローカル状態を公開します。
- Linear は外部 status 面のまま；リポジトリは release-ready にするために
  hosted telemetry を要求しません。

PostHog などの hosted telemetry は後から追加できますが、
第2の真実のソースになるのではなく同じ event model を消費する必要があります。

## リリースゲート (Release Gate)

この契約だけから publish、tag、announce、marketplace package 提出、
plugin 可用性の主張をしないでください。リリース準備には依然として
publication-readiness evidence ドキュメント、新鮮な queue チェック、package チェック、
plugin チェック、明示的な maintainer 承認が必要です。
