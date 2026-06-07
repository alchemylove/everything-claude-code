---
description: コードレビュー — ローカルの未コミット変更、または GitHub PR（PR モードでは PR 番号/URL を渡す）
argument-hint: [pr-number | pr-url | blank for local review]
---

# コードレビュー (Code Review)

> PR レビューモードは Wirasm による PRPs-agentic-eng から適応。PRP ワークフローシリーズの一部。

**Input**: $ARGUMENTS

---

## モード選択 (Mode Selection)

`$ARGUMENTS` に PR 番号、PR URL、または `--pr` が含まれる場合:
→ 下記の **PR Review Mode** へ進む。

それ以外:
→ **Local Review Mode** を使用する。

---

## ローカルレビューモード (Local Review Mode)

未コミット変更の包括的なセキュリティ・品質レビュー。

### フェーズ 1 — 収集 (Phase 1 — GATHER)

```bash
git diff --name-only HEAD
```

変更ファイルがなければ停止: "Nothing to review."

### フェーズ 2 — レビュー (Phase 2 — REVIEW)

変更された各ファイルを全文読む。以下を確認:

**Security Issues (CRITICAL):**
- ハードコードされた認証情報、API キー、トークン
- SQL インジェクションの脆弱性
- XSS の脆弱性
- 入力検証の欠如
- 安全でない依存関係
- パストラバーサルのリスク

**Code Quality (HIGH):**
- 50 行を超える関数
- 800 行を超えるファイル
- ネスト深度 4 レベル超
- エラーハンドリングの欠如
- `console.log` 文
- TODO/FIXME コメント
- 公開 API の JSDoc 欠如

**Best Practices (MEDIUM):**
- ミューテーションパターン（代わりに immutable を使用）
- コード/コメント内の絵文字使用
- 新規コードのテスト欠如
- アクセシビリティの問題（a11y）

### フェーズ 3 — レポート (Phase 3 — REPORT)

以下を含むレポートを生成:
- Severity: CRITICAL, HIGH, MEDIUM, LOW
- ファイル位置と行番号
- 問題の説明
- 修正提案

CRITICAL または HIGH の問題がある場合はコミットをブロックする。
セキュリティ脆弱性のあるコードは決して承認しない。

---

## PR レビューモード (PR Review Mode)

包括的な GitHub PR レビュー — diff を取得し、ファイル全文を読み、検証を実行し、レビューを投稿する。

### フェーズ 1 — 取得 (Phase 1 — FETCH)

入力を解析して PR を特定:

| Input | Action |
|---|---|
| Number (e.g. `42`) | PR 番号として使用 |
| URL (`github.com/.../pull/42`) | PR 番号を抽出 |
| Branch name | `gh pr list --head <branch>` で PR を検索 |

```bash
gh pr view <NUMBER> --json number,title,body,author,baseRefName,headRefName,changedFiles,additions,deletions
gh pr diff <NUMBER>
```

PR が見つからない場合はエラーで停止。後続フェーズ用に PR メタデータを保存する。

### フェーズ 2 — コンテキスト (Phase 2 — CONTEXT)

レビューコンテキストを構築:

1. **Project rules** — `CLAUDE.md`、`.claude/docs/`、およびコントリビューションガイドラインを読む
2. **Planning artifacts** — この PR に関連するコンテキストのため `.claude/prds/`、`.claude/plans/`、`.claude/reviews/`、およびレガシー `.claude/PRPs/{prds,plans,reports,reviews}/` を確認
3. **PR intent** — 目標、リンクされた issue、テストプランのため PR 説明を解析
4. **Changed files** — 変更された全ファイルを一覧し、種別（source, test, config, docs）で分類

### フェーズ 3 — レビュー (Phase 3 — REVIEW)

変更された各ファイルを **全文** 読む（diff ハンクだけでなく — 周辺コンテキストが必要）。

PR レビューでは、PR head リビジョンのファイル全文を取得:
```bash
gh pr diff <NUMBER> --name-only | while IFS= read -r file; do
  gh api "repos/{owner}/{repo}/contents/$file?ref=<head-branch>" --jq '.content' | base64 -d
done
```

7 カテゴリにわたるレビューチェックリストを適用:

| Category | What to Check |
|---|---|
| **Correctness** | ロジックエラー、オフバイワン、null 処理、エッジケース、競合状態 |
| **Type Safety** | 型不一致、安全でないキャスト、`any` 使用、ジェネリクス欠如 |
| **Pattern Compliance** | プロジェクト規約との一致（命名、ファイル構造、エラーハンドリング、import） |
| **Security** | インジェクション、認証ギャップ、シークレット露出、SSRF、パストラバーサル、XSS |
| **Performance** | N+1 クエリ、インデックス欠如、無制限ループ、メモリリーク、大きなペイロード |
| **Completeness** | テスト欠如、エラーハンドリング欠如、不完全なマイグレーション、ドキュメント欠如 |
| **Maintainability** | デッドコード、マジックナンバー、深いネスト、不明瞭な命名、型欠如 |

各所見に severity を割り当て:

| Severity | Meaning | Action |
|---|---|---|
| **CRITICAL** | セキュリティ脆弱性またはデータ損失リスク | マージ前に必須修正 |
| **HIGH** | 問題を引き起こしうるバグまたはロジックエラー | マージ前に修正推奨 |
| **MEDIUM** | コード品質の問題またはベストプラクティス欠如 | 修正推奨 |
| **LOW** | スタイル上の指摘または軽微な提案 | 任意 |

### フェーズ 4 — 検証 (Phase 4 — VALIDATE)

利用可能な検証コマンドを実行:

設定ファイル（`package.json`、`Cargo.toml`、`go.mod`、`pyproject.toml` など）からプロジェクト種別を検出し、適切なコマンドを実行:

**Node.js / TypeScript** (`package.json` がある場合):
```bash
npm run typecheck 2>/dev/null || npx tsc --noEmit 2>/dev/null  # Type check
npm run lint                                                    # Lint
npm test                                                        # Tests
npm run build                                                   # Build
```

**Rust** (`Cargo.toml` がある場合):
```bash
cargo clippy -- -D warnings  # Lint
cargo test                   # Tests
cargo build                  # Build
```

**Go** (`go.mod` がある場合):
```bash
go vet ./...    # Lint
go test ./...   # Tests
go build ./...  # Build
```

**Python** (`pyproject.toml` / `setup.py` がある場合):
```bash
pytest  # Tests
```

検出されたプロジェクト種別に該当するコマンドのみ実行。各コマンドの pass/fail を記録。

### フェーズ 5 — 判定 (Phase 5 — DECIDE)

所見に基づき推奨を決定:

| Condition | Decision |
|---|---|
| CRITICAL/HIGH 問題ゼロ、検証成功 | **APPROVE** |
| MEDIUM/LOW のみ、検証成功 | コメント付き **APPROVE** |
| HIGH 問題または検証失敗あり | **REQUEST CHANGES** |
| CRITICAL 問題あり | **BLOCK** — マージ前に必須修正 |

特殊ケース:
- Draft PR → 常に **COMMENT**（approve/block しない）
- docs/config のみの変更 → 軽いレビュー、正確性に焦点
- 明示的な `--approve` または `--request-changes` フラグ → 判定を上書き（ただし全所見は報告）

### フェーズ 6 — レポート (Phase 6 — REPORT)

リポジトリがレガシー `.claude/PRPs/reviews/` をこのワークストリームで既に使用していない限り、`.claude/reviews/pr-<NUMBER>-review.md` にレビュー成果物を作成:

```markdown
# PR Review: #<NUMBER> — <TITLE>

**Reviewed**: <date>
**Author**: <author>
**Branch**: <head> → <base>
**Decision**: APPROVE | REQUEST CHANGES | BLOCK

## Summary
<1-2 sentence overall assessment>

## Findings

### CRITICAL
<findings or "None">

### HIGH
<findings or "None">

### MEDIUM
<findings or "None">

### LOW
<findings or "None">

## Validation Results

| Check | Result |
|---|---|
| Type check | Pass / Fail / Skipped |
| Lint | Pass / Fail / Skipped |
| Tests | Pass / Fail / Skipped |
| Build | Pass / Fail / Skipped |

## Files Reviewed
<list of files with change type: Added/Modified/Deleted>
```

### フェーズ 7 — 公開 (Phase 7 — PUBLISH)

GitHub にレビューを投稿:

```bash
# If APPROVE
gh pr review <NUMBER> --approve --body "<summary of review>"

# If REQUEST CHANGES
gh pr review <NUMBER> --request-changes --body "<summary with required fixes>"

# If COMMENT only (draft PR or informational)
gh pr review <NUMBER> --comment --body "<summary>"
```

特定行へのインラインコメントには GitHub review comments API を使用:
```bash
gh api "repos/{owner}/{repo}/pulls/<NUMBER>/comments" \
  -f body="<comment>" \
  -f path="<file>" \
  -F line=<line-number> \
  -f side="RIGHT" \
  -f commit_id="$(gh pr view <NUMBER> --json headRefOid --jq .headRefOid)"
```

または、複数のインラインコメントを一度に投稿:
```bash
gh api "repos/{owner}/{repo}/pulls/<NUMBER>/reviews" \
  -f event="COMMENT" \
  -f body="<overall summary>" \
  --input comments.json  # [{"path": "file", "line": N, "body": "comment"}, ...]
```

### フェーズ 8 — 出力 (Phase 8 — OUTPUT)

ユーザーに報告:

```
PR #<NUMBER>: <TITLE>
Decision: <APPROVE|REQUEST_CHANGES|BLOCK>

Issues: <critical_count> critical, <high_count> high, <medium_count> medium, <low_count> low
Validation: <pass_count>/<total_count> checks passed

Artifacts:
  Review: .claude/reviews/pr-<NUMBER>-review.md
  GitHub: <PR URL>

Next steps:
  - <contextual suggestions based on decision>
```

---

## エッジケース (Edge Cases)

- **`gh` CLI なし**: ローカルのみのレビューにフォールバック（diff を読み、GitHub 公開はスキップ）。ユーザーに警告。
- **分岐したブランチ**: レビュー前に `git fetch origin && git rebase origin/<base>` を提案。
- **大きな PR（50 ファイル超）**: レビュー範囲について警告。まずソース変更、次にテスト、最後に config/docs に焦点。
