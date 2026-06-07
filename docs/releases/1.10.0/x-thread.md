# X スレッドドラフト — ECC v1.10.0 (X Thread Draft — ECC v1.10.0)

ECC は14万スターを超え、公開面が実際のリポジトリから大きく乖離していました。

だから v1.10.0 は sync リリースです。

38 agents
156 skills
72 commands

plugin metadata を修正
install 面を修正
docs とリリースストーリーをライブリポジトリに再整合

また、実利用から成長した operator / media lane も出荷しました：

- brand-voice
- social-graph-ranker
- connections-optimizer
- customer-billing-ops
- google-workspace-ops
- project-flow-ops
- workspace-surface-audit
- manim-video
- remotion-video-creation

そして最も重要なこと：

ECC 2.0 はもう roadmap の話だけではありません。

`ecc2/` control-plane alpha が in-tree にあり、今日ビルドでき、すでに次を公開しています：

- dashboard
- start
- sessions
- status
- stop
- resume
- daemon

まだ GA と呼びません。

実態をそのまま呼びます：

公開で構築してきた harness/workflow レイヤーの上に乗る、実際の alpha control plane です。
