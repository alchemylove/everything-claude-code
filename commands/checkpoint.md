---
description: 検証チェック実行後にワークフローの checkpoint を作成、検証、または一覧表示する。
---

# Checkpoint コマンド (Checkpoint Command)

ワークフローで checkpoint を作成または検証する。

## 使い方 (Usage)

`/checkpoint [create|verify|list] [name]`

## Checkpoint の作成 (Create Checkpoint)

checkpoint 作成時:

1. `/verify quick` を実行し、現在の状態がクリーンであることを確認する
2. checkpoint 名で git stash または commit を作成する
3. `.claude/checkpoints.log` に checkpoint を記録する:

```bash
echo "$(date +%Y-%m-%d-%H:%M) | $CHECKPOINT_NAME | $(git rev-parse --short HEAD)" >> .claude/checkpoints.log
```

4. checkpoint 作成を報告する

## Checkpoint の検証 (Verify Checkpoint)

checkpoint との照合時:

1. ログから checkpoint を読む
2. 現在の状態と checkpoint を比較する:
   - checkpoint 以降に追加されたファイル
   - checkpoint 以降に変更されたファイル
   - 当時と比べたテスト合格率
   - 当時と比べたカバレッジ

3. 報告:
```
CHECKPOINT COMPARISON: $NAME
============================
Files changed: X
Tests: +Y passed / -Z failed
Coverage: +X% / -Y%
Build: [PASS/FAIL]
```

## Checkpoint 一覧 (List Checkpoints)

すべての checkpoint を次で表示する:
- 名前
- タイムスタンプ
- Git SHA
- ステータス（current, behind, ahead）

## ワークフロー (Workflow)

典型的な checkpoint フロー:

```
[Start] --> /checkpoint create "feature-start"
   |
[Implement] --> /checkpoint create "core-done"
   |
[Test] --> /checkpoint verify "core-done"
   |
[Refactor] --> /checkpoint create "refactor-done"
   |
[PR] --> /checkpoint verify "feature-start"
```

## 引数 (Arguments)

$ARGUMENTS:
- `create <name>` - 名前付き checkpoint を作成
- `verify <name>` - 名前付き checkpoint と照合
- `list` - すべての checkpoint を表示
- `clear` - 古い checkpoint を削除（直近5件を保持）
