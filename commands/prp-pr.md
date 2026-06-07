---
description: "未 push のコミットがある現在のブランチから GitHub PR を作成 — テンプレートを探索し、変更を分析して push する"
argument-hint: "[base-branch] (default: main)"
---

# Pull Request の作成 (Create Pull Request)

> PRPs-agentic-eng by Wirasm から適応。PRP workflow シリーズの一部。

**入力**: `$ARGUMENTS` — 任意。base branch 名やフラグ（例: `--draft`）を含む場合がある。

**`$ARGUMENTS` の解析**:
- 認識されたフラグ（`--draft`）を抽出
- 残りの非フラグテキストを base branch 名として扱う
- 指定がなければ base branch のデフォルトは `main`

---

## Phase 1 — 検証 (VALIDATE)

前提条件を確認する:

```bash
git branch --show-current
git status --short
git log origin/<base>..HEAD --oneline
```

| チェック | 条件 | 失敗時の対応 |
|---|---|---|
| base branch 上にいない | 現在の branch ≠ base | 停止: "Switch to a feature branch first." |
| 作業ディレクトリがクリーン | 未コミット変更なし | 警告: "You have uncommitted changes. Commit or stash first. Use `/prp-commit` to commit." |
| 先行コミットがある | `git log origin/<base>..HEAD` が空でない | 停止: "No commits ahead of `<base>`. Nothing to PR." |
| 既存 PR がない | `gh pr list --head <branch> --json number` が空 | 停止: "PR already exists: #<number>. Use `gh pr view <number> --web` to open it." |

すべてのチェックを通過したら続行する。

---

## Phase 2 — 探索 (DISCOVER)

### PR テンプレート (PR Template)

以下の順で PR テンプレートを検索する:

1. `.github/PULL_REQUEST_TEMPLATE/` ディレクトリ — 存在する場合はファイルを一覧し、ユーザーに選択させる（または `default.md` を使用）
2. `.github/PULL_REQUEST_TEMPLATE.md`
3. `.github/pull_request_template.md`
4. `docs/pull_request_template.md`

見つかった場合は読み込み、その構造で PR body を作成する。

### コミット分析 (Commit Analysis)

```bash
git log origin/<base>..HEAD --format="%h %s" --reverse
```

コミットを分析して以下を決定する:
- **PR title**: type プレフィックス付きの conventional commit 形式 — `feat: ...`, `fix: ...` など
  - 複数 type がある場合は支配的なものを使用
  - 単一コミットの場合はそのメッセージをそのまま使用
- **Change summary**: type/領域ごとにコミットをグループ化

### ファイル分析 (File Analysis)

```bash
git diff origin/<base>..HEAD --stat
git diff origin/<base>..HEAD --name-only
```

変更ファイルを分類する: source, tests, docs, config, migrations。

### PRP アーティファクト (PRP Artifacts)

関連 PRP アーティファクトを確認する:
- `.claude/PRPs/reports/` — Implementation reports
- `.claude/PRPs/plans/` — 実行された plans
- `.claude/PRPs/prds/` — 関連 PRDs

存在する場合は PR body で参照する。

---

## Phase 3 — PUSH

```bash
git push -u origin HEAD
```

divergence により push が失敗した場合:
```bash
git fetch origin
git rebase origin/<base>
git push -u origin HEAD
```

rebase でコンフリクトが発生した場合は停止し、ユーザーに通知する。

---

## Phase 4 — 作成 (CREATE)

### テンプレートあり (With Template)

Phase 2 で PR テンプレートが見つかった場合、コミットとファイル分析を使って各セクションを埋める。すべてのテンプレートセクションを保持する — 該当しない場合は削除せず "N/A" とする。

### テンプレートなし (Without Template)

以下のデフォルト形式を使用する:

```markdown
## Summary

<1-2 sentence description of what this PR does and why>

## Changes

<bulleted list of changes grouped by area>

## Files Changed

<table or list of changed files with change type: Added/Modified/Deleted>

## Testing

<description of how changes were tested, or "Needs testing">

## Related Issues

<linked issues with Closes/Fixes/Relates to #N, or "None">
```

### PR の作成 (Create the PR)

```bash
gh pr create \
  --title "<PR title>" \
  --base <base-branch> \
  --body "<PR body>"
  # Add --draft if the --draft flag was parsed from $ARGUMENTS
```

---

## Phase 5 — 検証 (VERIFY)

```bash
gh pr view --json number,url,title,state,baseRefName,headRefName,additions,deletions,changedFiles
gh pr checks --json name,status,conclusion 2>/dev/null || true
```

---

## Phase 6 — 出力 (OUTPUT)

ユーザーに報告:

```
PR #<number>: <title>
URL: <url>
Branch: <head> → <base>
Changes: +<additions> -<deletions> across <changedFiles> files

CI Checks: <status summary or "pending" or "none configured">

Artifacts referenced:
  - <any PRP reports/plans linked in PR body>

Next steps:
  - gh pr view <number> --web   → open in browser
  - /code-review <number>       → review the PR
  - gh pr merge <number>        → merge when ready
```

---

## エッジケース (Edge Cases)

- **`gh` CLI なし**: 停止: "GitHub CLI (`gh`) is required. Install: <https://cli.github.com/>"
- **未認証**: 停止: "Run `gh auth login` first."
- **force push が必要**: remote が diverge し rebase 済みの場合は `git push --force-with-lease` を使用（`--force` は使わない）
- **複数 PR テンプレート**: `.github/PULL_REQUEST_TEMPLATE/` に複数ファイルがある場合は一覧し、ユーザーに選択させる
- **大きな PR（>20 ファイル）**: PR サイズについて警告。変更が論理的に分離可能なら分割を提案する
