---
name: claude-devfleet
description: マルチエージェントコーディングのオーケストレーション（Claude DevFleet）— プロジェクト計画、分離 worktree での並列派遣、進捗監視、構造化レポート。DevFleet, multi-agent, worktree.
origin: community
---

# Claude DevFleet マルチエージェントオーケストレーション (Claude DevFleet Multi-Agent Orchestration)

## 使用タイミング (When to Use)

複数の Claude Code エージェントを並列でコーディングタスクに派遣する必要があるときにこのスキルを使用する。各エージェントは完全なツール一式を備えた分離 git ワークツリーで実行される。

MCP 経由で接続された稼働中の Claude DevFleet インスタンスが必要:
```bash
claude mcp add devfleet --transport http http://localhost:18801/mcp
```

## 仕組み (How It Works)

```
User → "Build a REST API with auth and tests"
  ↓
plan_project(prompt) → project_id + mission DAG
  ↓
Show plan to user → get approval
  ↓
dispatch_mission(M1) → Agent 1 spawns in worktree
  ↓
M1 completes → auto-merge → auto-dispatch M2 (depends_on M1)
  ↓
M2 completes → auto-merge
  ↓
get_report(M2) → files_changed, what_done, errors, next_steps
  ↓
Report back to user
```

### ツール (Tools)

| Tool | Purpose |
|------|---------|
| `plan_project(prompt)` | AI breaks a description into a project with chained missions |
| `create_project(name, path?, description?)` | Create a project manually, returns `project_id` |
| `create_mission(project_id, title, prompt, depends_on?, auto_dispatch?)` | Add a mission. `depends_on` is a list of mission ID strings (e.g., `["abc-123"]`). Set `auto_dispatch=true` to auto-start when deps are met. |
| `dispatch_mission(mission_id, model?, max_turns?)` | Start an agent on a mission |
| `cancel_mission(mission_id)` | Stop a running agent |
| `wait_for_mission(mission_id, timeout_seconds?)` | Block until a mission completes (see note below) |
| `get_mission_status(mission_id)` | Check mission progress without blocking |
| `get_report(mission_id)` | Read structured report (files changed, tested, errors, next steps) |
| `get_dashboard()` | System overview: running agents, stats, recent activity |
| `list_projects()` | Browse all projects |
| `list_missions(project_id, status?)` | List missions in a project |

> **`wait_for_mission` に関する注意:** 最大 `timeout_seconds`（デフォルト 600）まで会話をブロックする。長時間ミッションでは、ユーザーに進捗更新を見せるため、30〜60 秒ごとに `get_mission_status` でポーリングする方がよい。

### ワークフロー: 計画 → 派遣 → 監視 → レポート (Workflow: Plan → Dispatch → Monitor → Report)

1. **計画**: `plan_project(prompt="...")` を呼ぶ → `project_id` + `depends_on` チェーンと `auto_dispatch=true` 付きミッション一覧を返す。
2. **計画の提示**: ミッションタイトル、タイプ、依存チェーンをユーザーに提示する。
3. **派遣**: ルートミッション（空の `depends_on`）で `dispatch_mission(mission_id=<first_mission_id>)` を呼ぶ。残りは依存が満たされると自動派遣される（`plan_project` が `auto_dispatch=true` を設定するため）。
4. **監視**: `get_mission_status(mission_id=...)` または `get_dashboard()` で進捗を確認する。
5. **レポート**: ミッション完了時に `get_report(mission_id=...)` を呼ぶ。ハイライトをユーザーと共有する。

### 並行性 (Concurrency)

DevFleet はデフォルトで最大 3 並列エージェント（`DEVFLEET_MAX_AGENTS` で設定可能）。スロットが満杯のとき、`auto_dispatch=true` のミッションはミッションウォッチャーでキューされ、スロットが空くと自動派遣される。`get_dashboard()` で現在のスロット使用状況を確認する。

## 例 (Examples)

### フル自動: 計画して起動 (Full auto: plan and launch)

1. `plan_project(prompt="...")` → ミッションと依存関係付き計画を表示。
2. 最初のミッション（空の `depends_on`）を派遣。
3. 依存が解決されると残りが自動派遣（`auto_dispatch=true`）。
4. プロジェクト ID とミッション数を報告し、何が起動したかユーザーに伝える。
5. すべてのミッションが終端状態（`completed`、`failed`、`cancelled`）になるまで `get_mission_status` または `get_dashboard()` で定期的にポーリング。
6. 各終端ミッションで `get_report(mission_id=...)` — 成功を要約し、失敗はエラーと次のステップで明示する。

### 手動: ステップバイステップ制御 (Manual: step-by-step control)

1. `create_project(name="My Project")` → `project_id` を返す。
2. 最初（ルート）ミッションで `create_mission(project_id=project_id, title="...", prompt="...", auto_dispatch=true)` → `root_mission_id` を取得。
   後続タスクごとに `create_mission(project_id=project_id, title="...", prompt="...", auto_dispatch=true, depends_on=["<root_mission_id>"])`。
3. チェーンを開始するため最初のミッションで `dispatch_mission(mission_id=...)`。
4. 完了時に `get_report(mission_id=...)`。

### レビュー付きシーケンシャル (Sequential with review)

1. `create_project(name="...")` → `project_id` を取得。
2. `create_mission(project_id=project_id, title="Implement feature", prompt="...")` → `impl_mission_id` を取得。
3. `dispatch_mission(mission_id=impl_mission_id)`、完了まで `get_mission_status` でポーリング。
4. 結果レビューに `get_report(mission_id=impl_mission_id)`。
5. `create_mission(project_id=project_id, title="Review", prompt="...", depends_on=[impl_mission_id], auto_dispatch=true)` — 依存が既に満たされているため自動開始。

## ガイドライン (Guidelines)

- ユーザーが進めてよいと言わない限り、派遣前に必ず計画をユーザーと確認する。
- ステータス報告時はミッションタイトルと ID を含める。
- ミッションが失敗したら、リトライ前にレポートを読む。
- 一括派遣前に `get_dashboard()` でエージェントスロット空きを確認する。
- ミッション依存は DAG を形成する — 循環依存を作らない。
- 各エージェントは分離 git ワークツリーで実行し、完了時に自動マージする。マージコンフリクトが起きた場合、変更はエージェントのワークツリーブランチに残り手動解決する。
- 手動でミッションを作るとき、依存完了時に自動トリガーしたい場合は必ず `auto_dispatch=true` を設定する。このフラグがないとミッションは `draft` のまま。
