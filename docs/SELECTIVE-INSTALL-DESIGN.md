# ECC 選択的インストール設計 (ECC Selective Install Design)

## 目的 (Purpose)

このドキュメントは、ECC のユーザー向け選択的インストール設計を定義する。

内部ランタイムアーキテクチャとコード境界に焦点を当てた
`docs/SELECTIVE-INSTALL-ARCHITECTURE.md` を補完する。

このドキュメントは、まずプロダクトとオペレーターの問いに答える：

- ユーザーが ECC コンポーネントをどう選ぶか
- CLI はどう感じられるべきか
- どの config ファイルが存在すべきか
- インストールは harness ターゲット横断でどう振る舞うべきか
- 設計は書き直しを要求せずに現在の ECC コードベースへどうマップするか

## 問題 (Problem)

リポジトリには第一段階の manifest とライフサイクルサポートがあるにもかかわらず、今日の ECC は依然として大きなペイロードインストーラーのように感じられる。

ユーザーにはより単純なメンタルモデルが必要である：

- ベースラインをインストールする
- 実際に使う言語パックを追加する
- 実際に欲しいフレームワーク config を追加する
- security、research、orchestration のようなオプション capability pack を追加する

選択的インストールシステムは、ECC をオールオアナッシングではなく構成可能に感じさせるべきである。

現在の基盤では、ユーザー向けコンポーネントは依然としてより粗い内部インストール module 上のエイリアスレイヤーである。つまり include/exclude は module 選択レベルではすでに有用だが、基盤となる module グラフがより細かく分割されるまで、一部のファイルレベル境界は不完全なままである。

## 目標 (Goals)

1. ユーザーが小さなデフォルト ECC フットプリントを素早くインストールできるようにする。
2. 再利用可能なコンポーネントファミリーからインストールを構成できるようにする：
   - core rules
   - language packs
   - framework packs
   - capability packs
   - target/platform configs
3. Claude、Cursor、Antigravity、Codex、OpenCode 横断で一貫した UX を維持する。
4. インストールを検査可能、修復可能、削除可能に保つ。
5. ロールアウト中は現在の `ecc-install typescript` スタイルとの後方互換性を保持する。

## 非目標 (Non-Goals)

- 第一段階で ECC を複数の npm パッケージにパッケージングすること
- リモートマーケットプレイスの構築
- 同じフェーズでのフルコントロールプレーン UI
- 選択的インストール出荷前にすべての skill 分類問題を解決すること

## ユーザー体験の原則 (User Experience Principles)

### 1. 小さく始める (Start Small)

ユーザーは1コマンドで有用な ECC インストールを得られるべきである：

```bash
ecc install --target claude --profile core
```

デフォルト体験は、すべての skill ファミリーとすべてのフレームワークをユーザーが欲しいと仮定すべきではない。

### 2. 意図で積み上げる (Build Up By Intent)

ユーザーは次のように考えるべきである：

- 「developer ベースラインが欲しい」
- 「TypeScript と Python が必要」
- 「Next.js と Django が欲しい」
- 「security pack が欲しい」

ユーザーは生の内部リポジトリパスを知る必要はない。

### 3. 変更前にプレビュー (Preview Before Mutation)

すべてのインストールパスは dry-run プランニングをサポートすべきである：

```bash
ecc install --target cursor --profile developer --with lang:typescript --with framework:nextjs --dry-run
```

プランは次を明確に示すべきである：

- 選択されたコンポーネント
- スキップされたコンポーネント
- ターゲットルート
- 管理対象パス
- 期待される install-state の場所

### 4. ローカル設定を第一級に (Local Configuration Should Be First-Class)

チームはプロジェクトレベルのインストール config をコミットし、次を使えるべきである：

```bash
ecc install --config ecc-install.json
```

これにより、コントリビューターと CI 横断で決定的なインストールが可能になる。

## コンポーネントモデル (Component Model)

現在の manifest はすでに install module と profile を使用している。ユーザー向け設計はその内部構造を維持しつつ、4つの主要コンポーネントファミリーとして提示すべきである。

近い将来の実装メモ：一部のユーザー向けコンポーネント ID は依然として共有内部 module へ解決される。特に language/framework レイヤーでは。カタログは後続フェーズでより細かい module 粒度へのクリーンな道を保ちつつ、UX を即座に改善する。

### 1. ベースライン (Baseline)

これらはデフォルトの ECC 構成要素である：

- core rules
- baseline agents
- core commands
- runtime hooks
- platform configs
- workflow quality primitives

現在の内部 module の例：

- `rules-core`
- `agents-core`
- `commands-core`
- `hooks-runtime`
- `platform-configs`
- `workflow-quality`

### 2. 言語パック (Language Packs)

言語パックは、言語エコシステム向けの rules、ガイダンス、ワークフローをグループ化する。

例：

- `lang:typescript`
- `lang:python`
- `lang:go`
- `lang:java`
- `lang:rust`

各言語パックは、1つ以上の内部 module とターゲット固有アセットへ解決されるべきである。

### 3. フレームワークパック (Framework Packs)

フレームワークパックは言語パックの上に位置し、フレームワーク固有の rules、skills、オプションのセットアップを取り込む。

例：

- `framework:react`
- `framework:nextjs`
- `framework:django`
- `framework:springboot`
- `framework:laravel`

フレームワークパックは、適切な場合に正しい言語パックまたはベースライン primitive に依存すべきである。

### 4. Capability パック (Capability Packs)

capability pack は横断的な ECC 機能バンドルである。

例：

- `capability:security`
- `capability:research`
- `capability:orchestration`
- `capability:media`
- `capability:content`

これらは manifest で導入中の現在の module ファミリーへマップされるべきである。

## プロファイル (Profiles)

profile は最速の入り口のままである。

推奨されるユーザー向け profile：

- `core`
  最小ベースライン。ECC を試す大多数のユーザー向けの安全なデフォルト
- `developer`
  活発なソフトウェアエンジニアリング作業向けの最良デフォルト
- `security`
  ベースラインに security 重視のガイダンスを追加
- `research`
  ベースラインに research/content/investigation ツールを追加
- `full`
  分類済みで現在サポートされているすべて

profile は追加の `--with` と `--without` フラグと構成可能であるべき。

例：

```bash
ecc install --target claude --profile developer --with lang:typescript --with framework:nextjs --without capability:orchestration
```

## 提案する CLI 設計 (Proposed CLI Design)

### 主要コマンド (Primary Commands)

```bash
ecc install
ecc plan
ecc list-installed
ecc doctor
ecc repair
ecc uninstall
ecc catalog
```

### インストール CLI (Install CLI)

推奨形状：

```bash
ecc install [--target <target>] [--profile <name>] [--with <component>]... [--without <component>]... [--config <path>] [--dry-run] [--json]
```

例：

```bash
ecc install --target claude --profile core
ecc install --target cursor --profile developer --with lang:typescript --with framework:nextjs
ecc install --target antigravity --with capability:security --with lang:python
ecc install --config ecc-install.json
```

### プラン CLI (Plan CLI)

推奨形状：

```bash
ecc plan [same selection flags as install]
```

目的：

- 変更なしでプレビューを生成する
- 選択的インストールの正規デバッグサーフェスとして機能する

### カタログ CLI (Catalog CLI)

推奨形状：

```bash
ecc catalog profiles
ecc catalog components
ecc catalog components --family language
ecc catalog show framework:nextjs
```

目的：

- ドキュメントを読まずに有効なコンポーネント名を発見できるようにする
- config 作成を親しみやすく保つ

### 互換 CLI (Compatibility CLI)

移行中、これらの legacy フローは依然として動作すべき：

```bash
ecc-install typescript
ecc-install --target cursor typescript
ecc typescript
```

内部的には、これらを新しいリクエストモデルへ正規化し、モダンなインストールと同じ方法で install-state を書き込むべきである。

## 提案する Config ファイル (Proposed Config File)

### ファイル名 (Filename)

推奨デフォルト：

- `ecc-install.json`

オプションの将来サポート：

- `.ecc/install.json`

### 設定形状 (Config Shape)

```json
{
  "$schema": "./schemas/ecc-install-config.schema.json",
  "version": 1,
  "target": "cursor",
  "profile": "developer",
  "include": [
    "lang:typescript",
    "lang:python",
    "framework:nextjs",
    "capability:security"
  ],
  "exclude": [
    "capability:media"
  ],
  "options": {
    "hooksProfile": "standard",
    "mcpCatalog": "baseline",
    "includeExamples": false
  }
}
```

### フィールドセマンティクス (Field Semantics)

- `target`
  `claude`、`cursor`、`antigravity` などの選択された harness ターゲット
- `profile`
  出発点となるベースライン profile
- `include`
  追加するコンポーネント
- `exclude`
  profile 結果から差し引くコンポーネント
- `options`
  コンポーネント同一性を変えないターゲット/ランタイム調整フラグ

### 優先順位ルール (Precedence Rules)

1. CLI 引数は config ファイルの値を上書きする。
2. config ファイルは profile デフォルトを上書きする。
3. profile デフォルトは内部 module デフォルトを上書きする。

これにより挙動が予測可能で説明しやすくなる。

## モジュラーインストールフロー (Modular Installation Flow)

ユーザー向けフローは次のとおりであるべき：

1. 提供された場合、または自動検出された config ファイルを読み込む
2. config 意図の上に CLI 意図をマージする
3. リクエストを正規化された選択へ正規化する
4. profile をベースラインコンポーネントへ展開する
5. `include` コンポーネントを追加する
6. `exclude` コンポーネントを差し引く
7. 依存関係とターゲット互換性を解決する
8. プランをレンダリングする
9. dry-run モードでなければオペレーションを適用する
10. install-state を書き込む

重要な UX 特性は、まったく同じフローが次を駆動することである：

- `install`
- `plan`
- `repair`
- `uninstall`

コマンドはアクションが異なるが、ECC が選択されたインストールを理解する方法は同じである。

## ターゲット挙動 (Target Behavior)

選択的インストールは、すべてのターゲット横断で同じ概念的コンポーネントグラフを保持しつつ、ターゲットアダプターがコンテンツの配置方法を決定できるようにすべきである。

### Claude (Claude)

最適な用途：

- home スコープの ECC ベースライン
- commands、agents、rules、hooks、platform config、orchestration

### Cursor (Cursor)

最適な用途：

- project スコープのインストール
- rules とプロジェクトローカルの自動化および config

### Antigravity (Antigravity)

最適な用途：

- project スコープの agent/rule/workflow インストール

### Codex / OpenCode (Codex / OpenCode)

インストーラーの特殊フォークではなく、追加的ターゲットとして残すべきである。

選択的インストール設計は、これらを新しいインストーラーアーキテクチャではなく、新しいアダプターと新しいターゲット固有マッピングルールとして扱えるようにすべきである。

## 技術的実現可能性 (Technical Feasibility)

この設計は実現可能である。リポジトリにはすでに次があるから：

- install module と profile manifest
- install-state パスを持つターゲットアダプター
- plan 検査
- install-state 記録
- ライフサイクルコマンド
- 統一 `ecc` CLI サーフェス

欠けている作業は概念的発明ではない。欠けている作業は、現在の基盤をよりクリーンなユーザー向けコンポーネントモデルへプロダクト化することである。

### Phase 1 で実現可能 (Feasible In Phase 1)

- profile + include/exclude 選択
- `ecc-install.json` config ファイルパース
- catalog/discovery コマンド
- ユーザー向けコンポーネント ID から内部 module セットへのエイリアスマッピング
- dry-run と JSON プランニング

### Phase 2 で実現可能 (Feasible In Phase 2)

- より豊富なターゲットアダプターセマンティクス
- config 系アセット向けの merge 対応オペレーション
- 非コピーオペレーション向けのより強い repair/uninstall 挙動

### 後日 (Later)

- publish サーフェスの削減
- 生成された slim bundle
- リモートコンポーネント取得

## 現在の ECC Manifest へのマッピング (Mapping To Current ECC Manifests)

現在の manifest は、まだ真のユーザー向け `lang:*` / `framework:*` / `capability:*` 分類法を公開していない。それは第2のインストーラーエンジンではなく、既存 module の上のプレゼンテーション層として導入されるべきである。

推奨アプローチ：

- `install-modules.json` を内部解決カタログとして維持する
- フレンドリーなコンポーネント ID を1つ以上の内部 module へマップするユーザー向けコンポーネントカタログを追加する
- 移行期間中、profile が内部 module またはユーザー向けコンポーネント ID のいずれかを参照できるようにする

これにより、現在の選択的インストール基盤を壊さずに UX を改善できる。

## 推奨ロールアウト (Suggested Rollout)

### Phase 1: 設計と発見 (Design And Discovery)

- ユーザー向けコンポーネント分類法を確定する
- config スキーマを追加する
- CLI 設計と優先順位ルールを追加する

### Phase 2: ユーザー向け解決レイヤー (User-Facing Resolution Layer)

- コンポーネントエイリアスを実装する
- config ファイルパースを実装する
- `include` / `exclude` を実装する
- `catalog` を実装する

### Phase 3: より強いターゲットセマンティクス (Stronger Target Semantics)

- より多くのロジックをターゲット所有のプランニングへ移す
- merge/generate オペレーションをクリーンにサポートする
- repair/uninstall の忠実度を改善する

### Phase 4: パッケージング最適化 (Packaging Optimization)

- publish サーフェスを狭める
- 生成 bundle を評価する

## 推奨 (Recommendation)

次の実装は「インストーラーの書き直し」ではないべきである。

次であるべき：

1. 現在の manifest/ランタイム基盤を維持する
2. ユーザー向けコンポーネントカタログと config ファイルを追加する
3. `include` / `exclude` 選択と catalog 発見を追加する
4. 既存の planner とライフサイクルスタックがそのモデルを消費できるようにする

これが、現在の ECC コードベースから、大きな legacy インストーラーではなく ECC 2.0 のように感じられる本物の選択的インストール体験への最短経路である。
