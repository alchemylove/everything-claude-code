# ECC v2.0.0-rc.1 リリースノート (ECC v2.0.0-rc.1 Release Notes)

## ポジショニング (Positioning)

ECC v2.0.0-rc.1 は、エージェント作業向け cross-harness operating system としての ECC の最初の release-candidate 面です。

Claude Code はコアターゲットのままです。Codex、OpenCode、Cursor、Gemini、その他 harness は同じ skills、rules、MCP 規約、operator workflow を共有できる実行面として扱われます。ECC は再利用可能 substrate；Hermes はそのレイヤーの上に乗る operator shell として文書化されています。

## 変更点 (What Changed)

- サニタイズされた Hermes セットアップガイドを公開リリースストーリーに追加。
- 1つのレビュー済み面からリリースできるよう in-repo ローンチコラテラルを追加。
- ECC を再利用 substrate、Hermes を operator shell とする分割を明確化。
- skills、hooks、MCP、rules、instructions 向け cross-harness ポータビリティモデルを文書化。
- ローカル operator パターンを公開 ECC skills に変換する Hermes import playbook を追加。
- BYOK と OpenRouter secret を ECC 管理プロジェクトファイル外に保ちつつ Zed をプロジェクトローカル planning/install ターゲットに追加。
- command-registry カバレッジ、platform audit、discussion audit、operator dashboard、Linear progress readiness、preview-pack smoke gate を追加。
- loop status、session trace、harness audit、ECC2 tool-risk log 向けローカル [observability readiness gate](../../architecture/observability-readiness.md) を追加。
- read-only basket research、comparison、oracle 型 market intelligence、
  risk review 向け公開ティーザー [Itô prediction-market skill pack](ito-prediction-market-skill-pack.md) を追加。ライブ Itô API アクセスは gated のまま ECC
  Tools billing から分離。
- ロールアウト由来の最適化 skill pack を追加：並列実行、
  benchmark loop、データスループット加速、レイテンシクリティカルシステム、再帰 decision ledger。
- 2026年5月 Mini Shai-Hulud/TanStack キャンペーンフォローアップ後のリリース準備 evidence を更新。full-campaign AgentShield
  IOC カバレッジ、queue-zero/discussion チェック、詳細 Linear roadmap gate、
  5月18日 operator dashboard スナップショット、announcement gating 向け live/pending リリース URL
  ledger を含む。
- `ecc-universal@2.0.0-rc.1` を npm の `next` dist-tag で公開。rc.1 期間中 `latest` タグは `1.10.0` のまま。

## v1.10.0 以降 (Since v1.10.0)

rc.1 面には孤立した feature branch ではなく主な 2.0 方向が含まれます：

- Claude Code、Codex、OpenCode、Cursor、Gemini、Zed、ターミナルのみ workflow 向け cross-harness substrate 作業；
- npm、Claude plugin、Codex repo-marketplace、OpenCode、agent metadata 向けより強い package と plugin publication 面；
- PR、issue、discussion、stale legacy 作業、Linear progress、リリース evidence、dashboard 反復可能性向け operator gate；
- Mini Shai-Hulud/TanStack キャンペーン後の supply-chain hardening（IOC スキャン、no-lifecycle CI install、advisory-source refresh、npm audit/signature チェック、ユーザーレベル AI-tool persistence ターゲット）；
- package-manager hardening、evidence-pack provenance、policy export、policy promotion、fleet routing、
  GitHub Action output telemetry 向け AgentShield enterprise-roadmap ミラー；
- hosted analysis、fleet-summary consumption、finding evidence path、harness policy-route linking、hosted promotion judge
  audit trace、billing announcement preflight、production Marketplace
  readback 状態向け ECC Tools roadmap ミラー；
- ドキュメント拡張、日本語ローカライズ、zh-CN から ja-JP parity
  修復、TypeScript 6 と Node type 更新による dependency readiness；
- GitHub リリースコピー、X、LinkedIn、記事アウトライン、Telegram/Hermes handoff、デモプロンプト、partner/sponsor/talk outreach、
  approval-gated ローンチチェックリスト向けローンチコラテラル。
- ライブ取引クレームや ECC Tools と Itô 所有権のマージではなく、公開ワークフローティーザーとしての gated Itô skill 配布。
- すでに解決するリンクと plugin tag/directory、video upload、ECC Tools
  billing readback を待たなければならないリンクを分離するリリース URL ledger。

## なぜ重要か (Why This Matters)

ECC はもう Claude Code plugin や設定バンドルだけではありません。

システムにはより明確な形状があります：

- 使い捨てプロンプトではなく再利用可能 skills
- ワークフロー規律向け hooks と tests
- docs、code、browser automation、research への MCP バックドアクセス
- Claude Code、Codex、OpenCode、Cursor、関連ツール向け cross-harness install 面
- chat、cron、handoff、日次作業ルーティング向けオプション operator shell としての Hermes

## Release Candidate 境界 (Release Candidate Boundaries)

これは最終 GA クレームではなく release candidate です。

この面で出荷するもの：

- 公開 Hermes セットアップドキュメント
- リリースノートとローンチコラテラル
- cross-harness アーキテクチャドキュメント
- サニタイズされた operator workflow 向け Hermes import ガイダンス
- queue 状態、discussion 状態、Linear roadmap カバレッジ、operator dashboard 状態、supply-chain フォローアップ向け publication-readiness evidence
- プライベート Hermes 状態なしで公開パックが組み立てられることを証明する preview-pack smoke evidence

ローカルに残すもの：

- secret、OAuth token、API key
- プライベートワークスペースエクスポート
- 個人データセット
- サニタイズされていない operator 固有 automation
- より深い CRM、finance、Google Workspace playbook

## アップグレード手順 (Upgrade Motion)

1. [rc.1 quickstart](quickstart.md) に従う。
2. [Hermes setup guide](../../HERMES-SETUP.md) を読む。
3. [cross-harness architecture](../../architecture/cross-harness.md) をレビュー。
4. [observability readiness gate](../../architecture/observability-readiness.md) を実行。
5. announcement リンクを使う前に [release URL ledger](release-url-ledger-2026-05-19.md) を確認。
6. 1つのワークフロー lane から始める：engineering、research、content、outreach。
7. サニタイズされた operator パターンのみ ECC skills に import。
8. リリース packaging と installer 振る舞いが確定するまで `ecc2/` を alpha control plane として扱う。

## 公開状態 (Publication State)

GitHub prerelease と npm `next` パッケージはライブ：

- GitHub prerelease:
  <https://github.com/affaan-m/ECC/releases/tag/v2.0.0-rc.1>
- npm rc package:
  <https://www.npmjs.com/package/ecc-universal/v/2.0.0-rc.1>

これは依然 release candidate であり GA クレームではありません。残りの公開クレームは、Claude plugin tag/marketplace path、
Codex repo-marketplace または公式 Plugin Directory 状態、video upload URL、
ECC Tools billing/native-payments readiness、最終 outbound コピーの readback が存在するまで approval-gated のままです。
