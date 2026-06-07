---
name: dynamic-workflow-mode
description: "Design task-local harnesses, eval gates, and reusable skill extraction for Claude dynamic workflow mode and other adaptive agent harnesses."
origin: ECC
---

# ダイナミックワークフローモード (Dynamic Workflow Mode)

コーディングエージェントが静的コマンドフローに従うだけでなくタスクローカル harness を生成・適応できるときにこのスキルを使用する。目標は dynamic workflow mode を規律あるシステムにすること: 一回限り作業の一時 harness、繰り返し作業の共有スキル抽出、チーム向け可観測 control pane チェックポイント。

## 有効化タイミング (When To Activate)

- ユーザーが dynamic workflows、custom harnesses、harness-per-task、adaptive workflows、Claude Code dynamic workflow mode に言及する。
- タスクにカスタムループ、evaluator、crawler、fixture generator、watcher、ローカルダッシュボードが必要。
- 複数エージェントが同じ繰り返しプロセスを必要とするが、まだ共有スキルとして捕捉されていない。
- ワークフローに永続 handoff アーティファクト、eval 証拠、マージ前のオペレーター承認が必要。

## コア契約 (Core Contract)

dynamic workflow mode は、同じステップを手動で駆動するより harness が安く安全な場合にのみタスクローカル harness を生成すべき。harness には次が必要:

- **Objective**: 所有する成果と明示的に所有しない成果。
- **Inputs**: ファイル、URL、プロンプト、データソース、認証情報ポリシー、ユーザー制約。
- **Outputs**: コミット、レポート、スクリーンショット、ステータスファイル、control pane スナップショット。
- **Eval**: タスクに紐づく少なくとも 1 つの pass/fail チェック（「動いた」だけではない）。
- **Handoff**: 次のオペレーターに何が起きたか、何がブロックされているか、どう再開するかを伝える短いアーティファクト。

## ダイナミック Harness 決定木 (Dynamic Harness Decision Tree)

1. **ワンショットタスク**: インラインのまま。harness を発明しない。
2. **入力が変わる繰り返しタスク**: タスクローカル harness を作り temp またはプロジェクトローカル作業領域に置く。
3. **チームメイトやリポジトリ横断の繰り返しタスク**: パターンを共有スキルに抽出。
4. **外部状態、キューイング、承認があるタスク**: 自動化を増やす前に control pane 可視性を追加。
5. **安全リスクがあるタスク**: 自律実行前に eval ゲートと人間マージゲートを追加。

## タスクローカル Harness テンプレート (Task-Local Harness Template)

コードを書く前にこの構造を使う:

```markdown
# Dynamic Workflow Harness

Objective:
- Ship:
- Do not ship:

Inputs:
- Repo or workspace:
- External systems:
- Credentials policy:

Loop:
1. Discover current state.
2. Generate or update the smallest useful artifact.
3. Run eval checks.
4. Record status and handoff.
5. Stop on failed gate, unclear ownership, or unsafe external action.

Eval:
- Command:
- Expected pass signal:
- Failure owner:

Handoff:
- Status:
- Evidence:
- Next action:
```

## 共有スキル抽出 (Shared Skill Extraction)

次の少なくとも 2 つが真のときのみタスクローカル harness を共有スキルに昇格:

- 同じワークフローが複数セッション、リポジトリ、チーム、ローンチに現れる。
- ワークフローに特定の言語、ツール、安全シーケンスが必要。
- オペレーターがゲートをスキップまたはコンテキストを失うため障害が繰り返される。
- ワークフローに安定した入出力契約がある。
- control pane、ステータスボード、チーム handoff が有益。

抽出時はまず `skills/<name>/SKILL.md` にスキルを書く。レガシースラッシュエントリ面がまだ必要な場合のみ command shim を追加。

## Control Pane チェックポイント (Control Pane Checkpoints)

dynamic workflow mode は状態を露出するとチームで使える。タスクが 1 セッションを超えるときはこれらのチェックポイントを記録:

- **Plan**: 目的、所有者、受け入れ基準、リスクのある外部システム。
- **Queue**: 作業項目、割り当てエージェントロール、branch/worktree、依存エッジ。
- **Run**: アクティブ harness、現在ループステップ、最近の eval 結果、利用可能なら token/cost シグナル。
- **Gate**: テスト結果、ブラウザスクリーンショット、セキュリティレビュー、マージ準備。
- **Handoff**: 完了、失敗、人間判断が必要なこと。

リポジトリで ECC2 state が有効なら、追跡されないメモを散らす代わりに ECC control pane または state-store バックスクリプトでチェックポイントを追加/読み取りを優先。

## Eval ゲート (Eval Gates)

すべての dynamic harness にタスク固有 eval が必要。最安で信頼できるゲートを選ぶ:

| Work Type | Eval Gate |
| --- | --- |
| Code feature | Focused test, lint, coverage, and one integration path |
| UI/control pane | Browser smoke with screenshot and overflow/error checks |
| Agent workflow | Fixture transcript or seeded work item with expected routing |
| Research/content | Source-neutral brief, claim checklist, and publish-ready outline |
| Integration | Dry-run command, config validation, and no-secret scan |

別のチームメイトが eval を再実行できるまで dynamic workflow を再利用可能と主張しない。

## アンチパターン (Anti-Patterns)

- 実際の意思決定ロジックをオペレーターから隠すスクリプト生成。
- dynamic workflow mode をテストスキップの許可と扱う。
- 共有スキルやステータスアーティファクトが本当の成果物なのに一回限りドキュメントを作る。
- 所有権、マージゲート、コンフリクトポリシーなしで複数エージェント実行。
- 生の非公開リサーチデータを公開ドキュメントに漏らす。

## 出力標準 (Output Standard)

次で終える:

- harness またはスキルパス。
- eval コマンドと結果。
- control pane または handoff アーティファクトパス。
- 次の再利用可能抽出候補。
