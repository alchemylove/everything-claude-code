# HUD ステータスとセッション制御契約 (HUD Status And Session Control Contract)

この契約は、ローカル operator 面、handoff、将来の HUD 向けに ECC が使うポータブル status payload を定義します。意図的に harness-neutral です：Claude Code statusline、Codex pane、dmux session、OpenCode run、またはターミナルのみ workflow はフィールド名を変えずに部分データを emit できます。

正規の例は
[`examples/hud-status-contract.json`](../../examples/hud-status-contract.json) にあります。

## Payload 形状 (Payload Shape)

すべての status payload は `schema_version: "ecc.hud-status.v1"` を使い、次の
トップレベルセクションを安定させます：

| Field | Purpose | Primary Source |
|---|---|---|
| `context` | Model, harness, repo, branch, worktree, session id, and context-window pressure | statusline stdin, git, session adapters |
| `toolCalls` | Recent tool counts, pending calls, stale calls, and last tool event | `loop-status`, `tool-usage.jsonl`, hook bridge |
| `activeAgents` | Current workers/subagents, runtime state, branch, worktree, objective, and handoff paths | dmux/orchestration snapshots |
| `todos` | Current in-progress task and todo counts | Claude todos, local task files, plan metadata |
| `checks` | Local and remote validation status with command/check URLs when available | CI, local commands, release gates |
| `cost` | Session spend, token counts, budget, and trend | cost tracker, metrics bridge |
| `risk` | Attention state, conflict pressure, stale calls, dirty worktree, and manual-review flags | readiness gates, git, queue state |
| `queueState` | GitHub PR/issue/discussion counts, conflict queue, merge queue, and stale-salvage queue | GitHub sync, work items |
| `sessionControls` | Supported operator actions for the current target | ECC CLI, dmux, git/GitHub |
| `sync` | Linear, GitHub, and handoff publication state | status updates, work items, handoff writer |

harness が signal を露出できない場合、フィールドは `null`、空配列、または `"unknown"` にできます。Producer は互換性のない名前を捏造すべきではありません。Consumer は欠落セクションを green ではなく unavailable として描画すべきです。

## セッション制御 (Session Controls)

最小 session-control 語彙は次です：

| Control | Meaning |
|---|---|
| `create` | Start a new isolated run, worktree, or orchestration plan |
| `resume` | Reattach to an existing session or historical target |
| `status` | Emit the current payload without mutating state |
| `stop` | Request a graceful stop or mark the session completed |
| `diff` | Show current working-tree or worker diff |
| `pr` | Open or inspect the linked pull request |
| `mergeQueue` | Show merge-ready, blocked, and waiting-check items |
| `conflictQueue` | Show dirty/conflicting PRs or worktrees needing integration |

`sessionControls.supported` は現在の harness で利用可能な制御を列挙します。
`sessionControls.blocked` は利用不可制御を説明します（例：GitHub token 欠落、tmux session なし、read-only adapter）。

## 同期契約 (Sync Contract)

sync セクションは durable tracker を分離します：

- `Linear` は project status update id、health、issue 作成が
  workspace 容量でブロックされているかを記録します。
- `GitHub` は現在の repo、PR/issue/discussion queue 件数、セッションに紐づく
  最新 merged または open PR を記録します。
- `handoff` は durable Markdown handoff path と、最新 batch 後に書き込まれたかを記録します。

これにより realtime 進捗追跡が明示的になり、すべての run が Linear issue や
GitHub comment を作る必要はありません。Linear issue 容量がブロックされていても、
status payload は project update と repo handoff で進捗を証明できます。

## 現在の実装 (Current Implementations)

- `ecc status --json` は SQLite state store から readiness、active sessions、skill runs、install
  health、governance、リンクされた work items を公開します。
- `ecc loop-status --json --write-dir <dir>` は長時間 loop 向けに live transcript snapshot と
  attention signal を書き込みます。
- `ecc session-inspect <target> --write <path>` は dmux と Claude-history adapter から
  正規 session snapshot を emit します。
- `scripts/hooks/ecc-statusline.js` は Claude Code 内で compact model、task、cost、tool、
  file、duration、directory、context pressure signal を描画します。

`ecc.hud-status.v1` payload は、ECC が専用フルスクリーン HUD を成長させる前に
これらの面が投影できる共通 outer contract です。
