---
description: "自然言語でのファイル指定によるクイックコミット — コミット対象を平易な英語で記述する"
argument-hint: "[target description] (blank = all changes)"
---

# Smart Commit

> PRPs-agentic-eng by Wirasm から適応。PRP workflow シリーズの一部。

**入力**: $ARGUMENTS

---

## Phase 1 — 評価 (ASSESS)

```bash
git status --short
```

出力が空の場合 → 停止: "Nothing to commit."

追加・変更・削除・未追跡の変更内容をユーザーに要約して表示する。

---

## Phase 2 — 解釈とステージング (INTERPRET & STAGE)

`$ARGUMENTS` を解釈してステージング対象を決定する:

| 入力 | 解釈 | Git コマンド |
|---|---|---|
| *(blank / empty)* | すべてをステージ | `git add -A` |
| `staged` | 既にステージ済みのものを使用 | *(no git add)* |
| `*.ts` or `*.py` etc. | 一致する glob をステージ | `git add '*.ts'` |
| `except tests` | すべてステージ後、テストをアンステージ | `git add -A && git reset -- '**/*.test.*' '**/*.spec.*' '**/test_*' 2>/dev/null \|\| true` |
| `only new files` | 未追跡ファイルのみステージ | `git ls-files --others --exclude-standard \| grep . && git ls-files --others --exclude-standard \| xargs git add` |
| `the auth changes` | status/diff から auth 関連ファイルを解釈 | `git add <matched files>` |
| 特定のファイル名 | それらのファイルをステージ | `git add <files>` |

自然言語入力（"the auth changes" など）の場合、`git status` と `git diff` を照合して関連ファイルを特定する。ステージするファイルとその理由をユーザーに表示する。

```bash
git add <determined files>
```

ステージ後に検証:
```bash
git diff --cached --stat
```

何もステージされていない場合 → 停止: "No files matched your description."

---

## Phase 3 — コミット (COMMIT)

命令形の 1 行コミットメッセージを作成する:

```
{type}: {description}
```

Types:
- `feat` — 新機能または新しい capability
- `fix` — バグ修正
- `refactor` — 挙動を変えないコード再構成
- `docs` — ドキュメント変更
- `test` — テストの追加または更新
- `chore` — build、config、依存関係
- `perf` — パフォーマンス改善
- `ci` — CI/CD 変更

Rules:
- 命令形（"add feature" であり "added feature" ではない）
- type プレフィックスの後は小文字
- 末尾にピリオドなし
- 72 文字以内
- HOW ではなく WHAT が変わったかを記述

```bash
git commit -m "{type}: {description}"
```

---

## Phase 4 — 出力 (OUTPUT)

ユーザーに報告:

```
Committed: {hash_short}
Message:   {type}: {description}
Files:     {count} file(s) changed

Next steps:
  - git push           → push to remote
  - /prp-pr            → create a pull request
  - /code-review       → review before pushing
```

---

## 例 (Examples)

| 入力 | 動作 |
|---|---|
| `/prp-commit` | すべてをステージし、メッセージを自動生成 |
| `/prp-commit staged` | 既にステージ済みのもののみコミット |
| `/prp-commit *.ts` | すべての TypeScript ファイルをステージしてコミット |
| `/prp-commit except tests` | テストファイル以外をすべてステージ |
| `/prp-commit the database migration` | status から DB migration ファイルを特定してステージ |
| `/prp-commit only new files` | 未追跡ファイルのみステージ |
