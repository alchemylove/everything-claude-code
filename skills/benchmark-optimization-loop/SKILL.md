---
name: benchmark-optimization-loop
description: ベンチマーク最適化ループ — 高速化、多数 variant の試行、再帰的最適化、latency/throughput/cost の benchmark、繰り返し計測テストによる最良実装の選択を依頼されたときに使用。
origin: ECC
tools: Read, Write, Edit, Bash, Grep, Glob
---

# ベンチマーク最適化ループ (Benchmark Optimization Loop)

「20 倍速くして」や「50 回の再帰的最適化を試して」を、実際にシステムを改善できる境界付き計測ループに変換する skill。

## 必須ベースライン (Required Baseline)

次が揃うまで最適化しない:

- 最適化対象の操作
- 常に green でなければならない正確性ゲート
- メトリク: wall time、p95 レイテンシ、rows/sec、cost/run、メモリ、エラー率
- 現在のベースライン
- 探索予算: 最大バリアント数、最大時間、最大支出、最大データ影響

ユーザーが非現実的な目標を求めても、野心は保ちつつループを境界付きで計測可能にする。

## ループ (Loop)

1. ベースラインを計測する。
2. 証拠からボトルネックを特定する。
3. 1 仮説ずつ検証するバリアントを生成する。
4. 同じ入力形状でバリアントを実行する。
5. 正確性、安全性、再現性に失敗したバリアントを却下する。
6. 最速の安全バリアントを昇格させる。
7. 勝ちパスを script、command、test、config、doc にコード化する。
8. ベースラインと勝者を再実行して差分を確認する。

## バリアント表 (Variant Table)

次のようにバリアントを追跡する:

```text
Variant | Hypothesis | Command | Time | Correct? | Notes
baseline | current path | npm run job | 120s | yes | stable
batch-500 | fewer round trips | npm run job -- --batch 500 | 42s | yes | winner
parallel-8 | more workers | npm run job -- --workers 8 | 31s | no | rate limited
```

## 再帰的探索 (Recursive Search)

再帰的またはハイパーパラメータ作業では:

- すべての実行を ledger に永続化する
- 直前の実行だけでなく、直前に受理した勝者と比較する
- holdout または replay チェックを保持する
- 改善がノイズ内、正確性失敗、コスト超過、説明不能な変数数の変更で停止する

探索空間が実際に網羅的でない限り、「global optimum」ではなく「best measured safe variant」のような表現を使う。

## 昇格ゲート (Promotion Gate)

次が満たされるまでバリアントを新デフォルトにできない:

- 正確性テストが pass する
- 性能差分が再現または説明される
- ロールバックが明確である
- 変更がソース管理または durable runbook にエンコードされる
- 最終サマリーに正確な command と計測が含まれる
