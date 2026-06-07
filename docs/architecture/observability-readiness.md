# ECC 2.0 Observability 準備 (ECC 2.0 Observability Readiness)

ECC 2.0 はより自律的になる前に observable であるべきです。ローカル
デフォルトは、コア signal が存在するかを telemetry をどこにも送らずにチェックする
opt-in、リポジトリ所有の readiness gate です。

実行：

```bash
npm run observability:ready
node scripts/observability-readiness.js --format json
```

ゲートは決定論的で CI で安全に実行できます。リポジトリ
ファイルのみをチェックし、リリース面が operator に必要な signal を
露出できるかを報告します。

## Signal モデル (Signal Model)

- Live status: `scripts/loop-status.js` は JSON を emit し、active loop を watch し、
  dashboard または handoff 向け snapshot を書き込めます。
- HUD/status contract: `docs/architecture/hud-status-session-control.md` と
  `examples/hud-status-contract.json` は context、
  tool calls、active agents、todos、checks、cost、risk、queue、session
  controls、tracker sync 向けのポータブル payload を定義します。
- Session traces: `scripts/session-inspect.js` は Claude、dmux、
  adapter-backed session を inspect し、正規 snapshot を書き込めます。
- Harness baseline: `scripts/harness-audit.js` は tool coverage、context efficiency、quality gate、memory persistence、
  eval coverage、security guardrail、cost efficiency 向けの反復可能 scorecard を提供します。
- Tool activity: `scripts/hooks/session-activity-tracker.js` は ECC2 が sync できる
  ローカル `tool-usage.jsonl` イベントを記録します。
- Risk ledger: `ecc2/src/observability/mod.rs` は tool call をスコアリングし、
  レビュー用の paginated ledger を保存します。
- Progress sync: `docs/architecture/progress-sync-contract.md` は merge batch と release-gate レビュー中に
  GitHub、Linear、ローカル handoff、リポジトリ roadmap、`scripts/work-items.js` が
  どう整合を保つかを定義します。
- Release safety: `docs/releases/2.0.0-rc.1/publication-readiness.md`、
  post-hardening evidence、supply-chain incident response、workflow-security
  validation、npm pack チェック、release-surface test は、公開 tag、package publish、plugin 提出、
  announcement アクションの前に存在する必要があります。

## 参照圧力 (Reference Pressure)

現在の agent-tooling エコシステムは同じ運用ニーズに収束しています：

- dmux、Orca、Superset は isolated worktree と、エージェント状態と merge/review 作業を
  一箇所で見ることを強調します。
- Claude HUD はコーディングループ内で context、tool activity、agent activity、todo 進捗を
  可視化します。
- Autocontext はすべての run を durable trace、report、artifact、
  再利用可能な改善として記録します。
- Meta-Harness は harness 自体を評価・改善対象として扱い、
  proposer 振る舞いと結果のクリーンなログを要求します。
- Zed と OpenCode は agent 制御面、レビュー可能な変更、
  ポータブルなプロジェクト知識を保つべき harness 固有設定を強調します。

ECC の答えはデフォルトで hosted analytics 依存ではありません。最初の
release-candidate gate はローカルで file-backed です。Hosted telemetry は
後から来られますが、ローカル event model が信頼できるほど有用になってからです。

## Operator ワークフロー (Operator Workflow)

1. `npm run observability:ready` を実行。
2. より広い harness scorecard 向けに `npm run harness:audit -- --format json` を実行。
3. より長い自律 batch 中に `node scripts/loop-status.js --json --write-dir .ecc/loop-status`
   を実行。
4. 新しい HUD または operator dashboard を配線する前に `examples/hud-status-contract.json` をレビュー。
5. 利用可能な session 面を確認するため `node scripts/session-inspect.js --list-adapters` を実行。
6. 追跡リポジトリのローカル work-item status に頼る前に
   `node scripts/work-items.js sync-github --repo <owner/repo>` を実行。
7. 自律性を上げる前に risky operation、conflict 分析、handoff
   レビューに ECC2 tool log を使う。
8. 公開リリースアクションの前に release-safety evidence チェックを再実行：
   publication readiness、supply-chain incident response、workflow-security
   validation、package surface、release-surface test。

終状態は実用的です：ECC により大きなマルチエージェント loop を走らせる前に、
operator は live status、durable session trace、baseline scorecard、ローカル risk ledger、
GitHub、Linear、handoff、roadmap evidence の乖離を防ぐ progress-sync contract を
持っていることを証明できます。
