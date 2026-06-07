# PR レビューとキュー トリアージ — 2026年3月13日 (PR Review And Queue Triage — March 13, 2026)

## スナップショット (Snapshot)

このドキュメントは、`2026-03-13T08:33:31Z` 時点の
`everything-claude-code` プルリクエストキューに対する GitHub トリアージのライブスナップショットを記録する。

使用したソース:

- `gh pr view`
- `gh pr checks`
- `gh pr diff --name-only`
- マージ済み `#399` の head に対する targeted なローカル検証

このパスで使用した stale 閾値:

- `last updated before 2026-02-11`（2026年3月13日より `>30` 日前）

## PR `#399` 振り返りレビュー (PR `#399` Retrospective Review)

PR:

- `#399` — `fix(observe): 5-layer automated session guard to prevent self-loop observations`
- state: `MERGED`
- merged at: `2026-03-13T06:40:03Z`
- merge commit: `c52a28ace9e7e84c00309fc7b629955dfc46ecf9`

変更ファイル:

- `skills/continuous-learning-v2/hooks/observe.sh`
- `skills/continuous-learning-v2/agents/observer-loop.sh`

マージ済み head `546628182200c16cc222b97673ddd79e942eacce` に対して実施した検証:

- 変更された両方の shell script に対する `bash -n`
- `node tests/hooks/hooks.test.js`（`204` passed、`0` failed）
- 以下に対する targeted な hook 呼び出し:
  - interactive CLI session
  - `CLAUDE_CODE_ENTRYPOINT=mcp`
  - `ECC_HOOK_PROFILE=minimal`
  - `ECC_SKIP_OBSERVE=1`
  - `agent_id` payload
  - trimmed `ECC_OBSERVE_SKIP_PATHS`

動作結果:

- コアの self-loop 修正は機能する
- automated-session guard の分岐は意図どおり observation 書き込みを抑制する
- 最終的な `non-cli => exit` の entrypoint ロジックは、fail-closed として正しい形である

残存する所見:

1. Medium: スキップされた automated session でも、新しい guard による exit の前に homunculus project state が作成される。
   `observe.sh` は automated-session guard ブロックに到達する前に `cwd` を解決し project detection を source するため、
   `detect-project.sh` は後で early exit する session に対しても
   `projects/<id>/...` ディレクトリを作成し `projects.json` を更新し続ける。
2. Low: 新しい guard matrix は direct な regression coverage なしで出荷された。
   hook test suite は隣接する動作は引き続き検証するが、
   新しい `CLAUDE_CODE_ENTRYPOINT`、`ECC_HOOK_PROFILE`、
   `ECC_SKIP_OBSERVE`、`agent_id`、または trimmed skip-path の分岐を direct に assert していない。

判定:

- `#399` は主要目標に対して技術的に正しく、緊急の loop-stop 修正としてマージしても安全だった。
- ただし、automated-session guard を project-registration の副作用より前に移動し、
  explicit な guard-path test を追加する follow-up issue または patch が依然として必要である。

## オープン PR 一覧 (Open PR Inventory)

現在 `4` 件のオープン PR がある。

### キュー表 (Queue Table)

| PR | Title | Draft | Mergeable | Merge State | Updated | Stale | Current Verdict |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `#292` | `chore(config): governance and config foundation (PR #272 split 1/6)` | `false` | `MERGEABLE` | `UNSTABLE` | `2026-03-13T07:26:55Z` | `No` | `Best current merge candidate` |
| `#298` | `feat(agents,skills,rules): add Rust, Java, mobile, DevOps, and performance content` | `false` | `CONFLICTING` | `DIRTY` | `2026-03-11T04:29:07Z` | `No` | `Needs changes before review can finish` |
| `#336` | `Customisation for Codex CLI - Features from Claude Code and OpenCode` | `true` | `MERGEABLE` | `UNSTABLE` | `2026-03-13T07:26:12Z` | `No` | `Needs manual review and draft exit` |
| `#420` | `feat: add laravel skills` | `true` | `MERGEABLE` | `UNSTABLE` | `2026-03-12T22:57:36Z` | `No` | `Low-risk draft, review after draft exit` |

`>30 days since last update` ルールによると、現在オープン中の PR に stale はない。

## PR ごとの評価 (Per-PR Assessment)

### `#292` — Governance / Config Foundation

ライブ状態:

- open
- non-draft
- `MERGEABLE`
- merge state `UNSTABLE`
- 表示されている checks:
  - `CodeRabbit` passed
  - `GitGuardian Security Checks` passed

スコープ:

- `.env.example`
- `.github/ISSUE_TEMPLATE/copilot-task.md`
- `.github/PULL_REQUEST_TEMPLATE.md`
- `.gitignore`
- `.markdownlint.json`
- `.tool-versions`
- `VERSION`

評価:

- これは現在のキューで最もクリーンな merge candidate である。
- ブランチはすでに current `main` に refresh 済みである。
- 現在表示されている bot feedback は、明らかに merge-blocking というより minor/nit レベルである。
- 主な注意点は、現在表示されているのが external bot checks のみであること。
  現在の PR checks 出力には GitHub Actions matrix run が表示されていない。

現在の推奨:

- `Mergeable after one final owner pass.`
- conservative な path を取るなら、マージ前に残りの
  `.env.example`、PR-template、`.tool-versions` の nitpick を human が quick review する。

### `#298` — Large Multi-Domain Content Expansion

ライブ状態:

- open
- non-draft
- `CONFLICTING`
- merge state `DIRTY`
- 表示されている checks:
  - `CodeRabbit` passed
  - `GitGuardian Security Checks` passed
  - `cubic · AI code reviewer` passed

スコープ:

- `35` files
- Java、Rust、mobile、DevOps、performance、data、MLOps にまたがる
  大規模な documentation と skill/rule の拡張

評価:

- この PR はマージ準備ができていない。
- current `main` と conflict しているため、branch レベルですら mergeable ではない。
- cubic は現在の review で `35` files 中 `34` issues を特定した。
  それらの所見は substantive で technical であり、style cleanup だけではない。
  複数の新規 skill にわたる broken または misleading な example をカバーしている。
- conflict がなくても、scope が大きいため quick merge decision ではなく
  deliberate な content-fix pass が必要である。

現在の推奨:

- `Needs changes.`
- まず rebase または restack し、その後 substantive な example-quality issues を解決する。
- momentum が重要なら、1 つの very large PR を抱え続けるより domain ごとに split する。

### `#336` — Codex CLI Customization

ライブ状態:

- open
- draft
- `MERGEABLE`
- merge state `UNSTABLE`
- 表示されている checks:
  - `CodeRabbit` passed
  - `GitGuardian Security Checks` passed

スコープ:

- `scripts/codex-git-hooks/pre-commit`
- `scripts/codex-git-hooks/pre-push`
- `scripts/codex/check-codex-global-state.sh`
- `scripts/codex/install-global-git-hooks.sh`
- `scripts/sync-ecc-to-codex.sh`

評価:

- この PR は conflict しなくなったが、依然として draft-only で
  meaningful な first-party review pass はまだ行われていない。
- user-global の Codex setup 動作と git-hook installation を変更するため、
  operational blast radius は docs-only PR より高い。
- 表示されている checks は external bot のみ。
  現在の check set には full GitHub Actions run が表示されていない。
- contributor fork の `main` から来ている branch であるため、
  status 変更前に提案内容を extra sanity pass する価値がある。

現在の推奨:

- `Needs changes before merge readiness`。必要な変更は
  already-proven な code defect というより process と review 指向である:
  - manual review を完了する
  - global-state scripts の validation を run または confirm する
  - その review 完了後にのみ draft から外す

### `#420` — Laravel Skills

ライブ状態:

- open
- draft
- `MERGEABLE`
- merge state `UNSTABLE`
- 表示されている checks:
  - `CodeRabbit` passed
  - `GitGuardian Security Checks` passed

スコープ:

- `README.md`
- `examples/laravel-api-CLAUDE.md`
- `rules/php/patterns.md`
- `rules/php/security.md`
- `rules/php/testing.md`
- `skills/configure-ecc/SKILL.md`
- `skills/laravel-patterns/SKILL.md`
- `skills/laravel-security/SKILL.md`
- `skills/laravel-tdd/SKILL.md`
- `skills/laravel-verification/SKILL.md`

評価:

- content-heavy で、`#336` より operationally なリスクは低い。
- 依然として draft で、substantive な human review pass はまだ行われていない。
- 表示されている checks は external bot のみ。
- live PR state から merge blocker を示唆するものはまだないが、
  draft で under-reviewed のままであるため、単にマージできる状態ではない。

現在の推奨:

- `Review next after the highest-priority non-draft work.`
- author が draft を外す準備ができたら、good review candidate になりそうである。

## マージ可能性バケット (Mergeability Buckets)

### 今すぐ、または最終 owner pass 後にマージ可能 (Mergeable Now Or After A Final Owner Pass)

- `#292`

### マージ前に変更が必要 (Needs Changes Before Merge)

- `#298`
- `#336`

### Draft / マージ判断前にレビューが必要 (Draft / Needs Review Before Any Merge Decision)

- `#420`

### Stale `>30 Days`

- none

## 推奨順序 (Recommended Order)

1. `#292`
   これは最もクリーンな live merge candidate である。
2. `#420`
   runtime risk は低いが、draft exit と real review pass を待つ。
3. `#336`
   global Codex sync と hook 動作を変更するため、慎重に review する。
4. `#298`
   rebase し substantive な content issues を修正してから、
   さらに review time を費やす。

## 結論 (Bottom Line)

- `#399`: safe な bugfix merge。follow-up cleanup が 1 件依然として必要
- `#292`: 現在の open queue で highest-priority merge candidate
- `#298`: mergeable ではない。conflicts に加え substantive な content defects
- `#336`: conflict しなくなったが、draft のまま lightly validated なため ready ではない
- `#420`: draft、low-risk content lane。non-draft queue の後に review

## ライブ更新 (Live Refresh)

`2026-03-13T22:11:40Z` に refresh 済み。

### Main Branch

- `origin/main` は現在 green。Windows test matrix を含む。
- mainline CI repair は現在の bottleneck ではない。

### 更新されたキュー読み取り (Updated Queue Read)

#### `#292` — Governance / Config Foundation

- open
- non-draft
- `MERGEABLE`
- 表示されている checks:
  - `CodeRabbit` passed
  - `GitGuardian Security Checks` passed
- highest-signal な残作業は CI repair ではなく、
  マージ前の `.env.example` と PR-template alignment に対する small correctness pass である

現在の推奨:

- `Next actionable PR.`
- 残りの doc/config correctness issues を patch するか、
  current tradeoffs を accept するなら final owner pass を 1 回行ってマージする。

#### `#420` — Laravel Skills

- open
- draft
- `MERGEABLE`
- 表示されている checks:
  - `CodeRabbit` skipped because the PR is draft
  - `GitGuardian Security Checks` passed
- substantive な human review はまだ visible ではない

現在の推奨:

- `Review after the non-draft queue.`
- implementation risk は低いが、draft で under-reviewed のまま merge-ready ではない。

#### `#336` — Codex CLI Customization

- open
- draft
- `MERGEABLE`
- 表示されている checks:
  - `CodeRabbit` passed
  - `GitGuardian Security Checks` passed
- global Codex sync と git-hook installation 動作に触れるため、
  deliberate な manual review が依然として必要

現在の推奨:

- `Manual-review lane, not immediate merge lane.`

#### `#298` — Large Content Expansion

- open
- non-draft
- `CONFLICTING`
- キュー内で依然として hardest remaining PR

現在の推奨:

- `Last priority among current open PRs.`
- まず rebase し、その後 substantive な content/example corrections を扱う。

### 現在の順序 (Current Order)

1. `#292`
2. `#420`
3. `#336`
4. `#298`
