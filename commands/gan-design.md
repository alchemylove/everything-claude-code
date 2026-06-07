---
description: フロントエンドまたはビジュアル作業に対して、制限付きイテレーションとスコアリングによる generator/evaluator デザインループを実行します。
---

$ARGUMENTS から以下を解析:
1. `brief` — 作成するデザインのユーザーの説明
2. `--max-iterations N` — （オプション、デフォルト 10）design-evaluate サイクルの最大回数
3. `--pass-threshold N` — （オプション、デフォルト 7.5）合格するための重み付きスコア（デザイン向けにデフォルトが高い）

## GANスタイルデザインハーネス (GAN-Style Design Harness)

フロントエンドのデザイン品質に特化した 2 エージェントループ（Generator + Evaluator）。planner なし — brief が spec そのものです。

これは Anthropic がフロントエンドデザイン実験で使用したのと同じモードで、CSS perspective と doorway navigation による 3D Dutch art museum のようなクリエイティブなブレイクスルーが見られました。

### セットアップ (Setup)
1. `gan-harness/` ディレクトリを作成
2. brief を直接 `gan-harness/spec.md` として書き込み
3. Design Quality と Originality に追加の重みを付けたデザイン特化の `gan-harness/eval-rubric.md` を書き込み

### デザイン特化の評価ルーブリック (Design-Specific Eval Rubric)
```markdown
### Design Quality (weight: 0.35)
### Originality (weight: 0.30)
### Craft (weight: 0.25)
### Functionality (weight: 0.10)
```

注: Originality の重みが高め（0.30 vs 0.20）で、クリエイティブなブレイクスルーを促進します。Functionality の重みが低いのは、design mode がビジュアル品質に焦点を当てるためです。

### ループ (Loop)
`/project:gan-build` の Phase 2 と同じですが、以下が異なります:
- planner をスキップ
- デザイン特化の rubric を使用
- Generator prompt が feature completeness より visual quality を強調
- Evaluator prompt が「do all features work?」より「would this win a design award?」を強調

### gan-build との主な違い (Key Difference from gan-build)
Generator には次のように指示されます: "Your PRIMARY goal is visual excellence. A stunning half-finished app beats a functional ugly one. Push for creative leaps — unusual layouts, custom animations, distinctive color work."
