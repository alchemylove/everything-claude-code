---
name: data-throughput-accelerator
description: 大規模 data ingestion、backfill、export、ETL、warehouse loading、manifest catch-up、table synchronization をデータ正確性を保ったまま大幅に高速化するときに使用。ETL, backfill, throughput.
origin: ECC
tools: Read, Write, Edit, Bash, Grep, Glob
---

# データスループットアクセラレータ (Data Throughput Accelerator)

ボトルネックが大量データの移動、変換、保存にあるときにこの skill を使う。目標は単なる速度ではない。正しいデータが証拠付きで正しい場所に、より速く着地することである。

## 最初の区別 (First Distinction)

最適化前に次を分離する:

- ソース抽出速度
- ネットワーク転送速度
- ウェアハウス/ロード速度
- 変換速度
- サービングテーブル鮮度
- ジョブ実行中のライブテール成長

新データが最終キャッチアップウィンドウより速く到着すると、パイプラインは「速い」のに遅れて見える。

## 高速パスヒューリスティクス (Fast Path Heuristics)

- 計算をデータが既にある場所へ移す。
- 大きな landed ファイルにはウェアハウスネイティブの scan、join、append を優先する。
- manifest または checkpoint で完了ファイル/パーティションをスキップする。
- 読み取りと append パターンに合うパーティショニングとクラスタリングを使う。
- 小さなファイル、リクエスト、書き込みをバッチ化する。
- unique key、manifest、置換可能 staging で書き込みを冪等にする。
- raw、derived、サービングテーブルを別々に説明可能に保つ。

## ワークフロー (Workflow)

1. 現在のソース、ターゲット、manifest 契約を読む。
2. バックログを計測: 外部ファイル、manifest 行、raw 行、derived 行、min/max タイムスタンプ、未処理件数。
3. 安全なキャッチアップまたはサンプルベンチマークを実行する。
4. バリアントを比較: バッチサイズ、ワーカー数、ウェアハウス SQL、ファイルグループ化、staging 形状、manifest 更新方法。
5. 件数とタイムスタンプの一貫性を保つ最速パスのみ昇格させる。
6. パスを CLI、スケジュールジョブ、ワークフロー、runbook としてコード化する。
7. コード化パス実行後に最終会計を再実行する。

## 会計出力 (Accounting Output)

明確な会計ブロックを使う:

```text
Data throughput result:
- Source files discovered: 294
- Files processed this run: 294
- Raw rows added: 9,683,598
- Derived rows added: 8,917,585
- Remaining tail: 24 files at readback time
- Runtime: 38.7s
- Correctness gate: manifest counts and table max timestamps match
```

## ガードレール (Guardrails)

- メトリクスを良く見せるために raw データを削除しない。
- 失敗ファイルを黙ってスキップしない。
- 履歴バックフィル状態とライブテール鮮度を混ぜない。
- ターゲットテーブルと manifest が一致するまでパイプラインを complete と呼ばない。
- 金融、ヘルスケア、規制対象、顧客影響データでは replay 証拠と承認ゲートを保持する。
