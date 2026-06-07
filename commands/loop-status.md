---
description: アクティブな loop の状態、進捗、障害シグナル、推奨される介入を検査する。
---

# Loop ステータスコマンド (Loop Status Command)

アクティブな loop の状態、進捗、障害シグナルを検査します。

この slash command は、現在のセッションが dequeue した後にのみ実行できます。wedged または sibling session を検査する必要がある場合は、別のターミナルからパッケージ化された CLI を実行してください:

```bash
npx --package ecc-universal ecc loop-status --json
```

CLI は `~/.claude/projects/**` 配下のローカル Claude transcript JSONL ファイルをスキャンし、古い `ScheduleWakeup` 呼び出しや、マッチする `tool_result` がない `Bash` tool 呼び出しを報告します。

## 使い方 (Usage)

`/loop-status [--watch]`

## 報告内容 (What to Report)

- アクティブな loop パターン
- 現在のフェーズと最後の成功 checkpoint
- 失敗しているチェック（ある場合）
- 推定時間/コストのドリフト
- 推奨される介入（continue/pause/stop）

## クロスセッション CLI (Cross-Session CLI)

- `ecc loop-status --json` は最近のローカル Claude transcript の機械読み取り可能なステータスを出力する。
- `ecc loop-status --home <dir>` は別のホームディレクトリをスキャンする（別のローカル profile やマウントされた workspace の検査時）。
- `ecc loop-status --transcript <session.jsonl>` は 1 つの transcript を直接検査する。
- `ecc loop-status --bash-timeout-seconds 1800` は古い Bash の閾値を調整する。
- `ecc loop-status --exit-code` は古い loop または tool シグナルが検出された場合に `2` で終了し、transcript をスキャンできない場合は `1` で終了する。
- `--exit-code` と `--watch` の併用時は `--watch-count` が必要（watchdog スクリプトがプロセス終了を永遠に待たないように）。
- `ecc loop-status --watch` は中断されるまでステータスを更新する。
- `ecc loop-status --watch --watch-count 3 --exit-code` は限定回数更新し、確認された最高ステータスで終了する。
- `ecc loop-status --watch --watch-count 3` はスクリプトや handoff 用の限定 watch ストリームを出力する。
- `ecc loop-status --watch --write-dir ~/.claude/loops` は sibling ターミナルや watchdog スクリプト用に `index.json` とセッションごとの JSON スナップショットを維持する。

## ウォッチモード (Watch Mode)

`--watch` が指定されている場合、定期的にステータスを更新します。`--json` 併用時は、各更新が 1 行あたり 1 つの JSON オブジェクトとして出力され、別のターミナルやスクリプトがストリームを消費できます。

## スナップショットファイル (Snapshot Files)

別のプロセスが現在の Claude セッションの `/loop-status` dequeue を待たずに loop 状態を検査する必要がある場合は、`--write-dir <dir>` を使用します。CLI は以下を書き込みます:

- 検査されたセッションごとに 1 行の `index.json`。
- そのセッションの完全なステータスペイロードを含む `<session-id>.json`。

これらのファイルはローカル transcript 分析のスナップショットです。Claude Code runtime の tool 呼び出しを制御したりタイムアウトさせたりするものではありません。

## 引数 (Arguments)

$ARGUMENTS:
- `--watch` optional
