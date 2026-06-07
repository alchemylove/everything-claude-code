---
name: instinct-import
description: ファイルまたは URL から instincts を project/global スコープにインポートする
command: true
---

# Instinct インポートコマンド (Instinct Import Command)

## 実装 (Implementation)

プラグインルートパスを使用して instinct CLI を実行します:

```bash
python3 "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/scripts/instinct-cli.py" import <file-or-url> [--dry-run] [--force] [--min-confidence 0.7] [--scope project|global]
```

または `CLAUDE_PLUGIN_ROOT` が未設定の場合（手動インストール）:

```bash
python3 ~/.claude/skills/continuous-learning-v2/scripts/instinct-cli.py import <file-or-url>
```

ローカルファイルパスまたは HTTP(S) URL から instincts をインポートします。

## 使い方 (Usage)

```
/instinct-import team-instincts.yaml
/instinct-import https://github.com/org/repo/instincts.yaml
/instinct-import team-instincts.yaml --dry-run
/instinct-import team-instincts.yaml --scope global --force
```

## 実行内容 (What to Do)

1. instinct ファイルを取得する（ローカルパスまたは URL）
2. 形式を解析して検証する
3. 既存 instincts との重複をチェックする
4. 新しい instincts をマージまたは追加する
5. inherited instincts ディレクトリに保存する:
   - Project scope: `~/.claude/homunculus/projects/<project-id>/instincts/inherited/`
   - Global scope: `~/.claude/homunculus/instincts/inherited/`

## インポートプロセス (Import Process)

```
 Importing instincts from: team-instincts.yaml
================================================

Found 12 instincts to import.

Analyzing conflicts...

## New Instincts (8)
These will be added:
  ✓ use-zod-validation (confidence: 0.7)
  ✓ prefer-named-exports (confidence: 0.65)
  ✓ test-async-functions (confidence: 0.8)
  ...

## Duplicate Instincts (3)
Already have similar instincts:
  WARNING: prefer-functional-style
     Local: 0.8 confidence, 12 observations
     Import: 0.7 confidence
     → Keep local (higher confidence)

  WARNING: test-first-workflow
     Local: 0.75 confidence
     Import: 0.9 confidence
     → Update to import (higher confidence)

Import 8 new, update 1?
```

## マージ動作 (Merge Behavior)

既存 ID の instinct をインポートする場合:
- より高い信頼度の import が更新候補になる
- 同等/より低い信頼度の import はスキップされる
- `--force` 未使用時はユーザー確認が必要

## ソース追跡 (Source Tracking)

インポートされた instincts は以下のようにマークされます:
```yaml
source: inherited
scope: project
imported_from: "team-instincts.yaml"
project_id: "a1b2c3d4e5f6"
project_name: "my-project"
```

## フラグ (Flags)

- `--dry-run`: インポートせずにプレビュー
- `--force`: 確認プロンプトをスキップ
- `--min-confidence <n>`: 閾値以上の instincts のみをインポート
- `--scope <project|global>`: 対象スコープを選択（デフォルト: `project`）

## 出力 (Output)

インポート後:
```
PASS: Import complete!

Added: 8 instincts
Updated: 1 instinct
Skipped: 3 instincts (equal/higher confidence already exists)

New instincts saved to: ~/.claude/homunculus/instincts/inherited/

Run /instinct-status to see all instincts.
```
