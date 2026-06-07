---
description: 厳密な validation loop 付きで実装計画を実行する
argument-hint: <path/to/plan.md>
---

> PRPs-agentic-eng by Wirasm から適応。PRP workflow シリーズの一部。

# PRP Implement

継続的な validation とともに plan ファイルをステップごとに実行する。すべての変更は直後に検証する — 壊れた状態を蓄積しない。

**Core Philosophy**: Validation loop はミスを早期に捉える。変更のたびにチェックを実行し、問題は即座に修正する。

**Golden Rule**: validation が失敗したら、先に進む前に修正する。壊れた状態を蓄積しない。

---

## Phase 0 — 検出 (DETECT)

### Package Manager 検出 (Package Manager Detection)

| File Exists | Package Manager | Runner |
|---|---|---|
| `bun.lockb` | bun | `bun run` |
| `pnpm-lock.yaml` | pnpm | `pnpm run` |
| `yarn.lock` | yarn | `yarn` |
| `package-lock.json` | npm | `npm run` |
| `pyproject.toml` or `requirements.txt` | uv / pip | `uv run` or `python -m` |
| `Cargo.toml` | cargo | `cargo` |
| `go.mod` | go | `go` |

### Validation Scripts

`package.json`（または同等物）で利用可能な scripts を確認する:

```bash
# For Node.js projects
cat package.json | grep -A 20 '"scripts"'
```

type-check、lint、test、build 用の利用可能なコマンドを記録する。

---

## Phase 1 — 読み込み (LOAD)

plan ファイルを読む:

```bash
cat "$ARGUMENTS"
```

plan から以下のセクションを抽出する:
- **Summary** — 何を構築するか
- **Patterns to Mirror** — 従うべきコード規約
- **Files to Change** — 作成または変更するもの
- **Step-by-Step Tasks** — 実装シーケンス
- **Validation Commands** — 正しさの検証方法
- **Acceptance Criteria** — 完了の定義

ファイルが存在しない、または有効な plan でない場合:
```
Error: Plan file not found or invalid.
Run /prp-plan <feature-description> to create a plan first.
```

**CHECKPOINT**: Plan loaded. All sections identified. Tasks extracted.

---

## Phase 2 — 準備 (PREPARE)

### Git State

```bash
git branch --show-current
git status --porcelain
```

### Branch Decision

| Current State | Action |
|---|---|
| On feature branch | Use current branch |
| On main, clean working tree | Create feature branch: `git checkout -b feat/{plan-name}` |
| On main, dirty working tree | **STOP** — Ask user to stash or commit first |
| In a git worktree for this feature | Use the worktree |

### Sync Remote

```bash
git pull --rebase origin $(git branch --show-current) 2>/dev/null || true
```

**CHECKPOINT**: On correct branch. Working tree ready. Remote synced.

---

## Phase 3 — 実行 (EXECUTE)

plan の各 task を順に処理する。

### Per-Task Loop

**Step-by-Step Tasks** の各 task について:

1. **MIRROR 参照を読む** — task の MIRROR フィールドで参照される pattern ファイルを開く。コードを書く前に規約を理解する。

2. **実装** — pattern に正確に従ってコードを書く。GOTCHA 警告を適用する。指定された IMPORTS を使用する。

3. **即座に検証** — ファイル変更のたびに:
   ```bash
   # Run type-check (adjust command per project)
   [type-check command from Phase 0]
   ```
   type-check が失敗したら → 次のファイルに進む前にエラーを修正する。

4. **進捗を記録** — ログ: `[done] Task N: [task name] — complete`

### 逸脱の扱い (Handling Deviations)

実装が plan から逸脱する必要がある場合:
- **WHAT** が変わったかを記録
- **WHY** 変わったかを記録
- 修正したアプローチで続行
- これらの逸脱はレポートに記録される

**CHECKPOINT**: All tasks executed. Deviations logged.

---

## Phase 4 — 検証 (VALIDATE)

plan のすべての validation level を実行する。各 level で問題を修正してから次へ進む。

### Level 1: Static Analysis

```bash
# Type checking — zero errors required
[project type-check command]

# Linting — fix automatically where possible
[project lint command]
[project lint-fix command]
```

auto-fix 後も lint エラーが残る場合は手動で修正する。

### Level 2: Unit Tests

plan の Testing Strategy で指定されたとおり、すべての新規関数にテストを書く。

```bash
[project test command for affected area]
```

- すべての関数に少なくとも 1 つのテストが必要
- plan に記載された edge case をカバー
- テストが失敗したら → 実装を修正する（テストが誤りでない限り、テストは修正しない）

### Level 3: Build Check

```bash
[project build command]
```

build はエラー 0 で成功する必要がある。

### Level 4: Integration Testing (if applicable)

```bash
# Start server, run tests, stop server
[project dev server command] &
SERVER_PID=$!

# Wait for server to be ready (adjust port as needed)
SERVER_READY=0
for i in $(seq 1 30); do
  if curl -sf http://localhost:PORT/health >/dev/null 2>&1; then
    SERVER_READY=1
    break
  fi
  sleep 1
done

if [ "$SERVER_READY" -ne 1 ]; then
  kill "$SERVER_PID" 2>/dev/null || true
  echo "ERROR: Server failed to start within 30s" >&2
  exit 1
fi

[integration test command]
TEST_EXIT=$?

kill "$SERVER_PID" 2>/dev/null || true
wait "$SERVER_PID" 2>/dev/null || true

exit "$TEST_EXIT"
```

### Level 5: Edge Case Testing

plan の Testing Strategy チェックリストの edge case を実行する。

**CHECKPOINT**: All 5 validation levels pass. Zero errors.

---

## Phase 5 — レポート (REPORT)

### Implementation Report の作成

```bash
mkdir -p .claude/PRPs/reports
```

レポートを `.claude/PRPs/reports/{plan-name}-report.md` に書く:

```markdown
# Implementation Report: [Feature Name]

## Summary
[What was implemented]

## Assessment vs Reality

| Metric | Predicted (Plan) | Actual |
|---|---|---|
| Complexity | [from plan] | [actual] |
| Confidence | [from plan] | [actual] |
| Files Changed | [from plan] | [actual count] |

## Tasks Completed

| # | Task | Status | Notes |
|---|---|---|---|
| 1 | [task name] | [done] Complete | |
| 2 | [task name] | [done] Complete | Deviated — [reason] |

## Validation Results

| Level | Status | Notes |
|---|---|---|
| Static Analysis | [done] Pass | |
| Unit Tests | [done] Pass | N tests written |
| Build | [done] Pass | |
| Integration | [done] Pass | or N/A |
| Edge Cases | [done] Pass | |

## Files Changed

| File | Action | Lines |
|---|---|---|
| `path/to/file` | CREATED | +N |
| `path/to/file` | UPDATED | +N / -M |

## Deviations from Plan
[List any deviations with WHAT and WHY, or "None"]

## Issues Encountered
[List any problems and how they were resolved, or "None"]

## Tests Written

| Test File | Tests | Coverage |
|---|---|---|
| `path/to/test` | N tests | [area covered] |

## Next Steps
- [ ] Code review via `/code-review`
- [ ] Create PR via `/prp-pr`
```

### PRD の更新（該当する場合）

この実装が PRD phase 向けの場合:
1. phase status を `in-progress` から `complete` に更新
2. レポートパスを参照として追加

### Plan のアーカイブ

```bash
mkdir -p .claude/PRPs/plans/completed
mv "$ARGUMENTS" .claude/PRPs/plans/completed/
```

**CHECKPOINT**: Report created. PRD updated. Plan archived.

---

## Phase 6 — 出力 (OUTPUT)

ユーザーに報告:

```
## Implementation Complete

- **Plan**: [plan file path] → archived to completed/
- **Branch**: [current branch name]
- **Status**: [done] All tasks complete

### Validation Summary

| Check | Status |
|---|---|
| Type Check | [done] |
| Lint | [done] |
| Tests | [done] (N written) |
| Build | [done] |
| Integration | [done] or N/A |

### Files Changed
- [N] files created, [M] files updated

### Deviations
[Summary or "None — implemented exactly as planned"]

### Artifacts
- Report: `.claude/PRPs/reports/{name}-report.md`
- Archived Plan: `.claude/PRPs/plans/completed/{name}.plan.md`

### PRD Progress (if applicable)
| Phase | Status |
|---|---|
| Phase 1 | [done] Complete |
| Phase 2 | [next] |
| ... | ... |

> Next step: Run `/prp-pr` to create a pull request, or `/code-review` to review changes first.
```

---

## 失敗の扱い (Handling Failures)

### Type Check が失敗
1. エラーメッセージを注意深く読む
2. ソースファイルの型エラーを修正
3. type-check を再実行
4. クリーンになるまで続行しない

### Tests が失敗
1. バグが実装側かテスト側かを特定
2. 根本原因を修正（通常は実装）
3. テストを再実行
4. green になるまで続行しない

### Lint が失敗
1. まず auto-fix を実行
2. エラーが残る場合は手動で修正
3. lint を再実行
4. クリーンになるまで続行しない

### Build が失敗
1. 通常は型または import の問題 — エラーメッセージを確認
2. 問題のあるファイルを修正
3. build を再実行
4. 成功するまで続行しない

### Integration Test が失敗
1. server が正しく起動したか確認
2. endpoint/route が存在するか確認
3. リクエスト形式が期待と一致するか確認
4. 修正して再実行

---

## 成功基準 (Success Criteria)

- **TASKS_COMPLETE**: plan のすべての task を実行済み
- **TYPES_PASS**: 型エラー 0
- **LINT_PASS**: lint エラー 0
- **TESTS_PASS**: すべてのテストが green、新規テストを作成済み
- **BUILD_PASS**: build 成功
- **REPORT_CREATED**: implementation report を保存済み
- **PLAN_ARCHIVED**: plan を `completed/` に移動済み

---

## 次のステップ (Next Steps)

- コミット前に `/code-review` で変更をレビュー
- 説明的なメッセージで `/prp-commit` を実行
- `/prp-pr` で pull request を作成
- PRD にさらに phase がある場合は `/prp-plan <next-phase>` を実行
