---
description: 実装タスクに対して、制限付きイテレーションとスコアリングによる generator/evaluator ビルドループを実行します。
---

$ARGUMENTS から以下を解析:
1. `brief` — 何をビルドするかのユーザーの一行説明
2. `--max-iterations N` — （オプション、デフォルト 15）generator-evaluator サイクルの最大回数
3. `--pass-threshold N` — （オプション、デフォルト 7.0）合格するための重み付きスコア
4. `--skip-planner` — （オプション）planner をスキップし、`spec.md` が既に存在すると想定
5. `--eval-mode MODE` — （オプション、デフォルト `"playwright"`）次のいずれか: playwright, screenshot, code-only

## GANスタイルハーネスビルド (GAN-Style Harness Build)

このコマンドは、Anthropic の 2026年3月 harness design 論文に触発された 3 エージェントビルドループをオーケストレーションします。

### フェーズ 0: セットアップ (Phase 0: Setup)
1. プロジェクトルートに `gan-harness/` ディレクトリを作成
2. サブディレクトリを作成: `gan-harness/feedback/`、`gan-harness/screenshots/`
3. git が未初期化なら初期化
4. 開始時刻と設定をログ

### フェーズ 1: プランニング (Phase 1: Planning (Planner Agent))
`--skip-planner` が設定されていない場合:
1. ユーザーの brief で Task tool 経由で `gan-planner` エージェントを起動
2. `gan-harness/spec.md` と `gan-harness/eval-rubric.md` の生成を待機
3. spec のサマリーをユーザーに表示
4. フェーズ 2 に進む

### フェーズ 2: ジェネレーター-エバリュエーターループ (Phase 2: Generator-Evaluator Loop)
```
iteration = 1
while iteration <= max_iterations:

    # GENERATE
    Task tool 経由で gan-generator エージェントを起動:
    - spec.md を読む
    - iteration > 1 の場合: feedback/feedback-{iteration-1}.md を読む
    - アプリケーションをビルド/改善
    - dev server が実行中であることを確認
    - 変更を commit

    # ジェネレーターの完了を待機

    # EVALUATE
    Task tool 経由で gan-evaluator エージェントを起動:
    - eval-rubric.md と spec.md を読む
    - ライブアプリケーションをテスト（mode: playwright/screenshot/code-only）
    - rubric に対してスコアリング
    - feedback/feedback-{iteration}.md に feedback を書き込み

    # エバリュエーターの完了を待機

    # CHECK SCORE
    feedback/feedback-{iteration}.md を読む
    重み付き合計スコアを抽出

    if score >= pass_threshold:
        "PASSED at iteration {iteration} with score {score}" をログ
        Break

    if iteration >= 3 and 直近 2 イテレーションでスコアが改善していない:
        "PLATEAU detected — stopping early" をログ
        Break

    iteration += 1
```

### フェーズ 3: サマリー (Phase 3: Summary)
1. すべての feedback ファイルを読む
2. 最終スコアとイテレーション履歴を表示
3. スコア推移を表示: `iteration 1: 4.2 → iteration 2: 5.8 → ... → iteration N: 7.5`
4. 最終評価からの残りの問題を一覧
5. 合計時間と推定コストを報告

### 出力 (Output)

```markdown
## GAN Harness Build Report

**Brief:** [original prompt]
**Result:** PASS/FAIL
**Iterations:** N / max
**Final Score:** X.X / 10

### Score Progression
| Iter | Design | Originality | Craft | Functionality | Total |
|------|--------|-------------|-------|---------------|-------|
| 1 | ... | ... | ... | ... | X.X |
| 2 | ... | ... | ... | ... | X.X |
| N | ... | ... | ... | ... | X.X |

### Remaining Issues
- [Any issues from final evaluation]

### Files Created
- gan-harness/spec.md
- gan-harness/eval-rubric.md
- gan-harness/feedback/feedback-001.md through feedback-NNN.md
- gan-harness/generator-state.md
- gan-harness/build-report.md
```

完全なレポートを `gan-harness/build-report.md` に書き込みます。
