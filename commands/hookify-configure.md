---
description: hookify rule をインタラクティブに有効化または無効化します。
---

既存の hookify rule をインタラクティブに enable または disable します。

## ステップ (Steps)

1. すべての `.claude/hookify.*.local.md` file を検索
2. 各 rule の現在の state を読み取り
3. 現在の enabled / disabled status 付きで list を提示
4. どの rule を toggle するか質問
5. 選択された rule file の `enabled:` field を更新
6. change を confirm
