# ECC 2.0 リファレンスアーキテクチャ (ECC 2.0 Reference Architecture)

現在の実行ミラー:
[`ECC-2.0-GA-ROADMAP.md`](ECC-2.0-GA-ROADMAP.md)。

このドキュメントは、2026年5月のリファレンス調査を具体的な ECC バックログの形に落とし込む。第二の戦略メモではない。以下のリファレンス圧力はすべて、adapter、check、observable signal、security policy、PR review surface、または release-readiness gate として着地させるべきである。

## リファレンスベースライン (Reference Baseline)

スナップショット日: 2026-05-12。

| 参照 (Reference) | ECC 2.0 への主な圧力 (Primary pressure on ECC 2.0) | 具体的な ECC デルタ (Concrete ECC delta) |
| --- | --- | --- |
| [`stablyai/orca`](https://github.com/stablyai/orca) | ターミナル、ソース管理、GitHub 統合、SSH、通知、design/browser モード、アカウント切り替え、worktree ごとの context を備えた worktree-native マルチエージェント IDE。 | worktree lifecycle、review state、notification state、account/provider identity を first-class adapter signal として扱う。 |
| [`superset-sh/superset`](https://github.com/superset-sh/superset) | 並列実行、worktree 分離、diff review、workspace preset、広い CLI-agent 互換を備えたデスクトップ AI-agent workspace。 | workspace preset taxonomy を追加し、ECC2 session/worktree state を外部エディターが消費できるほど export 可能にする。 |
| [`standardagents/dmux`](https://github.com/standardagents/dmux) | tmux/worktree orchestration、lifecycle hook、マルチ選択 agent 制御、smart merge、file browser、通知、クリーンアップ。 | harness matrix に lifecycle-hook カバレッジを追加し、merge/conflict queue event を定義する。 |
| [`aidenybai/ghast`](https://github.com/aidenybai/ghast) | cwd グループ化 workspace、pane、tab、drag/drop、検索、通知を備えたネイティブ macOS ターミナルマルチプレクサ。 | ターミナルネイティブ ergonomics を保ちつつ、cwd/session grouping と検索可能な handoff/session record を追加する。 |
| [`jarrodwatts/claude-hud`](https://github.com/jarrodwatts/claude-hud) | context、tool、agent、todo、transcript ベース activity 向けの常時可視 Claude Code statusline。 | context、cost、tool call、active agent、todo、queue state、check、risk 向け ECC HUD/status payload を形式化する。 |
| [`stanford-iris-lab/meta-harness`](https://github.com/stanford-iris-lab/meta-harness) | タスク固有 harness 設計（何を保存・取得・表示するか）の自動探索。 | ECC 改善ループを scenario spec、proposer trace、verifier result、promoted playbook に分割する。 |
| [`greyhaven-ai/autocontext`](https://github.com/greyhaven-ai/autocontext) | trace、report、artifact、dataset、playbook、役割分離 evaluator による再帰的 harness 改善。 | インストール済み harness asset を mutate する前に再利用可能な trace と playbook を保存する。 |
| [`NousResearch/hermes-agent`](https://github.com/NousResearch/hermes-agent) | memory、skill、scheduler、gateway、subagent、terminal backend、migration tooling を備えた自己改善 operator shell。 | 下位コマンドを隠さず、local、SSH、container、hosted terminal backend 横断で ECC をポータブルに保つ。 |
| [`anthropics/claude-code`](https://github.com/anthropics/claude-code), [`sst/opencode`](https://github.com/sst/opencode), Zed, Codex, Cursor, Gemini | 各 agent harness は異なる hook、plugin surface、session store、config file、review loop を公開する。 | 1つの harness を canonical UX として扱わず、公開 adapter compliance matrix を維持する。 |
| ローカル Claude Code ソースレビュー | session、tool、permission、hook、remote、analytics、task、context-suggestion surface は公開 CLI UX より構造化されている。 | session message、permission request、tool progress、context pressure、summary state 周辺で status と risk event をモデル化する。 |

## アーキテクチャの形 (Architecture Shape)

ECC 2.0 は、commands、agents、skills のカタログだけでなく、harness operating system であるべきである。

```text
┌──────────────────────────────────────────────────────────────┐
│ Operator Surface                                             │
│ CLI, plugin, TUI, HUD/statusline, release gates, PR checks   │
├──────────────────────────────────────────────────────────────┤
│ Harness Adapter Layer                                        │
│ Claude Code, Codex, OpenCode, Cursor, Gemini, Zed, dmux,     │
│ Orca, Superset, Ghast, terminal-only                         │
├──────────────────────────────────────────────────────────────┤
│ Worktree, Session, And Queue Runtime                         │
│ worktrees, panes, sessions, todos, checks, merge/conflict    │
│ queues, notification state, ownership, handoff exports       │
├──────────────────────────────────────────────────────────────┤
│ Observability And Evaluation Loop                            │
│ JSONL traces, status snapshots, risk ledger, harness audit,  │
│ scenario specs, verifiers, promoted playbooks, RAG sets      │
├──────────────────────────────────────────────────────────────┤
│ Security And Commercial Platform                             │
│ AgentShield policies/SARIF, ECC Tools checks, billing,       │
│ Linear/GitHub sync, enterprise reports                       │
└──────────────────────────────────────────────────────────────┘
```

## リファレンスからバックログへのマップ (Reference-To-Backlog Map)

### Worktree と Session のオーケストレーション (Worktree And Session Orchestration)

Orca、Superset、dmux、Ghast から採用:

- Worktree lifecycle event: create、resume、pause、stop、diff、review、PR、
  merge-ready、conflict、stale、close、salvage。
- repo、branch、cwd、task、owner、harness による session grouping。
- release lane、PR triage lane、docs lane、security lane、
  test-writer lane 向け workspace preset。
- blocked CI、dirty worktree、merge conflict、stale review、
  完了した autonomous run 向け notification。
- maintainer の所有権を奪わず diff と PR に注釈できる review loop。

リポジトリ作業:

- `everything-claude-code`: adapter compliance matrix と public scorecard onramp を拡張する。
- `ecc2`: hosted telemetry を追加する前に、安定した local payload 経由で session/worktree state を公開する。
- `ECC-Tools`: PR checks、issue routing、Linear sync に同じ lifecycle events を消費する。

検証:

- `npm run harness:audit -- --format json`
- `npm run observability:ready`
- matrix が docs から data に移行した後の targeted adapter matrix tests

### HUD、Status、Observability (HUD, Status, And Observability)

Claude HUD と Claude Code source review から採用:

- Context pressure: usage、compaction risk、large-result warning、summary
  state。
- Tool activity: active tool、recent tool、duration、risky operation、
  permission request。
- Agent activity: active subagent、delegated task、branch/worktree、wait
  state。
- Queue activity: open PR/issue、CI state、stale/conflict batch、review
  state、closed-stale salvage backlog。
- Cost/risk: token cost 見積もり、destructive-operation risk、hook/MCP risk、
  security scan state。

リポジトリ作業:

- `docs/architecture/observability-readiness.md` を operator-facing readiness gate として維持する。
- ECC2 と ECC Tools の両方が消費できる versioned HUD/status JSON contract を定義する。
- visual UI を構築する前に、`loop-status`、`session-inspect`、harness audit、risk ledger からの sample exports を fixture directory に追加する。

検証:

- `npm run observability:ready`
- すべての status payload に対する fixture validation
- session history を読む commands の cross-platform smoke test

### 自己改善 Harness ループ (Self-Improving Harness Loop)

Meta-Harness、Autocontext、Hermes Agent から採用:

- ループを observation、proposal、verification、promotion、rollback に分離する。
- 提案された改善を最終的な変更ファイルだけでなく、trace plus artifact として保存する。
- verifier が scenario を改善し、blast radius を広げないことを証明した後にのみ playbook を promote する。
- vetted ECC patterns、team history、CI failures、review outcomes、harness config quality、security decisions 向けの RAG/reference sets を使用する。

リポジトリ作業:

- `everything-claude-code`: scenario specs、verifier contracts、playbook promotion rules を文書化する。
- `ECC-Tools`: workspace を溢れさせずに analyzer findings を PR comments、check runs、Linear tasks にマップする。
- `agentshield`: prompt-injection と config-risk findings を regression suites に供給する。

現在のプロトタイプ:

- `docs/architecture/evaluator-rag-prototype.md` は read-only evaluator/RAG artifact contract を定義する。
- `examples/evaluator-rag-prototype/` は stale-PR salvage 向けの最初の scenario spec、trace、report、candidate playbook、verifier result を記録する。

検証:

- trace、report、candidate playbook、verifier result を出力する read-only prototype
- bad proposal が拒否されることを証明する regression fixture

### AgentShield Enterprise Security Platform (AgentShield Enterprise Security Platform)

AgentShield は有用な scanner から enterprise security platform へ移行すべきである。

バックログの形:

- org baseline、rule severity、owner、exception、expiration、
  evidence、audit trail 向け policy schema。
- GitHub code scanning 向け SARIF 出力。
- OSS、team、enterprise、regulated、high-risk hooks/MCP、
  CI enforcement 向け policy pack。
- MCP package、npm/pip provenance、CVE、
  typosquat、dependency reputation 向け supply-chain intelligence。
- prompt-injection corpus と regression benchmark。
- JSON および executive HTML/PDF report 出力。

検証:

- schema unit tests
- SARIF fixture tests
- policy-pack golden tests
- public issue history からの false-positive regression tests

### ECC Tools Commercial And Review Platform (ECC Tools Commercial And Review Platform)

ECC Tools は billing、deep analysis、PR check、Linear progress tracking 向けの GitHub-native layer となるべきである。

バックログの形:

- payment 告知前の native GitHub Marketplace billing audit:
  plan、seat、org/account mapping、subscription state、overage behavior、
  downgrade/cancel behavior、failure mode。
- GitGuardian、Dependabot、CodeRabbit、Greptile の有用部分に匹敵する deep analyzer:
  security evidence、dependency risk、
  CI/CD recommendation、PR review behavior、config quality、token/cost risk、
  harness drift。
- 検証済み ECC pattern、過去 PR outcome、
  dependency advisory、CI failure、review decision、team 固有
  convention 上の RAG/reference set。
- finding を project status、milestone evidence、
  owner-ready issue にマップし issue limit を枯渇させない Linear sync。

検証:

- check-run fixture tests
- billing webhook replay tests
- analyzer golden PR fixtures
- Linear sync dry-run fixture

### Closed-Stale Salvage レーン (Closed-Stale Salvage Lane)

stale PR を close することで public queue を使いやすく保つが、contributor が rebase する時間がなくなっただけで有用な作業を失ってはならない。

実行ルール:

1. stale、conflicted、または obsolete な PR を明確な courtesy comment 付きで close する。
2. source PR、author、close 理由、有用 file/concept、risk、推奨 maintainer action を salvage ledger に記録する。
3. cleanup batch の後、各 closed PR diff を手動で検査する。
4. patch が clean に適用でき、現在のアーキテクチャを維持するときのみ cherry-pick する。それ以外は有用なアイデアを fresh maintainer branch で再実装する。
5. commit body または PR body で attribution を保持する。
6. 有用な作業が land したとき、maintainer PR または merged commit をリンクして source PR にコメントを返す。
7. ledger item を landed、superseded、Linear-tracked、または no-action としてマークする。

必須の安全策:

- generated churn、bulk localization、dependency major-version change を盲目的に cherry-pick しない。
- 1つの salvage megabranch より小さな maintainer PR を優先する。
- 通常の code、docs、catalog change と同じ validation gate を実行する。
- 最終実装が書き直されても contributor credit を保持する。

## 近い将来の実装順序 (Near-Term Implementation Order)

1. harness adapter matrix と public scorecard onramp を拡張する。
2. rc.1 publication の前に fresh final-commit evidence で release/name/plugin publication checklist を最新に保つ。
3. HUD/status JSON contract と fixture directory を定義する。
4. AgentShield policy schema と SARIF fixtures を開始する。
5. ECC Tools billing と check-run surfaces を監査する。
6. legacy folders と closed-stale PRs を salvage ledger に棚卸しする。
7. 有用な stale work を小さな attributed maintainer PR に移植する。

## 非目標 (Non-Goals)

- local event model が有用でテスト可能になる前の hosted telemetry。
- verifier evidence なしでの user harness configs の自動 mutation。
- いずれか1つの agent harness を canonical interface として扱うこと。
- command、package、marketplace、billing evidence が fresh になる前の release または payments announcements。
