# Capability Surface 選択 (Capability Surface Selection)

capability が rule、skill、MCP server、またはプレーン CLI/API ワークフローのどれに属するかを決めるときのルーティングガイドとして使う。

ECC はこれらの面を interchangeable とは扱いません。目標は、正確性を保ち、token コストを抑え、不要な runtime や supply-chain drag を作らない最も狭い面に各 capability を置くことです。

## 短い版 (The Short Version)

- `rules/` は path または event が一致したとき注入されるべき決定論的、常時オン制約向け。
- `skills/` はオンデマンドワークフロー、より豊富な playbook、関連するときだけ読み込むべき token 高コストガイダンス向け。
- `MCP` は session や client 横断で長寿命 tool/resource 面の恩恵がある対話型 structured capability 向け。
- ローカル `CLI` または repo script は永続 server を必要としない単純な決定論的アクション向け。
- skill 内の直接 `API` 呼び出しは、フル MCP server が問題より重い狭いリモートアクション向け。

## 決定順序 (Decision Order)

次の質問を順に：

1. path や event が一致するたびに、モデル判断なしで毎回起きるべきか？
   - `rule` を使う。
2. これは主に playbook、ワークフロー、またはタスクが実際に必要なときだけ読み込むべき助言レイヤーか？
   - `skill` を使う。
3. capability は複数 harness や client が繰り返し呼ぶ structured 対話型 tool/resource インターフェースを必要とするか？
   - `MCP` を使う。
4. server を生かさず script として走らせられる単純なローカルアクションか？
   - ローカル `CLI` エントリポイントまたは repo script を使い、必要なら skill でラップ。
5. より大きなワークフロー内の狭いリモート統合ステップだけか？
   - skill または script から外部 `API` を直接呼ぶ。

## 面ごとのガイダンス (Surface-by-Surface Guidance)

### Rules

rules を使う場合：

- path スコープのコーディング不変条件
- safety floor と permission 制約
- 常に適用されるべき harness/runtime 制約
- モデル裁量に依存すべきでない決定論的リマインダー

rules を使わない場合：

- 一致する編集ごとに肥大化する大きな playbook
- オプショナルワークフロー
- たまにしか関係しない高コストドメイン context

### Skills

skills を使う場合：

- マルチステップワークフロー
- 判断重視のガイダンス
- オンデマンドでのみ読み込むのに十分高コストなドメイン playbook
- script、API、MCP tool、隣接 skill 横断のオーケストレーション

決定論的ルーティングを本当に望む静的不変条件のダンプ場所として skills を使わない。

### MCP

capability が次の恩恵を受けるとき MCP を使う：

- structured tool 入出力
- 再利用可能な resource または prompt
- 繰り返しのクロスクライアント利用
- Claude Code、Codex、Cursor、OpenCode、関連 harness で動く安定インターフェース
- 長寿命 server プロセスが運用オーバーヘッドに見合う

MCP を避ける場合：

- one-shot ローカルコマンドの仕事
- server が一度 shell out するだけ
- server がプロダクト価値より install/runtime 負担を増やす

### CLI / Repo Scripts

次のときローカル script または CLI を優先：

- アクションが決定論的
- 起動が安い
- ワークフローがほぼローカル
- 永続 tool/resource 面を露出する利点がない

しばしば正しい選択：

- lint/test/build ラッパー
- ローカル変換
- 小さな installer
- 呼び出しごとに一度走るコンテンツ生成

### 直接 API 呼び出し (Direct API Calls)

既存 skill または script 内の直接 API を優先する場合：

- 統合が狭い
- リモートアクションがより大きなワークフローの一部
- まだ再利用可能な transport 面が不要

同じリモート統合が中心的で繰り返しでマルチクライアントになるのが、MCP 面へ昇格するシグナル。

## コストと信頼性バイアス (Cost and Reliability Bias)

両方 viable なとき：

- より小さい runtime 面を優先
- より低い token オーバーヘッドを優先
- 外部 moving part が少ない経路を優先
- 別の第三者依存を導入するより ECC-native packaging を優先

capability が明らかに maintenance、security、install 負担に見合う場合を除き、外部 plugin や package 依存を first-class ECC 面として正規化しない。

## リポジトリポリシー (Repo Policy)

外部リポジトリからアイデアを持ち込むとき：

- 外部依存ではなく根本アイデアをコピー
- ECC-native rule、skill、script、または MCP 面として再パッケージ
- ECC 向けに実質的に拡張または再形成された場合は改名
- その依存が意図的で監査済みでワークフローの中心でない限り、無関係な第三者パッケージのインストールを要求する指示を出荷しない

## 例 (Examples)

- `api/**` 編集に常に適用すべき backend auth 不変条件：
  - `rule`
- より深い API 設計と pagination playbook：
  - `skill`
- 複数 harness で使う再利用可能なリモート検索面：
  - `MCP`
- ローカルファイルを読みレポートを書く one-shot repo analyzer：
  - ローカル `CLI` または script、必要なら `skill` でラップ
- より広い customer-ops ワークフロー内の単一 billing-portal session 作成ステップ：
  - ワークフロー内の直接 `API` 呼び出し

## 実用ヒューリスティック (Practical Heuristic)

不明なら、小さく始める：

- 決定論的不変条件は `rule` から
- ガイダンス/ワークフローは `skill` から
- one-shot 実行は script から
- structured server 境界が明らかに自分を賄うときだけ `MCP` に昇格
