# 記事アウトライン - ECC v2.0.0-rc.1 (Article Outline - ECC v2.0.0-rc.1)

## 仮タイトル (Working Title)

ECC を Cross-Harness Operating System に変える (Turning ECC Into a Cross-Harness Operating System)

## 中核論点 (Core Argument)

ほとんどのエージェント作業はツールが孤立したままだと破綻する。

レバレッジは harness、再利用可能 workflow レイヤー、operator shell を1システムとして扱うことから来る：

- 繰り返し作業向け skills
- 強制向け hooks と tests
- tool アクセス向け MCP
- 継続性向け memory と handoff
- 日次実行をルーティングできる1つの operator shell

## 構成 (Structure)

### 1. 問題 (1. The Problem)

- チャットウィンドウが多すぎる
- ツール固有ワークフローが多すぎる
- 再利用可能なシステム形状ではなく個人習慣に context が住みすぎている

### 2. ECC がすでに解いたこと (2. What ECC Already Solved)

- 再利用可能 skill 形式
- cross-harness install 面
- hooks と verification 規律
- security と review パターン
- コンテンツ、research、ビジネス ops 周辺の operator workflow skills
- 運用状態を inspectable にする queue、discussion、Linear、legacy、release-evidence チェック
- Mini Shai-Hulud/TanStack キャンペーン後の supply-chain IOC スキャンと no-lifecycle install hardening

### 3. なぜ Hermes が Operator レイヤーか (3. Why Hermes Is the Operator Layer)

- chat、CLI、TUI、cron、handoff は再利用可能 ECC レイヤーの上に乗れる
- エンジニアリング作業の隣でビジネスとコンテンツ作業を走らせられる
- 日次ループが inspect と改善しやすくなる

### 4. rc.1 で出荷するもの (4. What Ships in rc.1)

- サニタイズされた Hermes セットアップガイド
- リリースと配布コラテラル
- cross-harness アーキテクチャ doc
- Hermes import ガイダンス
- リポジトリ内のより明確な 2.0 ポジショニング
- preview-pack smoke gate
- GitHub リリースコピー、X、LinkedIn、記事、Telegram/Hermes handoff、デモプロンプト向けローンチドラフト

### 5. v1.10.0 以降の変化 (5. What Changed Since v1.10.0)

- Claude Code はコアターゲットのままだが、ECC は Codex、OpenCode、
  Cursor、Gemini、Zed、ターミナルのみ workflow を共有実行面として扱う。
- リリースプロセスに反復可能な platform、discussion、observability、
  supply-chain、Linear progress、preview-pack チェックがある。
- AgentShield と ECC Tools 作業は roadmap にミラーされ、enterprise
  security、hosted review、policy promotion、billing-readiness lane が
  メインリリースから乖離しない。

### 6. ローカルに残すもの (6. What Stays Local)

- secret と auth
- 生ワークスペースエクスポート
- 個人データセット
- サニタイズされていない operator 固有 automation
- より深い CRM、finance、Google Workspace playbook

### 7. 締めのポイント (7. Closing Point)

目標は正確に同じスタックをコピーすることではない。

目標は、繰り返し作業を再利用可能で測定可能な面に変えるエージェント周辺の operating system を構築することだ。
