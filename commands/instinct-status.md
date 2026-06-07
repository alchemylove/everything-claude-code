---
name: instinct-status
description: 学習済み instincts（project + global）を信頼度付きで表示する
command: true
---

# Instinct ステータスコマンド (Instinct Status Command)

現在のプロジェクトと global instincts の学習済み instincts を、ドメインごとにグループ化して表示します。

## 実装 (Implementation)

プラグインルートパスを使用して instinct CLI を実行します:

```bash
python3 "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/scripts/instinct-cli.py" status
```

または `CLAUDE_PLUGIN_ROOT` が未設定の場合（手動インストール）は:

```bash
python3 ~/.claude/skills/continuous-learning-v2/scripts/instinct-cli.py status
```

## 使い方 (Usage)

```
/instinct-status
```

## 実行内容 (What to Do)

1. 現在のプロジェクトコンテキストを検出する（git remote/path hash）
2. `~/.claude/homunculus/projects/<project-id>/instincts/` から project instincts を読む
3. `~/.claude/homunculus/instincts/` から global instincts を読む
4. 優先ルールでマージする（ID が衝突する場合は project が global を上書き）
5. ドメインごとに信頼度バーと観察統計付きで表示する

## 出力形式 (Output Format)

```
============================================================
  INSTINCT STATUS - 12 total
============================================================

  Project: my-app (a1b2c3d4e5f6)
  Project instincts: 8
  Global instincts:  4

## PROJECT-SCOPED (my-app)
  ### WORKFLOW (3)
    ███████░░░  70%  grep-before-edit [project]
              trigger: when modifying code

## GLOBAL (apply to all projects)
  ### SECURITY (2)
    █████████░  85%  validate-user-input [global]
              trigger: when handling user input
```
