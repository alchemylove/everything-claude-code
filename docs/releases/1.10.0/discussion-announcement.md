# ECC v1.10.0 がリリースされました (ECC v1.10.0 is live)

ECC は **14万スター** を超え、公開リリース面が実際のリポジトリから大きく乖離していました。

だから v1.10.0 はハード sync リリースです：

- **38 agents**
- **156 skills**
- **72 commands**
- plugin/install metadata を修正
- トップライン docs とリリース面を再整合

このリリースは、コア harness システム周辺で成長してきた operator/media lane も取り込みます：

- `brand-voice`
- `social-graph-ranker`
- `connections-optimizer`
- `customer-billing-ops`
- `google-workspace-ops`
- `project-flow-ops`
- `workspace-surface-audit`
- `manim-video`
- `remotion-video-creation`

2.0 側では：

ECC 2.0 は `ecc2/` 配下で in-tree の **実際の alpha control-plane 面** になりました。

今日ビルドでき、次を公開しています：

- `dashboard`
- `start`
- `sessions`
- `status`
- `stop`
- `resume`
- `daemon`

これは **ECC 2.0 roadmap 全体が完了した** という意味ではありません。

control-plane alpha がここにあり、使え、かつ「ビジョンだけ」のカテゴリから動き出しているという意味です。

今の最も正直なフレーミング：

- ECC 1.x は今日広く出荷されている実戦済み harness/workflow レイヤー
- ECC 2.0 はその上に成長する alpha control-plane

次を待っていたなら：

- よりクリーンな install 面
- より強い cross-harness parity
- コーディング primitive だけでなく operator workflow
- 散在したメモではなく実際の control-plane 方向性

このリリースでリポジトリが再び一貫して感じられるようになります。
