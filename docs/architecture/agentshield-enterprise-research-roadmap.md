# AgentShield エンタープライズ リサーチ ロードマップ (AgentShield Enterprise Research Roadmap)

生成日: 2026-05-12; 2026-05-18 の AgentShield フリートチケットと Mini Shai-Hulud IOC 証拠で更新。

これは次の AgentShield エンタープライズ反復向けの計画アーティファクトです。AgentShield コードは変更しません。目標は、現在のスキャナー、ポリシーゲート、コーパス、レポート面を、複数 harness で AI コーディングエージェントを運用するチーム向けのセキュリティコントロールプレーンに変えることです。

## レビューした証拠 (Evidence Reviewed)

現在の AgentShield リポジトリ状態:

- クリーンな `main` 上の AgentShield チェックアウト。
- `README.md`、`API.md`、`package.json`、`.github/workflows/*`、および `src/`/`tests/` モジュールレイアウト。
- 現在サポートするユーザー面: `agentshield scan`、`agentshield init`、`agentshield miniclaw start`、スキャナー JSON、MiniClaw API、GitHub Action、HTML、SARIF、markdown、ターミナル、JSON レポート。
- 現在のエンタープライズ的な面: ポリシーパック、GitHub Action ポリシー強制、SARIF ポリシー違反、サプライチェーン provenance、コーパスベンチマーク、HTML エグゼクティブレポート、例外ライフサイクル監査。

公式 GitHub リポジトリまたは README ソースから確認した外部参照:

- [stablyai/orca](https://github.com/stablyai/orca): マルチエージェント IDE、worktree 分離、ライブエージェントステータス、GitHub 統合、diff レビュー、通知。
- [superset-sh/superset](https://github.com/superset-sh/superset): worktree オーケストレーション、組み込み diff レビュー、ワークスペースプリセット、汎用 CLI エージェント互換を備えた AI エージェントエディター。
- [standardagents/dmux](https://github.com/standardagents/dmux): ライフサイクルフック、マルチエージェント起動、ペイン可視性、マージ/PR ワークフローを備えた tmux/worktree マルチプレクサ。
- [jarrodwatts/claude-hud](https://github.com/jarrodwatts/claude-hud): Claude Code ステータスライン、コンテキスト健全性、ツール活動、エージェント追跡、todo 進捗、トランスクリプト解析、利用テレメトリ。
- [stanford-iris-lab/meta-harness](https://github.com/stanford-iris-lab/meta-harness): 反復可能タスク、ログされた proposer 相互作用、評価された scaffold 変更による harness 最適化。
- [greyhaven-ai/autocontext](https://github.com/greyhaven-ai/autocontext): トレース、スコア付き生成、プレイブック、永続知識、シナリオ評価、オプションの本番トレースを伴う再帰的改善ループ。
- [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent): 自己改善 skills、メモリ、セッション検索、マルチプラットフォームゲートウェイ、スケジュール自動化、ターミナルバックエンド、軌跡生成。
- [anthropics/claude-code](https://github.com/anthropics/claude-code): ターミナル、IDE、GitHub、プラグイン、権限、MCP、データ保持面。
- [anomalyco/opencode](https://github.com/anomalyco/opencode): build/plan エージェント、デスクトップ beta、クライアント/サーバーアーキテクチャ、LSP サポートを備えたプロバイダー非依存のオープンソースコーディングエージェント。
- [opencode-ai/opencode](https://github.com/opencode-ai/opencode): セッション、プロバイダー、LSP、ファイル変更追跡、カスタムコマンド、auto-compact を備えた旧アーカイブ Go ベースのターミナルエージェント。
- [zed-industries/zed](https://github.com/zed-industries/zed): 厳格なライセンス/コンプライアンス CI 期待を伴う高性能マルチプレイヤーエディター。
- [aidenybai/ghast](https://github.com/aidenybai/ghast): Ghostty を中心としたネイティブターミナルマルチプレクサ。ワークスペースグループ化、分割ペイン、ドラッグ/ドロップ、通知、ターミナル検索。

ローカル Claude Code ソース調査:

- プライベート Claude Code ソーススナップショットから、シークレット以外のローカルファイル/モジュール形状のみをレビュー。
- 関連面: `tools/`、`utils/permissions/`、`utils/mcp/`、`utils/hooks/`、`utils/plugins/`、`types/permissions.ts`、`types/plugin.ts`、`remote/`、`tasks/`、`assistant/sessionHistory.ts`、セッション/履歴ユーティリティ。
- コードはコピーしていない。要点は、AgentShield が `.claude/` ツリーを唯一の正とみなすのではなく、permissions、plugins、MCP、hooks、リモートセッション、task/subagent 活動、履歴を第一級の監査ドメインとして追跡すべきだということ。

## 現在の AgentShield ポジション (Current AgentShield Position)

AgentShield はすでに静的 lint ツール以上のものです:

- ルールカバレッジは secrets、permissions、hooks、MCP サーバー、エージェント設定、プロンプトインジェクション、サプライチェーン、汚染解析、sandbox 実行、ポリシー評価、ランタイム修復/ステータス、コーパス検証、MiniClaw、Opus 分析に及ぶ。
- レポートは人間と機械の両方で使える: ターミナル、JSON、markdown、HTML、SARIF、スキャンログ、GitHub Action 出力。
- エンタープライズフックが存在: ポリシーパック、例外メタデータ、期限切れ間近/期限切れ例外レポート、SARIF コードスキャン、job-summary 出力。
- 精度作業が進行中: `runtimeConfidence`、テンプレート/例の重み付け、docs-example のダウングレード、インストール済み Claude plugin-cache 信頼度、hook-manifest 解決、false-positive 監査ガイダンス、コーパス readiness。
- evidence-pack 消費は下流ツール向けに第一級になった:
  `agentshield evidence-pack inspect` はバンドルを検証し、レポートスコア、finding 数、runtime confidence、ポリシー、ベースライン、サプライチェーン、CI コンテキスト、修復、不正アーティファクトエラーのコンパクト JSON/テキスト要約を出力する。
- フリートレベルの evidence-pack 消費にはローカルルーティングプリミティブがある:
  `agentshield evidence-pack fleet <dirs...> [--json]` は複数の検査済みバンドルを ready、security-blocker、policy-review、baseline-regression、supply-chain-review、invalid ルートへ集約する。
- ECC-Tools はホストセキュリティレビューでそのフリートプリミティブを消費:
  `agentshield-evidence/fleet-summary.json` は invalid pack、security blocker、policy review、baseline regression、supply-chain review をホスト finding へルーティングする。

2026-05-16 更新: AgentShield PR #87 は
`26bb44650663816d07180e0d20c1895e431a326c` としてマージ。インストール済み Claude plugin cache コンテンツを `runtimeConfidence: plugin-cache` として分類し、非 secret の plugin-cache スコア影響を `0.5x` に保ち、リポジトリローカルの非 Claude `plugins/cache` パスをダウングレードせず、キャッシュされた hook 実装が active な `hook-code` として見えないように plugin-cache 分類を優先する。
AgentShield PR #88 は
`65ed6e2a87545dc99d962b58413f49096a4d70ec` としてマージ。`agentshield evidence-pack inspect <dir> [--json]` を追加し、読み戻し前にバンドルを検証し、消費者向け evidence アーティファクトを要約し、不正だが有効な JSON アーティファクトが検査をクラッシュさせない。
AgentShield PR #89 は
`521ada9091bb6d818511ab8589ae675b920c106a` としてマージ。`agentshield evidence-pack fleet <dirs...> [--json]` を追加し、各 pack を inspect パスで検証し、finding、ポリシー、ベースライン、サプライチェーン、修復の合計を集約し、各 pack を決定論的フリートルートへ割り当てる。
AgentShield commit `840952a7a07f820f24081c43df656d7f7295f23b` は
優先度、ラベル、タイトル、Markdown 本文を伴う Linear/オペレーター対応フリートレビューチケットペイロードを追加。同コミットはクラスタ内 Vault エンドポイントと一時 lockfile breadcrumb 向けの現在の Mini Shai-Hulud/TanStack IOC カバレッジを拡張し、ローカル typecheck、lint、フルテスト、`git diff --check`、GitHub CI/Self-Scan/Action-test 証拠を伴う。

フリートルーティング後の次の反復は、デフォルトで「さらに regex ルールを追加」ではない。ECC-Tools のフォローアップルーティングはフリート要約を消費し、ホスト finding にソース evidence パスを表示し、最初のクロスハーネスポリシースライスは AgentShield フリートルートターゲットパスを harness オーナーレビューへリンクする。AgentShield フリート出力は `reviewItems` とソース evidence パス、オーナー対応推奨、ルーティングされた pack 向けコピー可能チケットペイロードも出力する。より高いレバレッジは、ルーティングされたフリート finding 向けの永続的オペレーター承認/読み戻しとワークフロー自動化である。

## エンタープライズギャップ (Enterprise Gaps)

### 1. 組織ベースラインとドリフト (Organization Baselines And Drift)

エンタープライズ購入者は、リポジトリ、チーム、エージェントフリートが時間とともに安全になっているかリスクが増しているかを知る必要がある。AgentShield にはスキャンログとベースライン比較モジュールがあり、PR #63 はそのドリフトを GitHub Action の入力、出力、アノテーション、job-summary 証拠で公開した。PR #64 は `agentshield baseline write` による第一級ベースラインスナップショット作成を追加した。残るプロダクト面は、CLI ドリフト要約、evidence pack、オーナー対応デルタを明示することである。

目標能力:

- `agentshield baseline write --path .claude --output agentshield-baseline.json`
- `agentshield scan --baseline agentshield-baseline.json`
- 新規、修正、変更なし、抑制、ポリシー例外の finding セクション。
- 一時点のグレードだけでなく「セキュリティ姿勢が変化した」と投稿する GitHub Action 出力。

### 2. マルチハーネス セキュリティ アダプター (Multi-Harness Security Adapters)

市場は単一ツールではなく、多くの並列エージェント harness へ向かっている。Orca、Superset、dmux、OpenCode、Claude Code、Codex、Gemini、Zed、ターミナルマルチプレクサはそれぞれ異なるセキュリティ面を生む。

目標能力:

- `claude-code`、`opencode`、`codex`、`gemini`、`zed`、`dmux`、`orca`、`superset`、`generic-terminal` 向けの小さなアダプターレジストリ。
- 各アダプターは config パス、権限概念、プラグイン面、MCP/ツール規約、履歴/セッション面、CI 証拠を宣言する。
- レポート出力は harness と信頼度で finding をグループ化し、テンプレート/docs の finding が active なランタイム露出に見えないようにする。

### 3. セッションと Worktree 認識 (Session And Worktree Awareness)

Worktree ネイティブオーケストレーターはリスクモデルを変える。チームはブランチ、シェル、MCP 設定、ローカル状態をそれぞれ持つ多くのエージェントを並列実行できる。

目標能力:

- ブランチ、worktree パス、エージェント名、セッション ID、プロバイダー、オーケストレーター向けのオプションスキャンメタデータ。
- どの worktree が新しい権限を導入したか、どのエージェント実行がリスクのある MCP を追加したか、どのブランチがポリシーを緩めたか、最終マージブランチが修正したかを答えるスキャン履歴テーブル。
- ステータスライン、GitHub チェック、ローカルダッシュボードで使えるコンパクトな「セキュリティ HUD」要約。

### 4. 購入者と監査人向け Evidence Pack (Evidence Packs For Buyers And Auditors)

HTML レポートは今日の購入者向けアーティファクトとして適切; ネイティブ PDF は延期。より深いニーズは、監査、セキュリティレビュー、顧客アンケートに添付できるポータブル証拠バンドルである。

目標能力:

- `agentshield scan --evidence-pack out/agentshield-evidence`
- バンドルに JSON レポート、HTML レポート、SARIF、ポリシー評価、例外監査、ベースライン diff、依存/provenance 要約、アーティファクトの解釈方法を説明する短い README を含める。
- secrets、ローカルパス、ユーザー名、プロジェクト名向けのオプション redaction モード。

### 5. 回帰コーパスとリファレンスセット (Regression Corpus And Reference Sets)

Meta-Harness と Autocontext は同じ教訓を示す: 改善にはスコア付きシナリオ、トレース、プレイブックが必要。AgentShield にはコーパスベンチマークがあるが、エンタープライズ信頼には false positive、false negative、ポリシー回帰向けのキュレーション済みリファレンスセットが必要。

目標能力:

- 重要ルール、false-positive 抑制、ポリシー例外、テンプレート/docs 例、プラグインマニフェスト、hook-code 解決向けのバージョン付きシナリオフィクスチャ。
- 集約 readiness だけでなく、カテゴリ別 precision/coverage レポート。
- リリース前に通過必須の「精度回帰なし」ゲート。
- 抑制が存在する理由と期限切れ条件を説明するプレイブックノート。

### 6. 修復ワークフロー (Remediation Workflow)

セキュリティツールは finding をメンテナーを溢れさせずに説明責任ある作業へ変えるときにエンタープライズ級になる。

目標能力:

- 安全な変換向けのワンクリックまたは CLI 生成修復ブランチ。
- ファイル順ではなくオーナーとリスクで finding をグループ化するポリシーコメント。
- チェックランアノテーション、issue 上限、Linear 同期、延期バックログエクスポート向け GitHub App サポート。
- 繰り返しスキャンで重複 issue を避ける finding フィンガープリント。

### 7. 脅威インテリジェンスとパッケージ評判 (Threat Intelligence And Package Reputation)

エージェントセキュリティは MCP パッケージ、プラグインリポジトリ、アクションバンドル、急速に変わる CLI エコシステムに依存する。静的チェックには維持された外部評判レイヤーが必要。

目標能力:

- 既知の MCP/パッケージリスク、CVE、マルウェアパッケージ名、疑わしいインストールスクリプト、可変 git 依存、既知の良好パッケージ向けのローカルファースト threat-intel キャッシュ。
- オフライン決定論モードは引き続き利用可能。
- オンライン enrichment はオプトインで、外部主張ごとに明確な provenance を生成する。

### 8. 商用とチームコントロール (Commercial And Team Controls)

AgentShield は概念的に ECC Tools GitHub App と接続済み。ネイティブ GitHub 決済はプロダクトパスを具体化する: 無料ローカルスキャン、有料組織ポリシーゲート、有料 evidence バンドル、有料ドリフト/履歴。

目標能力:

- ティア対応 GitHub App チェック: 無料静的スキャン、有料組織ポリシー強制、有料 evidence pack、有料履歴ドリフト、有料深掘り分析。
- ポリシーオーナーと例外承認者向けの seat/チームマッピング。
- ECC-Tools と共有する課金 readiness チェックで、決済状態が強制挙動を静かに変えないようにする。

## 推奨ビルド順序 (Recommended Build Order)

### スライス 1: ベースラインドリフト MVP (Slice 1: Baseline Drift MVP)

最小のエンタープライズコントロールプレーン primitive を実装: このスキャンを最後に受け入れたベースラインと比較する。

成果物:

- ベースライン JSON スキーマ。
- ベースライン writer と comparator。
- 新規/修正/変更なし finding 向けのターミナルと JSON レポートセクション。
- 安定フィンガープリント、修正 finding、新規 finding、ポリシー例外引き継ぎをカバーするテスト。

最初の理由:

- 既存スキャン出力を再利用する。
- CLI、GitHub Action、GitHub App の価値を同時に高める。
- ホストサービスを必要としない。

### スライス 2: Evidence Pack バンドル (Slice 2: Evidence Pack Bundle)

既存の機械・人間向けレポートをポータブル監査アーティファクトにバンドルする。

成果物:

- `--evidence-pack <dir>` CLI フラグ。
- redacted バンドル README。
- HTML、JSON、SARIF、ポリシー、例外、ベースライン diff ファイル。
- ファイルレイアウト、redaction、決定論的出力名のテスト。

2番目の理由:

- 既存レポート作業を購入者対応証拠へ変換する。
- ネイティブ PDF を延期しつつ監査引き渡しニーズを満たす。

### スライス 3: Harness アダプターレジストリ (Slice 3: Harness Adapter Registry)

harness サポートを暗黙ではなく明示にする。

成果物:

- Claude Code、OpenCode、Codex、Gemini、dmux、汎用ターミナル、プロジェクトローカルテンプレート向けアダプターメタデータ。
- どのアダプターが一致したかと理由を報告する discovery 出力。
- アダプター別レポートグループ化。
- 各アダプター向けフィクスチャディレクトリのテスト。

3番目の理由:

- ECC の harness 非依存ポジショニングと整合する。
- すべての harness が Claude の config モデルを共有しているふりをせず、将来の Zed、Orca、Superset、Hermes 統合向けの安定面を作る。

### スライス 4: コーパス精度ゲート (Slice 4: Corpus Accuracy Gate)

コーパスをベンチマークからリリースゲートへ昇格させる。

成果物:

- カテゴリ別コーパスレポート。
- 必須カテゴリ閾値。
- 既知 false-positive 抑制向け回帰スナップショット。
- 公開前にコーパス readiness を要求するリリースチェックリスト項目。

4番目の理由:

- ルール拡張時にエンタープライズ信頼性の劣化を防ぐ。
- 後の Meta-Harness/Autocontext 型改善ループ向けの永続ルートを作る。

### スライス 5: GitHub App と Linear 同期配線 (Slice 5: GitHub App And Linear Sync Wiring)

AgentShield finding を ECC-Tools フォローアップルーティングへ接続する。

成果物:

- ECC-Tools issue 上限と互換な finding フィンガープリント。
- ベースラインドリフトとポリシー違反向け Linear 対応バックログエクスポート。
- オーナー/リスクでグループ化されたチェックランアノテーション。
- 繰り返しスキャンが重複 issue をスパムしないテスト。

5番目の理由:

- スライス 1 のベースライン/フィンガープリント作業が必要。
- ローカル CLI から有料チームワークフローへの橋。

## この反復の非目標 (Non-Goals For This Iteration)

- 購入者/コンプライアンスワークフローが HTML ではなく生成 PDF を明示的に要求しない限りのネイティブ PDF 生成。
- ローカルベースライン/evidence/フィンガープリント契約が安定する前のホストダッシュボード。
- 決定論的コーパスゲートとリファレンストレースが存在する前の fine-tuning やモデル学習。
- 明示的でレビュー可能な変換とテストなしのリスク finding 向け広範な自動コード書き換え。

## 受け入れゲート (Acceptance Gates)

AgentShield エンタープライズ反復は、以下が真になるまで完了ではない:

- AgentShield リポジトリルートからローカル `npm run typecheck`、`npm run lint`、`npm test`、`npm run build` が通過する。
- ビルド済み CLI smoke テストが新フラグまたはレポートモードをカバーする。
- GitHub Action セルフテストが新しい CI 可視出力をカバーする。
- ドキュメントが無料/ローカルパスと有料/チームパスを別々に記載する。
- runtime-confidence 変更には、低信頼度の plugin/package 面が抑制されず可視のままであることを示すライブスキャン証拠を含める。
- 機能が生成する証拠は CI diff に十分決定論的である。
- ECC-Tools が finding フィンガープリントまたはバックログエクスポートを GitHub/Linear オブジェクト上限を超えず消費できる。
- GA ロードマップと Linear プロジェクトステータスがマージ済み AgentShield PR へリンクする。
