# チームエージェントオーケストレーションコンテンツパック (Team Agent Orchestration Content Pack)

このパックは、プライベート研究ソースを露出せずに現在の ECC 方向性を公開可能なアイデアに変換します。中核クレーム：エージェントツールはソロのチャットウィンドウから、ボード、コントロールペイン、動的ワークフロー、eval ゲート、共有 skills を持つチームオーケストレーションシステムへ移行しています。

## ポジショニング (Positioning)

ECC はマルチエージェントスタック向けのオーケストレーションと control-plane レイヤーとして位置づけるべきです。要点は「もう一つのプロンプトライブラリ」ではありません。Claude Code、Codex、OpenCode、Hermes スタイルのデスクトップ、ターミナルペイン、ブラウザエージェント、MCP gateway、内部エージェントツールを同時に使うチーム向けのワークフロー operating system です。

## ナラティブテーゼ (Narrative Thesis)

旧世代のエージェント Kanban は、エージェントが実カードを信頼して担当できるほど dependable ではなかったため失敗しました。コンテキストを幻覚し、検証をスキップし、マージできない出力を生みました。新世代は動的ワークフロー、より強いコードモデル、eval harness、ローカル状態、ブラウザ制御、MCP 標準化により、各カードを observable で gateable にできるため機能します。

## 動画コンセプト (Video Concepts)

### 1. なぜエージェント Kanban は失敗し、今なら機能しうるか (1. Why Agent Kanban Failed, And Why It Can Work Now)

- Hook: 「エージェント Kanban はかつて theater でした。今は operating surface になり得ます。」
- 表示：バックログから running、review、merged へ移動する1枚のカード。
- キービート：
  - カードには owner、branch、eval、merge gate が必要。
  - 動的ワークフローはエージェントがタスクローカル harness を生成できる。
  - コントロールペインは隠れたチャット出力を運用状態に変える。
- CTA: 「エージェントがコードできるか聞くのをやめろ。チームがエージェント作業をルート、検証、マージできるか聞け。」

### 2. コントロールペインは新しい IDE プリミティブ (2. The Control Pane Is The New IDE Primitive)

- Hook: 「次の IDE はテキストエディタではない。ミッションコントロール面だ。」
- 表示：sessions、work items、memory、connectors、actions、merge readiness。
- キービート：
  - チームは複数 harness を同時に走らせる。
  - 勝つプロダクトは context、tools、evidence を調整する。
  - 状態を inspectable にするときデスクトップアプリが重要で、別のチャットボックスを足すときではない。
- CTA: 「エージェントが何をしているか、何が失敗し、何が出荷できるかを伝えるペインを作れ。」

### 3. タスクごとの Harness (3. A Harness For Every Task)

- Hook: 「エージェントはコードを書くだけではない。コードが動くことを証明するワークフローを構築すべきだ。」
- 表示：動的ワークフローが tests、browser smoke、handoff artifacts を生成。
- キービート：
  - 静的ワークフローは良いデフォルト。
  - 動的ワークフローはタスクローカル harness。
  - 繰り返される動的ワークフローは共有 skills になる。
- CTA: 「本当の資産は使い捨て回答ではなく再利用可能なワークフローだ。」

### 4. MCP Gateway と全エージェント再設定の終焉 (4. MCP Gateways And The End Of Reconfiguring Every Agent)

- Hook: 「MCP server を10回設定するなら、エージェントスタックはすでに壊れている。」
- 表示：複数 harness に供給する1つの tool registry。
- キービート：
  - ツールは中央宣言されローカルで強制可能であるべき。
  - コントロールペインは connector 健全性を示すべき。
  - エージェントポータビリティは共有 tool 契約に依存。
- CTA: 「ツールを per-chat 設定ではなくインフラとして扱え。」

### 5. チームは AI ラボのように動く (5. Teams Will Run Like AI Labs)

- Hook: 「すべてのワークフローに eval があるとき、すべての会社は AI ラボになる。」
- 表示：pass/fail evaluator と work item queue を持つビジネスワークフロー。
- キービート：
  - Eval gate はエージェント作業をデモから運用へ移す。
  - 共有 skills はチームのベストプラクティスファイル。
  - コントロールペインは管理がスループットとリスクを見る場所。
- CTA: 「未来は1エージェントではない。評価されたエージェントのチームだ。」

## 記事アングル (Article Angles)

### 1. エージェント Kanban は早すぎただけで間違いではなかった (1. Agent Kanban Was Early, Not Wrong)

論点：

- カードがプロンプトだけだったとき、エージェント Kanban は失敗した。
- カードが ownership、branch scope、tests、eval、handoff を持つと機能し始める。
- 動的ワークフローは各カードが独自の proof harness を生成できる。
- コントロールペインは filesystem、tests、sessions から状態を示すためボードを正直にする。

推奨セクション：

1. なぜ初期のエージェント Kanban はフェイクに感じたか。
2. 何が変わったか：より良いモデル、動的ワークフロー、MCP、ローカル状態、ブラウザ自動化。
3. 最小 viable カードスキーマ。
4. なぜ merge gate はタスク割当より重要か。
5. チームが今構築すべきこと。

### 2. AI 開発のコントロールペイン時代 (2. The Control Pane Era Of AI Development)

論点：

- 次の開発者面は、エージェント、ツール、memory、gate を調整するコントロールペイン。
- チャットは相互作用レイヤーだが、プロダクト価値はオーケストレーション状態にある。
- ECC はローカル harness、デスクトップエージェント、チームシステム横断の共有レイヤーとして位置づけるべき。

推奨セクション：

1. チーム作業にはチャットだけでは足りない。
2. sessions、memory、tools、work items は1つのペインが必要。
3. 動的ワークフローには可視性が必要。
4. コントロールペインがプロダクトの moat になる。
5. オープンソース配布はインフラになることから来る。

### 3. 共有 Skills は新しいチームプレイブック (3. Shared Skills Are The New Team Playbooks)

論点：

- 最良の会社は、すべてのエンジニアが独自のエージェントワークフローを発明するのに頼らない。
- 共有 skill ファイルは新しいベストプラクティス文書だが、エージェントが実行可能。
- 動的ワークフローは発見；skills は組織記憶。

推奨セクション：

1. エージェント利用のチーム分岐が高コストな理由。
2. skill に属するもの。
3. タスクローカル harness を昇格するタイミング。
4. eval が共有 skills を正直に保つ方法。
5. これがプラットフォームレイヤーになる方法。

## ショート投稿 (Short Posts)

1. エージェント Kanban はボードが間違っていたから失敗したのではない。カードに ownership、eval、branch、merge gate がなかったからだ。新しいプリミティブは「エージェントにプロンプトを割り当てる」ではない。「検証済み work item をエージェントチームに割り当てる」ことだ。

2. 動的ワークフローは再利用の単位を変える。回答は使い捨て。harness が価値ある。同じタスクローカル harness が2回機能したら、共有 skill に昇格させろ。

3. コントロールペインはエージェント作業を管理から見えるようにする場所：誰がカードを所有し、何が変わり、何が失敗し、何が合格し、何がマージできるか。

4. エージェントインフラ向けの未来 OSS wedge は古い infra wedge に似る：ツール、ワークフロー、evidence、handoff を標準化するためチームが最初にインストールするものになる。

5. チームは1エージェントを走らせない。コード、ブラウザ、データ、レビュー、コンテンツ横断の評価された squad を走らせる。プロダクトレイヤーはオーケストレーションだ。

## 配信プラン (Distribution Plan)

1. エージェント Kanban について短い投稿を1本公開。
2. コントロールペインでカードが動く90秒動画でフォロー。
3. チームプレイブックとしての共有 skills 記事を公開。
4. ECC コントロールペイン + 動的ワークフローカードのデモクリップをリリース。
5. コメントを次の skill または記事に変換。

## ECC へのプロダクト含意 (Product Implications For ECC)

- skills を先に構築；commands は互換性 shim。
- コントロールペインに work items、エージェント Kanban 状態、gate、再利用 skill 候補を表示。
- 動的ワークフローを共有 skills のフィーダーシステムとして扱う。
- MCP と connector 設定を harness 横断で可視化すべきインフラとして扱う。
- プライベート研究はプライベートのまま；合成された概念とプロダクト evidence を公開。
