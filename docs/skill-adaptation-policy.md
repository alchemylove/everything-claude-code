# Skill 適応ポリシー (Skill Adaptation Policy)

ECC は外部リポジトリからのアイデアを受け入れますが、出荷される skills は ECC-native な面になる必要があります。

## デフォルトルール (Default Rule)

コントリビューションが他の OSS リポジトリ、prompt pack、plugin、harness、個人設定から始まる場合：

- 根本的なアイデア、ワークフロー、構造をコピーする
- ECC の現在の install 面、validation flow、リポジトリ規約に適応する
- 不要な外部ブランディング、依存前提、上流固有のフレーミングを除去する

目標は、ECC を他人の runtime の薄いラッパーにしないことです。

## 元の名前を残すとき (When To Keep The Original Name)

次のすべてが真のときのみ、元の skill 名を保持します：

- コントリビューションがほぼ直接 port に近い
- 名前がすでに説明的で中立である
- 面が依然として上流概念のように振る舞う
- リポジトリ内により良い ECC-native 名がまだない

例：

- `nestjs-patterns` のような framework 名
- ベンダー訴求ではなく主題そのものである protocol や product 名

## 改名するとき (When To Rename)

ECC が元の作業を意味fully に拡張、縮小、再パッケージする場合は skill を改名します。

典型的なトリガー：

- ECC が実質的な新しい動作、構造、ガイダンスを追加した
- 元の名前が workflow-forward ではなく vendor-forward または community-brand-forward である
- コントリビューションが既存 ECC 面と重なり、より明確な境界が必要
- コントリビューションが literal port ではなく capability、operator workflow、policy レイヤーとして適合するようになった

例：

- 再利用可能な graph primitive は `social-graph-ranker` のままにし、より広い workflow レイヤーは `lead-intelligence` や `connections-optimizer` にする
- スコープが大きく変わった場合は、曖昧な import された planning ラベルより ECC-native 名の `product-capability` を優先する

## 依存ポリシー (Dependency Policy)

ECC は目的を達成する最も狭い native 面を優先します：

- 決定論的制約には `rules/`
- オンデマンド workflow には `skills/`
- 長寿命の対話型 tool 境界が正当化される場合に MCP
- 決定論的 one-shot 実行にはローカル scripts/CLI
- リモート呼び出しが狭く MCP を正当化しない場合に direct API

未検証の第三者パッケージをインストール・信頼するようユーザーに指示する skill だけを出荷することは避けてください。

外部機能を保持する価値がある場合：

- 実用的なら関連ロジックを ECC 内で vendor 化または再実装する
- または統合を optional にし、external であることを明確に示す
- 明示的な正当化なしに新しい外部依存をデフォルトパスにしない

## レビュー質問 (Review Questions)

contributed skill をマージする前に、次に答えてください：

1. これは ECC 内の実際に再利用可能な面か、それとも別ツールのドキュメントか？
2. 現在の名前は ECC 化された面とまだ一致しているか？
3. この振る舞いの大部分を担う ECC skill はすでに存在するか？
4. 概念を import しているのか、他人の product identity を import しているのか？
5. ECC ユーザーは上流リポジトリを知らなくても、この skill の目的を理解できるか？

これらの答えが弱い場合は、より適応する、スコープを狭める、または出荷しない。
