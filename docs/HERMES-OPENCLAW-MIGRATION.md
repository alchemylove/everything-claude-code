# Hermes / OpenClaw → ECC 移行 (Hermes / OpenClaw -> ECC Migration)

このドキュメントは、Hermes または OpenClaw スタイルの operator セットアップを現在の ECC モデルへ移行するための公開 migration ガイドです。

目的は private operator workspace をバイト単位で再現することではありません。

保持すべき有用な workflow surface は次のとおりです:

- 再利用可能な skill
- 安定した自動化エントリポイント
- cross-harness ポータビリティ
- scheduler / reminder / dispatch
- durable context と operator memory

一方、private に残すべき部分は次です:

- secret
- 個人データセット
- アカウント token
- ローカル限定のビジネスアーティファクト

## 移行テーゼ (Migration Thesis)

Hermes と OpenClaw はソースシステムとして扱い、最終ランタイムではありません。

ECC は durable な公開システムです:

- skill
- agent
- command
- hook
- install surface
- session adapter
- ECC 2.0 control-plane 作業

Hermes と OpenClaw は、ECC-native surface に蒸留できる反復 operator workflow を含む有用な入力です。

最短の安全なパスは:

1. 再利用可能な振る舞いを抽出する
2. ECC-native skill、hook、docs、または adapter 作業に翻訳する
3. secret と個人データをリポジトリ外に置く

## 現在のワークスペースモデル (Current Workspace Model)

現在のワークスペース分割を一貫して使います:

- ライブコード作業は `~/GitHub` 配下の clone リポジトリで行う
- リポジトリ固有のアクティブ実行 context はリポジトリレベルの `WORKING-CONTEXT.md` に置く
- より広い非コード context は KB/archive レイヤーに置ける
- durable なマルチマシン真実は GitHub、Linear、ナレッジベースを優先する

公開リポジトリ内に shadow private workspace を再構築しないでください。

## 翻訳マップ (Translation Map)

### 1. Scheduler / cron レイヤー

ソース例:

- `cron/scheduler.py`
- `jobs.py`
- 定期的な readiness または accountability ループ

翻訳先:

- 利用可能なら Claude-native scheduling
- ローカル反復性のための ECC hook / command 自動化
- issue `#1050` 配下の ECC 2.0 scheduler 作業

現状、リポジトリには適切な公開フレーミングがあります:

- 低レイテンシ repo ローカル自動化の hook
- 明示的 operator 操作の command
- 将来の長寿命 scheduling/control plane としての ECC 2.0

### 2. Gateway / dispatch レイヤー

ソース例:

- Hermes gateway
- モバイル dispatch / リモート nudge
- アクティブセッション間の operator ルーティング

翻訳先:

- ECC session adapter と control-plane 作業
- orchestration/session 検査 command
- 次の ECC 2.0 control-plane バックログ:
  - `#1045`
  - `#1046`
  - `#1047`
  - `#1048`

公開リポジトリは adapter 境界と control-plane モデルを記述すべきで、リモート operator shell がすでに完全 GA であるかのように見せるべきではありません。

### 3. Memory レイヤー

ソース例:

- `memory_tool.py`
- ローカル operator memory
- ビジネス / ops context ストア

翻訳先:

- `knowledge-ops`
- リポジトリ `WORKING-CONTEXT.md`
- GitHub / Linear / KB バックの durable context
- `#1049` 配下の将来の deep memory 作業

重要な区別:

- リポジトリ実行 context はリポジトリ近くに属する
- より広い非コード memory は KB/archive システムに属する
- 公開リポジトリは境界を文書化すべきで、private memory ダンプを保存すべきではない

### 4. Skill レイヤー

ソース例:

- Hermes skill
- OpenClaw skill
- 生成された operator playbook

翻訳先:

- workflow が再利用可能なら ECC-native トップレベル skill
- コンテンツがテンプレートのみなら docs/examples
- 振る舞いが知識型ではなく手続き型なら hook または command

最近の salvage 例:

- `knowledge-ops`
- `github-ops`
- `hookify-rules`
- `automation-audit-ops`
- `email-ops`
- `finance-billing-ops`
- `messages-ops`
- `research-ops`
- `terminal-ops`
- `ecc-tools-cost-audit`

### 5. Tool / service レイヤー

ソース例:

- カスタム service ラッパー
- API キー付きローカル tool
- ブラウザ自動化 glue

翻訳先:

- connector がある場合は MCP バック surface
- workflow ロジックが本当の資産なら ECC-native operator skill
- 欠けているのが session/runtime 調整なら adapter/control-plane 作業

private workflow が依存していたからといって、不透明なサードパーティランタイムを ECC に import しないでください。

workflow に価値がある場合:

1. 振る舞いを理解する
2. 最小の ECC-native 版を再構築する
3. ローカルで必要な auth/connector を文書化する

## 公開リポジトリにすでにあるもの (What Already Exists Publicly)

現在のリポジトリは移行の有意な部分をすでにカバーしています:

- ECC 2.0 adapter/control-plane discovery docs
- orchestration/session 検査 substrate
- operator workflow skill
- cost / billing / workflow audit skill
- cross-harness install surface
- 設定と agent surface スキャン用 AgentShield

移行問題はもはや「ゼロから」ではありません。

主に:

- 欠けている private workflow の蒸留
- 公開 docs の明確化
- ECC 2.0 operator/control-plane 構築の継続

ECC 2.0 は bounded migration audit エントリポイントを出荷しています:

- `ecc migrate audit --source ~/.hermes`
- `ecc migrate plan --source ~/.hermes --output migration-plan.md`
- `ecc migrate scaffold --source ~/.hermes --output-dir migration-artifacts`
- `ecc migrate import-skills --source ~/.hermes --output-dir migration-artifacts/skills`
- `ecc migrate import-tools --source ~/.hermes --output-dir migration-artifacts/tools`
- `ecc migrate import-plugins --source ~/.hermes --output-dir migration-artifacts/plugins`
- `ecc migrate import-schedules --source ~/.hermes --dry-run`
- `ecc migrate import-remote --source ~/.hermes --dry-run`
- `ecc migrate import-env --source ~/.hermes --dry-run`
- `ecc migrate import-memory --source ~/.hermes`

まずこれで legacy workspace を棚卸しし、検出 surface を現在の ECC2 scheduler、remote dispatch、memory graph、template、manual-translation レーンにマップしてください。

## バックログに残すべきもの (What Still Belongs In Backlog)

未解決の大きな移行テーマはすでに追跡されています:

- `#1051` Hermes/OpenClaw migration
- `#1049` deep memory layer
- `#1050` autonomous scheduling
- `#1048` universal harness compatibility layer
- `#1046` agent orchestrator
- `#1045` multi-session TUI manager
- `#1047` visual worktree manager

未解決の control-plane 作業はそこが適切な場所です。

公開 docs が存在するだけで移行が「完了」したかのように見せないでください。

## 推奨ブリングアップ順序 (Recommended Bring-Up Order)

1. 公開 ECC リポジトリを canonical 再利用レイヤーとして保つ。
2. 再利用可能な Hermes/OpenClaw workflow を ECC-native skill に1レーンずつ移植する。
3. private auth と個人 context をリポジトリ外に置く。
4. GitHub / Linear / KB システムを durable 真実として使う。
5. ECC 2.0 を完成品ではなくネイティブ operator shell への道として扱う。

## 判断ルール (Decision Rule)

Hermes または OpenClaw アーティファクトをレビューするとき、次を問う:

1. これは operator 横断で再利用可能か、個人専用か？
2. 資産は主に知識、手続き、ランタイム振る舞いのどれか？
3. 次のどれになるべきか:
   - skill
   - command
   - hook
   - doc/example
   - control-plane issue
4. 公開出荷で secret、private データセット、個人 operating state が漏れるか？

再利用可能な surface だけ出荷する。
