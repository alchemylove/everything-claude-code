---
name: promote
description: プロジェクトスコープの instinct をグローバルスコープへ昇格する
command: true
---

# Promote コマンド (Promote Command)

continuous-learning-v2 で、プロジェクトスコープの instinct をグローバルスコープへ昇格する。

## 実装 (Implementation)

plugin root パスを使って instinct CLI を実行する:

```bash
python3 "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/scripts/instinct-cli.py" promote [instinct-id] [--force] [--dry-run]
```

`CLAUDE_PLUGIN_ROOT` が未設定の場合（手動インストール）:

```bash
python3 ~/.claude/skills/continuous-learning-v2/scripts/instinct-cli.py promote [instinct-id] [--force] [--dry-run]
```

## 使い方 (Usage)

```bash
/promote                      # 昇格候補を自動検出
/promote --dry-run            # 自動昇格候補をプレビュー
/promote --force              # 確認なしで条件を満たす候補をすべて昇格
/promote grep-before-edit     # 現在のプロジェクトから特定の instinct を 1 件昇格
```

## 実行内容 (What to Do)

1. 現在のプロジェクトを検出する
2. `instinct-id` が指定されている場合、その instinct のみ昇格する（現在のプロジェクトに存在する場合）
3. それ以外は、以下を満たす cross-project 候補を探す:
   - 少なくとも 2 つのプロジェクトに出現する
   - confidence 閾値を満たす
4. 昇格した instinct を `~/.claude/homunculus/instincts/personal/` に `scope: global` で書き込む
