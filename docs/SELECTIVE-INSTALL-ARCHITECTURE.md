# ECC 2.0 選択的インストールの発見 (ECC 2.0 Selective Install Discovery)

## 目的 (Purpose)

このドキュメントは、2026年3月11日のメガプランにおける選択的インストール要件を、具体的な ECC 2.0 発見設計へと落とし込む。

目標は単に「インストール時にコピーするファイルを減らす」ことではない。実際の目標は、次の問いに決定的に答えられるインストールシステムである：

- 何がリクエストされたか
- 何が解決されたか
- 何がコピーまたは生成されたか
- どのターゲット固有の変換が適用されたか
- ECC が何を所有し、後から安全に削除または修復できるか

これが、ECC 1.x のインストールと ECC 2.0 コントロールプレーンの間に欠けていた契約である。

## 現在実装済みの基盤 (Current Implemented Foundation)

選択的インストールの第一段階の基盤は、すでにリポジトリ内に存在する：

- `manifests/install-modules.json`
- `manifests/install-profiles.json`
- `schemas/install-modules.schema.json`
- `schemas/install-profiles.schema.json`
- `schemas/install-state.schema.json`
- `scripts/ci/validate-install-manifests.js`
- `scripts/lib/install-manifests.js`
- `scripts/lib/install/request.js`
- `scripts/lib/install/runtime.js`
- `scripts/lib/install/apply.js`
- `scripts/lib/install-targets/`
- `scripts/lib/install-state.js`
- `scripts/lib/install-executor.js`
- `scripts/lib/install-lifecycle.js`
- `scripts/ecc.js`
- `scripts/install-apply.js`
- `scripts/install-plan.js`
- `scripts/list-installed.js`
- `scripts/doctor.js`

現在の機能：

- 機械可読な module と profile カタログ
- manifest エントリが実在するリポジトリパスを指していることを検証する CI
- 依存関係の展開とターゲットフィルタリング
- アダプター対応のオペレーションプランニング
- legacy モードと manifest インストールモード向けの正規化されたリクエスト
- 正規化されたリクエストからプラン作成への明示的なランタイムディスパッチ
- legacy と manifest の両インストールが永続的な install-state を書き込む
- 変更前にインストールプランを読み取り専用で検査
- install、planning、ライフサイクルコマンドを統合する `ecc` CLI ルーティング
- `list-installed`、`doctor`、`repair`、`uninstall` によるライフサイクル検査と変更

現在の制限：

- 一部の module ではターゲット固有の merge/remove セマンティクスがまだスキャフォールドレベル
- legacy `ecc-install` 互換性は依然として `install.sh` を指している
- `package.json` の publish サーフェスは依然として広い

## 現在のコードレビュー (Current Code Review)

現在のインストーラースタックは、元の言語ファーストのシェルインストーラーよりはるかに健全だが、依然として少数のファイルに責任が集中しすぎている。

### 現在のランタイムパス (Current Runtime Path)

現在のランタイムフローは次のとおり：

1. `install.sh`
   実際のパッケージルートを解決する薄いシェルラッパー
2. `scripts/install-apply.js`
   legacy モードと manifest モード向けのユーザー向けインストーラー CLI
3. `scripts/lib/install/request.js`
   CLI パースと正規化されたリクエスト
4. `scripts/lib/install/runtime.js`
   正規化されたリクエストからインストールプランへのランタイムディスパッチ
5. `scripts/lib/install-executor.js`
   引数変換、legacy 互換性、オペレーションの具体化、ファイルシステム変更、install-state の書き込み
6. `scripts/lib/install-manifests.js`
   module/profile カタログの読み込みと依存関係の展開
7. `scripts/lib/install-targets/`
   ターゲットルートと宛先パスのスキャフォールド
8. `scripts/lib/install-state.js`
   スキーマ対応の install-state 読み書き
9. `scripts/lib/install-lifecycle.js`
   保存されたオペレーションから導出される doctor/repair/uninstall の挙動

これは選択的インストール基盤を証明するには十分だが、インストーラーアーキテクチャが定着したと感じられるほどではない。

### 現在の強み (Current Strengths)

- インストール意図は `--profile` と `--modules` により明示的になった
- リクエストパースとリクエスト正規化は CLI シェルから分離された
- ターゲットルート解決はすでにアダプター化されている
- ライフサイクルコマンドは推測ではなく永続的な install-state を使用する
- リポジトリには `ecc` と `install-apply.js` による統一 Node エントリポイントがすでにある

### 依然として残る結合 (Current Coupling Still Present)

1. `install-executor.js` は以前より小さいが、依然として多くのプランニング層と具体化層を同時に担っている。
   リクエスト境界は抽出されたが、legacy リクエスト変換、manifest-plan の展開、オペレーションの具体化は依然として同居している。
2. ターゲットアダプターは依然として薄すぎる。
   今日は主にルート解決と宛先パスのスキャフォールドにとどまる。実際のインストールセマンティクスは executor の分岐とパスヒューリスティクスに残っている。
3. planner/executor 境界はまだ十分にクリーンではない。
   `install-manifests.js` は module を解決するが、最終的なインストールオペレーションセットは依然として executor 固有のロジックで一部構築されている。
4. ライフサイクル挙動は安定した module セマンティクスよりも低レベルな記録オペレーションに依存している。
   単純なファイルコピーでは機能するが、merge/generate/remove の挙動では脆くなる。
5. 互換モードはメインのインストーラーランタイムに直接混在している。
   legacy 言語インストールは並列のインストーラーアーキテクチャではなく、リクエストアダプターとして振る舞うべきである。

## 提案するモジュラーアーキテクチャの変更 (Proposed Modular Architecture Changes)

次のアーキテクチャステップは、インストーラーを明示的なレイヤーに分離し、各レイヤーが即座にファイルを変更するのではなく安定したデータを返すことである。

### 目標状態 (Target State)

望ましいインストールパイプラインは次のとおり：

1. CLI サーフェス
2. リクエスト正規化
3. module 解決
4. ターゲットプランニング
5. オペレーションプランニング
6. 実行
7. install-state の永続化
8. 同じオペレーション契約に基づくライフサイクルサービス

主な考え方は単純である：

- manifest はコンテンツを記述する
- アダプターはターゲット固有の配置セマンティクスを記述する
- planner は何が起こるべきかを記述する
- executor はそれらのプランを適用する
- ライフサイクルコマンドは同じ plan/state モデルを再利用し、再発明しない

### 提案するランタイムレイヤー (Proposed Runtime Layers)

#### 1. CLI サーフェス (CLI Surface)

責務：

- ユーザー意図のパースのみ
- install、plan、doctor、repair、uninstall へのルーティング
- 人間向けまたは JSON 出力のレンダリング

所有すべきでないもの：

- legacy 言語変換
- ターゲット固有のインストールルール
- オペレーション構築

推奨ファイル：

```text
scripts/ecc.js
scripts/install-apply.js
scripts/install-plan.js
scripts/doctor.js
scripts/repair.js
scripts/uninstall.js
```

これらはエントリポイントとして残るが、ライブラリモジュールの薄いラッパーになる。

#### 2. リクエスト正規化 (Request Normalizer)

責務：

- 生の CLI フラグを正規化されたインストールリクエストへ変換する
- legacy 言語インストールを互換リクエスト形状へ変換する
- 混在または曖昧な入力を早期に拒否する

推奨される正規化リクエスト：

```json
{
  "mode": "manifest",
  "target": "cursor",
  "profile": "developer",
  "modules": [],
  "legacyLanguages": [],
  "dryRun": false
}
```

または、互換モードでは：

```json
{
  "mode": "legacy-compat",
  "target": "claude",
  "profile": null,
  "modules": [],
  "legacyLanguages": ["typescript", "python"],
  "dryRun": false
}
```

これにより、パイプラインの残りはリクエストが旧 CLI 構文か新 CLI 構文かを無視できる。

#### 3. Module リゾルバ (Module Resolver)

責務：

- manifest カタログの読み込み
- 依存関係の展開
- 競合の拒否
- ターゲットごとに未サポートの module のフィルタリング
- 正規化された解決オブジェクトの返却

このレイヤーは純粋で読み取り専用であるべき。

知るべきでないもの：

- 宛先ファイルシステムパス
- merge セマンティクス
- コピー戦略

現在の最も近いファイル：

- `scripts/lib/install-manifests.js`

推奨される分割：

```text
scripts/lib/install/catalog.js
scripts/lib/install/resolve-request.js
scripts/lib/install/resolve-modules.js
```

#### 4. ターゲットプランナー (Target Planner)

責務：

- インストールターゲットアダプターの選択
- ターゲットルートの解決
- install-state パスの解決
- module からターゲットへのマッピングルールの展開
- ターゲット対応のオペレーション意図の出力

ターゲット固有の意味はここに属する。

例：

- Claude は `~/.claude` 配下のネイティブ階層を保持する可能性がある
- Cursor はバンドルされた `.cursor` ルート子要素を rules と異なる方法で同期する可能性がある
- 生成された config はターゲットによって merge または replace セマンティクスを要求する可能性がある

現在の最も近いファイル：

- `scripts/lib/install-targets/helpers.js`
- `scripts/lib/install-targets/registry.js`

推奨される進化：

```text
scripts/lib/install/targets/registry.js
scripts/lib/install/targets/claude-home.js
scripts/lib/install/targets/cursor-project.js
scripts/lib/install/targets/antigravity-project.js
```

各アダプターは最終的に `resolveRoot` 以上を公開すべきである。
ターゲットファミリー向けのパスと戦略マッピングを所有すべきである。

#### 5. オペレーションプランナー (Operation Planner)

責務：

- module 解決とアダプタールールを型付きオペレーショングラフへ変換する
- 次のような第一級オペレーションを出力する：
  - `copy-file`
  - `copy-tree`
  - `merge-json`
  - `render-template`
  - `remove`
- 所有権と検証メタデータの付与

これは現在のインストーラーに欠けているアーキテクチャ上の継ぎ目である。

今日、オペレーションは一部スキャフォールドレベル、一部 executor 固有である。
ECC 2.0 ではオペレーションプランニングを独立したフェーズにし、次を可能にすべきである：

- `plan` が実行の真のプレビューになる
- `doctor` が現在のファイルだけでなく意図した挙動を検証できる
- `repair` が欠落した作業を正確かつ安全に再構築できる
- `uninstall` が管理対象オペレーションのみを逆転できる

#### 6. 実行エンジン (Execution Engine)

責務：

- 型付きオペレーショングラフの適用
- 上書きと所有権ルールの強制
- 安全な書き込みのステージング
- 最終的に適用されたオペレーション結果の収集

このレイヤーは *何をするか* を決定すべきではない。
提供されたオペレーション種別を *どのように* 安全に適用するかだけを決定すべきである。

現在の最も近いファイル：

- `scripts/lib/install-executor.js`

推奨されるリファクタリング：

```text
scripts/lib/install/executor/apply-plan.js
scripts/lib/install/executor/apply-copy.js
scripts/lib/install/executor/apply-merge-json.js
scripts/lib/install/executor/apply-remove.js
```

これにより executor ロジックは、1つの大きな分岐ランタイムから小さなオペレーションハンドラーの集合へと変わる。

#### 7. Install-State ストア (Install-State Store)

責務：

- install-state の検証と永続化
- 正規化されたリクエスト、解決、適用オペレーションの記録
- インストールを逆算させずにライフサイクルコマンドをサポート

現在の最も近いファイル：

- `scripts/lib/install-state.js`

このレイヤーはすでに適切な形状に近い。主な残りの変更は、merge/generate セマンティクスが実装されたら、より豊富なオペレーションメタデータを保存することである。

#### 8. ライフサイクルサービス (Lifecycle Services)

責務：

- `list-installed`：状態のみを検査
- `doctor`：望ましい/install-state ビューと現在のファイルシステムを比較
- `repair`：状態からプランを再生成し、安全なオペレーションを再適用
- `uninstall`：ECC 所有の出力のみを削除

現在の最も近いファイル：

- `scripts/lib/install-lifecycle.js`

このレイヤーは最終的に生の `copy-file` レコードではなく、オペレーション種別と所有権ポリシーで動作すべきである。

## 提案するファイルレイアウト (Proposed File Layout)

クリーンなモジュラー最終状態は、おおよそ次のようになる：

```text
scripts/lib/install/
  catalog.js
  request.js
  resolve-modules.js
  plan-operations.js
  state-store.js
  targets/
    registry.js
    claude-home.js
    cursor-project.js
    antigravity-project.js
    codex-home.js
    opencode-home.js
  executor/
    apply-plan.js
    apply-copy.js
    apply-merge-json.js
    apply-render-template.js
    apply-remove.js
  lifecycle/
    discover.js
    doctor.js
    repair.js
    uninstall.js
```

これはパッケージング分割ではない。
現在のリポジトリ内でのコード所有権分割であり、各レイヤーが1つの役割を持つ。

## 現在のファイルからの移行マップ (Migration Map From Current Files)

最もリスクの低い移行パスは、書き直しではなく段階的な進化である。

### 維持 (Keep)

- 公開互換シムとしての `install.sh`
- 統一 CLI としての `scripts/ecc.js`
- 状態ストアの出発点としての `scripts/lib/install-state.js`
- 現在のターゲットアダプター ID と状態の場所

### 抽出 (Extract)

- `scripts/lib/install-executor.js` からのリクエストパースと互換変換
- executor 分岐からターゲットアダプターと planner モジュールへのターゲット対応オペレーションプランニング
- 共有ライフサイクルモノリスから小さなサービスへのライフサイクル固有の分析

### 段階的に置換 (Replace Gradually)

- 広範なパスコピーヒューリスティクスを型付きオペレーションへ
- スキャフォールドのみのアダプタープランニングをアダプター所有のセマンティクスへ
- legacy 言語インストール分岐を、同じ planner/executor パイプラインへの legacy リクエスト変換へ

## 次に行うべき即時のアーキテクチャ変更 (Immediate Architecture Changes To Make Next)

目標が「とりあえず動く」ではなく ECC 2.0 であるなら、次のモジュール化ステップは次のとおりであるべき：

1. `install-executor.js` をリクエスト正規化、オペレーションプランニング、実行モジュールへ分割する
2. ターゲット固有の戦略決定をアダプター所有のプランニングメソッドへ移す
3. `repair` と `uninstall` を単純な `copy-file` レコードだけでなく型付きオペレーションハンドラーで動作させる
4. manifest にインストール戦略と所有権を教え、planner がパスヒューリスティクスに依存しなくする
5. 内部 module 境界が安定した後にのみ npm publish サーフェスを狭める

## 現在のモデルが不十分な理由 (Why The Current Model Is Not Enough)

今日、ECC は依然として広範なペイロードコピー装置のように振る舞う：

- `install.sh` は言語ファーストでターゲット分岐が多い
- ターゲットはディレクトリレイアウトに一部暗黙的
- uninstall、repair、doctor は存在するが、依然として初期のライフサイクルコマンド
- リポジトリは過去のインストールが実際に何を書き込んだかを証明できない
- `package.json` の publish サーフェスは依然として広い

これはメガプランですでに指摘された問題を生む：

- ユーザーは harness やワークフローに必要以上のコンテンツを取得する
- インストールが記録されないためサポートとアップグレードが難しい
- インストールロジックがシェル分岐に重複するためターゲット挙動がドリフトする
- Codex や OpenCode のような将来ターゲットは、安定したインストール契約の再利用ではなく、より多くの特殊ケースロジックを要求する

## ECC 2.0 設計テーゼ (ECC 2.0 Design Thesis)

選択的インストールは次のようにモデル化されるべきである：

1. リクエストされた意図を正規化された module グラフへ解決する
2. そのグラフをターゲットアダプター経由で変換する
3. 決定的なインストールオペレーションセットを実行する
4. install-state を永続的な真実の源として書き込む

つまり ECC 2.0 には1つではなく2つの契約が必要である：

- コンテンツ契約
  どの module が存在し、互いにどう依存するか
- ターゲット契約
  それらの module が Claude、Cursor、Antigravity、Codex、OpenCode 内にどう配置されるか

現在のリポジトリは初期形態では前半のみを持っていた。
現在のリポジトリは最初の完全な垂直スライスを持つが、ターゲット固有セマンティクスの全体はまだない。

## 設計制約 (Design Constraints)

1. `everything-claude-code` を正規のソースリポジトリとして維持する。
2. 移行中は既存の `install.sh` フローを保持する。
3. 同じ planner から home スコープと project スコープのターゲットをサポートする。
4. 推測なしで uninstall/repair/doctor を可能にする。
5. ターゲットごとのコピーロジックが module 定義へ漏れ戻るのを避ける。
6. 将来の Codex と OpenCode サポートを書き直しではなく追加的に保つ。

## 正規アーティファクト (Canonical Artifacts)

### 1. Module カタログ (Module Catalog)

module カタログは正規のコンテンツグラフである。

すでに実装済みのフィールド：

- `id`
- `kind`
- `description`
- `paths`
- `targets`
- `dependencies`
- `defaultInstall`
- `cost`
- `stability`

ECC 2.0 でまだ必要なフィールド：

- `installStrategy`
  例：`copy`、`flatten-rules`、`generate`、`merge-config`
- `ownership`
  ECC がターゲットパスを完全に所有するか、その配下の生成ファイルのみを所有するか
- `pathMode`
  例：`preserve`、`flatten`、`target-template`
- `conflicts`
  1つのターゲット上で共存できない module またはパスファミリー
- `publish`
  module がデフォルトでパッケージ化されるか、オプションか、インストール後に生成されるか

推奨される将来の形状：

```json
{
  "id": "hooks-runtime",
  "kind": "hooks",
  "paths": ["hooks", "scripts/hooks"],
  "targets": ["claude", "cursor", "opencode"],
  "dependencies": [],
  "installStrategy": "copy",
  "pathMode": "preserve",
  "ownership": "managed",
  "defaultInstall": true,
  "cost": "medium",
  "stability": "stable"
}
```

### 2. Profile カタログ (Profile Catalog)

profile は薄く保つ。

ユーザー意図を表現し、ターゲットロジックを重複させない。

すでに実装済みの例：

- `core`
- `developer`
- `security`
- `research`
- `full`

まだ必要なフィールド：

- `defaultTargets`
- `recommendedFor`
- `excludes`
- `requiresConfirmation`

これにより ECC 2.0 は次のようなことを言える：

- `developer` は Claude と Cursor の推奨デフォルト
- `research` は狭いローカルインストールには重い可能性がある
- `full` は許可されるがデフォルトではない

### 3. ターゲットアダプター (Target Adapters)

これが主に欠けているレイヤーである。

module グラフは次を知るべきではない：

- Claude home がどこにあるか
- Cursor がコンテンツをどうフラット化または再マップするか
- どの config ファイルが盲目的なコピーではなく merge セマンティクスを必要とするか

それはターゲットアダプターに属する。

推奨インターフェース：

```ts
type InstallTargetAdapter = {
  id: string;
  kind: "home" | "project";
  supports(target: string): boolean;
  resolveRoot(input?: string): Promise<string>;
  planOperations(input: InstallOperationInput): Promise<InstallOperation[]>;
  validate?(input: InstallOperationInput): Promise<ValidationIssue[]>;
};
```

推奨される最初のアダプター：

1. `claude-home`
   `~/.claude/...` に書き込む
2. `cursor-project`
   `./.cursor/...` に書き込む
3. `antigravity-project`
   `./.agent/...` に書き込む
4. `codex-home`
   後日
5. `opencode-home`
   後日

これは session-adapter 発見ドキュメントですでに提案されたパターンと一致する：正規契約が先、harness 固有アダプターが後。

## インストールプランニングモデル (Install Planning Model)

現在の `scripts/install-plan.js` CLI は、リポジトリがリクエストされた module をフィルタ済み module セットへ解決できることを証明している。

ECC 2.0 には次のレイヤーが必要：オペレーションプランニング。

推奨フェーズ：

1. 入力正規化
   - `--target` のパース
   - `--profile` のパース
   - `--modules` のパース
   - オプションで legacy 言語引数の変換
2. module 解決
   - 依存関係の展開
   - 競合の拒否
   - サポート対象ターゲットによるフィルタリング
3. アダプタープランニング
   - ターゲットルートの解決
   - 正確なコピーまたは生成オペレーションの導出
   - config の merge とターゲット再マップの特定
4. dry-run 出力
   - 選択された module の表示
   - スキップされた module の表示
   - 正確なファイルオペレーションの表示
5. 変更
   - オペーションプランの実行
6. 状態書き込み
   - 成功完了後にのみ install-state を永続化

推奨オペレーション形状：

```json
{
  "kind": "copy",
  "moduleId": "rules-core",
  "source": "rules/common/coding-style.md",
  "destination": "/Users/example/.claude/rules/ecc/common/coding-style.md",
  "ownership": "managed",
  "overwritePolicy": "replace"
}
```

その他のオペレーション種別：

- `copy`
- `copy-tree`
- `flatten-copy`
- `render-template`
- `merge-json`
- `merge-jsonc`
- `mkdir`
- `remove`

## Install-State 契約 (Install-State Contract)

install-state は ECC 1.x に欠けていた永続的な契約である。

推奨パス規約：

- Claude ターゲット：
  `~/.claude/ecc/install-state.json`
- Cursor ターゲット：
  `./.cursor/ecc-install-state.json`
- Antigravity ターゲット：
  `./.agent/ecc-install-state.json`
- 将来の Codex ターゲット：
  `~/.codex/ecc-install-state.json`

推奨ペイロード：

```json
{
  "schemaVersion": "ecc.install.v1",
  "installedAt": "2026-03-13T00:00:00Z",
  "lastValidatedAt": "2026-03-13T00:00:00Z",
  "target": {
    "id": "claude-home",
    "root": "/Users/example/.claude"
  },
  "request": {
    "profile": "developer",
    "modules": ["orchestration"],
    "legacyLanguages": ["typescript", "python"]
  },
  "resolution": {
    "selectedModules": [
      "rules-core",
      "agents-core",
      "commands-core",
      "hooks-runtime",
      "platform-configs",
      "workflow-quality",
      "framework-language",
      "database",
      "orchestration"
    ],
    "skippedModules": []
  },
  "source": {
    "repoVersion": "2.0.0-rc.1",
    "repoCommit": "git-sha",
    "manifestVersion": 1
  },
  "operations": [
    {
      "kind": "copy",
      "moduleId": "rules-core",
      "destination": "/Users/example/.claude/rules/ecc/common/coding-style.md",
      "digest": "sha256:..."
    }
  ]
}
```

状態要件：

- uninstall が ECC 管理出力のみを削除するのに十分な詳細
- repair が望ましい状態と実際にインストールされたファイルを比較するのに十分な詳細
- doctor が推測ではなくドリフトを説明するのに十分な詳細

## ライフサイクルコマンド (Lifecycle Commands)

次のコマンドは install-state のライフサイクルサーフェスである：

1. `ecc list-installed`
2. `ecc uninstall`
3. `ecc doctor`
4. `ecc repair`

現在の実装状況：

- `ecc list-installed` は `node scripts/list-installed.js` にルーティング
- `ecc uninstall` は `node scripts/uninstall.js` にルーティング
- `ecc doctor` は `node scripts/doctor.js` にルーティング
- `ecc repair` は `node scripts/repair.js` にルーティング
- 移行中は legacy スクリプトエントリポイントも利用可能

### インストール済み一覧 (list-installed)

責務：

- ターゲット ID とルートの表示
- リクエストされた profile/module の表示
- 解決された module の表示
- ソースバージョンとインストール時刻の表示

### アンインストール (uninstall)

責務：

- install-state の読み込み
- 状態に記録された ECC 管理宛先のみを削除
- ユーザー作成の無関係なファイルはそのまま残す
- クリーンアップ成功後にのみ install-state を削除

### 診断 (doctor)

責務：

- 欠落した管理ファイルの検出
- 予期しない config ドリフトの検出
- 存在しなくなったターゲットルートの検出
- manifest/バージョン不一致の検出

### 修復 (repair)

責務：

- install-state から望ましいオペーションプランを再構築
- 欠落またはドリフトした管理ファイルを再コピー
- 互換マップがない限り、リクエストされた module が現在の manifest に存在しなくなった場合は repair を拒否

## Legacy 互換レイヤー (Legacy Compatibility Layer)

現在の `install.sh` は次を受け付ける：

- `--target <claude|cursor|antigravity>`
- 言語名のリスト

この挙動はユーザーがすでに依存しているため、一括で消せない。

ECC 2.0 は legacy 言語引数を互換リクエストへ変換すべきである。

推奨アプローチ：

1. legacy モードの既存 CLI 形状を維持する
2. 言語名を次のような module リクエストへマップする：
   - `rules-core`
   - ターゲット互換の rule サブセット
3. legacy インストールでも install-state を書き込む
4. リクエストに `legacyMode: true` を付与する

例：

```json
{
  "request": {
    "legacyMode": true,
    "legacyLanguages": ["typescript", "python"]
  }
}
```

これにより旧挙動を維持しつつ、すべてのインストールを同じ状態契約へ移行できる。

## Publish 境界 (Publish Boundary)

現在の npm パッケージは依然として `package.json` 経由で広範なペイロードを publish している。

ECC 2.0 はこれを慎重に改善すべきである。

推奨シーケンス：

1. まず1つの正規 npm パッケージを維持する
2. publish 形状を変える前に、manifest でインストール時選択を駆動する
3. 安全な場合にのみ、後でパッケージ化サーフェスを削減することを検討する

理由：

- 選択的インストールは積極的なパッケージ手術より先に出荷できる
- uninstall と repair は publish 変更より install-state に依存する
- パッケージソースが統一されていれば Codex/OpenCode サポートは容易

将来の可能性のある方向：

- profile ごとの生成された slim bundle
- ターゲット固有の生成 tarball
- 重い module のオプションリモート取得

これらは Phase 3 以降であり、profile 対応インストールの前提条件ではない。

## ファイルレイアウト推奨 (File Layout Recommendation)

推奨される次のファイル：

```text
scripts/lib/install-targets/
  claude-home.js
  cursor-project.js
  antigravity-project.js
  registry.js
scripts/lib/install-state.js
scripts/ecc.js
scripts/install-apply.js
scripts/list-installed.js
scripts/uninstall.js
scripts/doctor.js
scripts/repair.js
tests/lib/install-targets.test.js
tests/lib/install-state.test.js
tests/lib/install-lifecycle.test.js
```

`install.sh` は移行中ユーザー向けエントリポイントとして残せるが、ターゲットごとのシェル分岐を増やし続けるのではなく、Node ベースの planner と executor の薄いシェルになるべきである。

## 実装シーケンス (Implementation Sequence)

### Phase 1: 契約へのプランナー (Planner To Contract)

1. 現在の manifest スキーマとリゾルバを維持する
2. 解決済み module の上にオペレーションプランニングを追加する
3. `ecc.install.v1` 状態スキーマを定義する
4. インストール成功時に install-state を書き込む

### Phase 2: ターゲットアダプター (Target Adapters)

1. Claude インストール挙動を `claude-home` アダプターへ抽出する
2. Cursor インストール挙動を `cursor-project` アダプターへ抽出する
3. Antigravity インストール挙動を `antigravity-project` アダプターへ抽出する
4. `install.sh` を引数パースとアダプター呼び出しに縮小する

### Phase 3: ライフサイクル (Lifecycle)

1. より強いターゲット固有の merge/remove セマンティクスを追加する
2. 非コピーオペレーション向けに repair/uninstall カバレッジを拡張する
3. 広いフォルダではなく module グラフへパッケージ出荷サーフェスを縮小する
4. `ecc-install` を `ecc install` の薄いエイリアスにするタイミングを決める

### Phase 4: Publish と将来ターゲット (Publish And Future Targets)

1. `package.json` publish サーフェスの安全な削減を評価する
2. `codex-home` を追加する
3. `opencode-home` を追加する
4. パッケージング圧力が高いままなら、生成された profile bundle を検討する

## リポジトリ内の即時の次ステップ (Immediate Repo-Local Next Steps)

このリポジトリで最もシグナルの高い次の実装は次のとおり：

1. config 系 module 向けのターゲット固有 merge/remove セマンティクスを追加する
2. 単純な copy-file オペレーションを超えて repair と uninstall を拡張する
3. 広いフォルダではなく module グラフへパッケージ出荷サーフェスを縮小する
4. `ecc-install` を別物として残すか `ecc install` にするかを決める
5. 次を固定するテストを追加する：
   - ターゲット固有の merge/remove 挙動
   - 非コピーオペレーション向けの repair と uninstall の安全性
   - 統一 `ecc` CLI ルーティングと互換性保証

## 未解決の問い (Open Questions)

1. rules は legacy モードで言語アドレス可能のまま永続すべきか、移行期間中のみか？
2. `platform-configs` は常に `core` と一緒にインストールすべきか、より小さなターゲット固有 module に分割すべきか？
3. config merge セマンティクスはオペレーションレベルで記録すべきか、アダプターロジックのみか？
4. 重い skill ファミリーは最終的にパッケージ時包含ではなく fetch-on-demand へ移すべきか？
5. Codex と OpenCode ターゲットアダプターは Claude/Cursor ライフサイクルコマンドが安定した後にのみ出荷すべきか？

## 推奨 (Recommendation)

現在の manifest リゾルバをインストールのアダプター `0` として扱う：

1. 現在のインストールサーフェスを保持する
2. 実際のコピー挙動をターゲットアダプターの背後へ移す
3. すべての成功インストールで install-state を書き込む
4. uninstall、doctor、repair を install-state のみに依存させる
5. その後にのみパッケージングを縮小するか、より多くのターゲットを追加する

これが、ECC 1.x インストーラーの肥大化から、決定的でサポート可能かつ拡張可能な ECC 2.0 インストール/コントロール契約への最短経路である。
