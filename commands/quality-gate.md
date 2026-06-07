---
description: ファイルまたはプロジェクトスコープ向けに ECC quality pipeline を実行し、修正手順を報告する。
---

# Quality Gate コマンド (Quality Gate Command)

ファイルまたはプロジェクトスコープ向けに、オンデマンドで ECC quality pipeline を実行する。

## 使い方 (Usage)

`/quality-gate [path|.] [--fix] [--strict]`

- デフォルト対象: カレントディレクトリ (`.`)
- `--fix`: 設定されている場合は auto-format/fix を許可
- `--strict`: サポートされている場合は warning でも失敗

## パイプライン (Pipeline)

1. 対象の language/tooling を検出する。
2. formatter チェックを実行する。
3. 利用可能な場合は lint/type チェックを実行する。
4. 簡潔な remediation リストを出力する。

## 注意事項 (Notes)

このコマンドは hook の挙動を反映するが、オペレーターが明示的に起動する。

## 引数 (Arguments)

$ARGUMENTS:
- `[path|.]` 任意の対象パス
- `--fix` 任意
- `--strict` 任意
