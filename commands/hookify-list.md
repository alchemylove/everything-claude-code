---
description: 設定済みのすべての hookify rule を一覧表示します。
---

すべての hookify rule を検索し、formatted table で表示します。

## ステップ (Steps)

1. すべての `.claude/hookify.*.local.md` file を検索
2. 各 file の frontmatter を読み取り:
   - `name`
   - `enabled`
   - `event`
   - `action`
   - `pattern`
3. table として表示:

| Rule | Enabled | Event | Pattern | File |
|------|---------|-------|---------|------|

4. rule count を表示し、`/hookify-configure` で後から state を変更できることを remind。
