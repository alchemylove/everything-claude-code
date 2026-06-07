---
name: projects
description: 既知のプロジェクトとその instinct 統計を一覧表示する
command: true
---

# Projects コマンド (Projects Command)

continuous-learning-v2 向けに、プロジェクト registry エントリとプロジェクトごとの instinct/observation 件数を一覧表示する。

## 実装 (Implementation)

plugin root パスを使って instinct CLI を実行する:

```bash
python3 "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/scripts/instinct-cli.py" projects
```

`CLAUDE_PLUGIN_ROOT` が未設定の場合（手動インストール）:

```bash
python3 ~/.claude/skills/continuous-learning-v2/scripts/instinct-cli.py projects
```

## 使い方 (Usage)

```bash
/projects
```

## 実行内容 (What to Do)

1. `~/.claude/homunculus/projects.json` を読む
2. 各プロジェクトについて表示する:
   - プロジェクト名、id、root、remote
   - personal と inherited の instinct 件数
   - observation event 件数
   - 最終確認タイムスタンプ
3. グローバル instinct 合計も表示する
