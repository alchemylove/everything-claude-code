---
description: 安全性デフォルトと明示的な停止条件を持つ、管理された自律 loop パターンを開始する。
---

# Loop 開始コマンド (Loop Start Command)

安全性デフォルトを持つ管理された自律 loop パターンを開始します。

## 使い方 (Usage)

`/loop-start [pattern] [--mode safe|fast]`

- `pattern`: `sequential`, `continuous-pr`, `rfc-dag`, `infinite`
- `--mode`:
  - `safe`（デフォルト）: 厳格な品質ゲートと checkpoint
  - `fast`: スピード重視で削減されたゲート

## フロー (Flow)

1. リポジトリの状態とブランチ戦略を確認する。
2. loop パターンと model tier 戦略を選択する。
3. 選択されたモードに必要な hooks/profile を有効化する。
4. loop 計画を作成し、`.claude/plans/` に runbook を書き込む。
5. loop の開始とモニタリング用コマンドを表示する。

## 必須の安全チェック (Required Safety Checks)

- 最初の loop iteration の前にテストが通ることを検証する。
- `ECC_HOOK_PROFILE` がグローバルに無効化されていないことを確認する。
- loop に明示的な停止条件があることを確認する。

## 引数 (Arguments)

$ARGUMENTS:
- `<pattern>` optional (`sequential|continuous-pr|rfc-dag|infinite`)
- `--mode safe|fast` optional
