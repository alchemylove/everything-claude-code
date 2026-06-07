---
name: prune
description: 30 日以上経過し、一度も昇格されなかった pending instinct を削除する
command: true
---

# Pending Instinct の整理 (Prune Pending Instincts)

自動生成されたがレビューまたは昇格されなかった、期限切れの pending instinct を削除する。

## 実装 (Implementation)

plugin root パスを使って instinct CLI を実行する:

```bash
python3 "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/scripts/instinct-cli.py" prune
```

`CLAUDE_PLUGIN_ROOT` が未設定の場合（手動インストール）:

```bash
python3 ~/.claude/skills/continuous-learning-v2/scripts/instinct-cli.py prune
```

## 使い方 (Usage)

```
/prune                    # 30 日より古い instinct を削除
/prune --max-age 60      # カスタム経過日数しきい値（日）
/prune --dry-run         # 削除せずプレビュー
```
