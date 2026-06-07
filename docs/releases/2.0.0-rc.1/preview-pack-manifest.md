# ECC v2.0.0-rc.1 プレビューパックマニフェスト (ECC v2.0.0-rc.1 Preview Pack Manifest)

このマニフェストは `2.0.0-rc.1` のレビュー済みプレビューパックを定義する。それ自体はリリースアクションではない。GitHub prerelease と npm `next` 公開の後、プラグイン surface の tag、video アップロード、告知投稿の前に、公開ローンチ surface が組み立てられたままであることを検証するために使用する。

## パック内容 (Pack Contents)

| アーティファクト (Artifact) | 役割 (Role) | ゲート (Gate) |
| --- | --- | --- |
| `README.md` | 公開オンボードとインストール surface | Hermes setup、rc.1 ノート、プラグインインストール、手動インストール、リセット、アンインストールガイドへのリンク |
| `docs/HERMES-SETUP.md` | 公開 Hermes オペレータートポロジー | raw workspace export、credentials、private account 名、local-only オペレーター状態を含まない |
| `skills/hermes-imports/SKILL.md` | サニタイズ済み Hermes-to-ECC インポートワークフロー | インポートルール、サニタイズチェックリスト、変換パターン、出力コントラクトを含む |
| `docs/architecture/cross-harness.md` | Claude Code、Codex、OpenCode、Cursor、Gemini、Hermes、terminal-only 利用向けの共有 substrate モデル | ポータビリティ境界を命名し、サポートされていない native parity を主張しない |
| `docs/architecture/harness-adapter-compliance.md` | Adapter マトリクスとスコアカード | `npm run harness:adapters -- --check` で検証 |
| `docs/architecture/platform-value-loop.md` | プロダクト統合とフルスタックプラットフォーム thesis | 外部プロダクト skill pack、gated API、case study、sponsorship、Pro、consulting loop を、サポートされていない GA/control-pane 主張から分離して維持 |
| `docs/architecture/observability-readiness.md` | ローカルオペレーター readiness ゲート | `npm run observability:ready` で検証 |
| `docs/architecture/progress-sync-contract.md` | GitHub、Linear、handoff、roadmap、work-item sync 境界 | `node scripts/platform-audit.js --json` でチェック |
| `scripts/preview-pack-smoke.js` | 決定論的 preview-pack smoke ゲート | `npm run preview-pack:smoke` で検証 |
| `scripts/release-approval-gate.js` | 最終オーナー決定、live-URL、ローンチコピーゲート | 追加のリリース/パッケージアクション、プラグイン tag、video アップロード、告知、outbound batch の前に ready true を返すこと |
| `docs/releases/2.0.0-rc.1/release-notes.md` | GitHub リリースコピーソース | 公開前に live GitHub/npm URL と残りのプラグイン/video/billing ゲートと整合していること |
| `docs/releases/2.0.0-rc.1/quickstart.md` | clone から最初のワークフローまでのパス | clone、install、verify、最初の skill、harness 切り替えをカバー |
| `docs/releases/2.0.0-rc.1/launch-checklist.md` | オペレーターローンチチェックリスト | プラグイン、video、billing、告知アクションについて approval-gated のまま維持 |
| `docs/releases/2.0.0-rc.1/publication-readiness.md` | リリースゲート | 正確なリリースコミットからの新鮮な証拠を要求 |
| `docs/releases/2.0.0-rc.1/publication-evidence-2026-05-15.md` | 現在の May 15 キュー、roadmap、security、supply-chain watch、no-lifecycle CI install hardening、AgentShield #86 evidence-pack provenance、ECC Tools billing-gate、Actions cache purge、`ecc2` test 証拠（PR #1941 まで） | 実際の公開前に最終 clean-checkout 証拠ファイルで置き換えること |
| `docs/releases/2.0.0-rc.1/publication-evidence-2026-05-16.md` | 現在の May 16/17 キュークリーンアップ、recsys skill merge、GateGuard triage、PR #1947 supply-chain protection、AgentShield #87 plugin-cache confidence evidence、AgentShield #88 evidence-pack inspect/readback、AgentShield #89 evidence-pack fleet routing、AgentShield #90 fleet review items、AgentShield #91 policy export、AgentShield #92 policy promotion、ECC-Tools #76 fleet-summary consumption、ECC-Tools #77 hosted finding evidence paths、ECC-Tools #78 harness policy-route linking、dashboard refresh、May 16 mirror までの combined Node/Rust/release-surface gate 証拠 | 実際の公開前に strict clean checkout から再実行が必要 |
| `docs/releases/2.0.0-rc.1/publication-evidence-2026-05-17.md` | May 17 queue-zero 状態、日本語ローカライゼーション merge、Dependabot TypeScript と Node type merge、merge 後の ja-JP lint 修復、Mini Shai-Hulud/TanStack protection recheck、npm audit/signature チェック、legacy と Linear progress routing、決定論的 preview-pack smoke、オペレーターダッシュボード refresh、Linear sync、`27dc2918` の GitHub CI 証拠 | May 18 証拠スナップショットで置き換え済み; 実際の公開前に strict clean checkout から再実行 |
| `docs/releases/2.0.0-rc.1/publication-evidence-2026-05-18.md` | May 18 queue-zero 状態、#1970/#1971/#1972 merge batch、#1978 review/closure、supply-chain recheck、AgentShield evidence mirror、Linear sync、`4470e2e6` の current-head CI/security scan 成功、ITO-46 naming/plugin publication closure | May 19 ECC identity、video、growth 証拠スナップショットで置き換え済み |
| `docs/releases/2.0.0-rc.1/publication-evidence-2026-05-19.md` | 現在の May 19/20 証拠（正規 ECC identity、リリース video suite、partner/sponsor/talk outreach pack、オーナー承認パケット、release approval gate、May 20 オペレーターダッシュボード、preview-pack smoke digest `eebb8a66c33e`、2568-test ローカルスイート、PR #1998 visual QA CI 成功、PR #1999 dashboard evidence CI 成功、PR #2000 suite-count evidence 成功、PR #2001 owner approval packet CI 成功、PR #2002 owner-approval dashboard gate CI 成功、PR #2004 Linear readiness evidence sync CI 成功、PR #2008 supply-chain evidence gate CI 成功、post-PR #2006 main CI 成功、PR #2009 project-registry hygiene CI 成功、post-PR #2009 main CI 成功、post-PR #2011 GateGuard CI 成功、post-PR #2013 release-approval-gate CI 成功、PR #2017/#2018 AgentShield evidence sync、ECC-Tools #79 billing-announcement redaction hardening、ECC-Tools #80-#93 runtime-receipt、AgentShield approval-ID、Linear sync、remediation sync、hosted observability event/status/depth-plan/API readback、Marketplace Pro selected-target readback、selected-target announcement gate、env-file billing オペレーターパス、non-breaking operator bearer path、live `announcementGateReady: true`、AgentShield #94 Zed/VS Code adapter coverage、AgentShield #95 Dependabot alert closure、JARVIS #15/#16 queue/deploy repair、ECC #2019/#2020 Marketplace Pro gate sync、May 19/20 Linear sync コメント） | 現在の最強 readiness スナップショット; 実際の公開前に strict clean checkout から再実行が必要 |
| `docs/releases/2.0.0-rc.1/operator-readiness-dashboard-2026-05-17.md` | 以前の prompt-to-artifact オペレーターダッシュボード | May 18 生成ダッシュボードで置き換え済み |
| `docs/releases/2.0.0-rc.1/operator-readiness-dashboard-2026-05-18.md` | 以前の prompt-to-artifact オペレーターダッシュボード | May 19 生成ダッシュボードで置き換え済み |
| `docs/releases/2.0.0-rc.1/operator-readiness-dashboard-2026-05-19.md` | 以前の prompt-to-artifact オペレーターダッシュボード | May 20 生成ダッシュボードで置き換え済み |
| `docs/releases/2.0.0-rc.1/operator-readiness-dashboard-2026-05-20.md` | 現在の prompt-to-artifact オペレーターダッシュボード | PR/issue/discussion/platform/supply-chain ゲートが current であることを示し、現在の `$1,728/mo` から `$10,000/mo` の hypergrowth、video オーナー承認、Linear release-gate sync、selected-target billing gate、operator bearer path、live billing gate pass、outbound-pack 運用レーンを追加 |
| `docs/releases/2.0.0-rc.1/owner-approval-packet-2026-05-19.md` | リリース、パッケージ、プラグイン、video、billing、ソーシャル、outbound 承認の最終人間判断シート | 公開または outbound アクションの前にオーナーがレビューすること |
| `docs/releases/2.0.0-rc.1/release-url-ledger-2026-05-19.md` | リリースコピー向け live URL と approval-gated URL ledger | 公開告知前に最終リリースコミットから再生成すること |
| `docs/releases/2.0.0-rc.1/video-suite-production.md` | リリース video 制作マニフェスト | ローカルメディアインベントリ、rough primary render、captions、timeline、self-eval、no-private-path 公開ルールをゲート |
| `docs/releases/2.0.0-rc.1/partner-sponsor-talks-pack.md` | パートナー、sponsor、consulting、conference、podcast、discussion コピー | approval-gated のまま維持し、証拠なしに live billing、リリース、パッケージ、プラグイン主張を避ける |
| `docs/releases/2.0.0-rc.1/naming-and-publication-matrix.md` | 命名、slug、公開パス決定記録 | rc.1 では `ECC`、npm `ecc-universal`、プラグイン slug `ecc` を維持 |
| `docs/releases/2.0.0-rc.1/release-name-plugin-publication-checklist-2026-05-18.md` | リリース名、パッケージ、Claude plugin、Codex plugin、公開順序チェックリスト | rc.1 identity を凍結し、プラグイン、billing、告知アクション前に最終コミット証拠を要求 |
| `docs/releases/2.0.0-rc.1/x-thread.md` | X ローンチドラフト | live GitHub/npm URL を使用し、残りのプラグイン/video/billing URL を gated のまま維持 |
| `docs/releases/2.0.0-rc.1/linkedin-post.md` | LinkedIn ローンチドラフト | live GitHub/npm URL を使用し、残りのプラグイン/video/billing URL を gated のまま維持 |
| `docs/releases/2.0.0-rc.1/article-outline.md` | Longform ローンチアウトライン | GA 証拠が存在するまで release-candidate フレーミングを維持 |
| `docs/releases/2.0.0-rc.1/telegram-handoff.md` | 内部/共有可能 handoff コピー | private workspace または credential 詳細を含まない |
| `docs/releases/2.0.0-rc.1/demo-prompts.md` | デモプロンプトと proof-of-work プロンプト | private Hermes ワークフローを公開例に抽象化したまま維持 |
| `docs/releases/2.0.0-rc.1/ito-prediction-market-skill-pack.md` | 公開 Itô skill-pack 配布ノート | Itô API アクセスを gated、non-advisory のまま維持し、ECC Tools billing から分離 |

## Itô Skill Pack 境界 (Itô Skill Pack Boundary)

一般的なプロダクト統合コントラクトは `docs/architecture/platform-value-loop.md` に記録されている。Itô pack は最初の実例: 有用な公開ワークフロー、別個の gated API アクセス、ECC にフィードバックするサニタイズ済みオペレーターパターンで、ビジネス所有権をマージしない。

プレビューパックには prediction-market と Itô 隣接ワークフロー向けの 6 つの公開 teaser skill が含まれる:

- `skills/ito-market-intelligence/SKILL.md`
- `skills/ito-basket-compare/SKILL.md`
- `skills/ito-trade-planner/SKILL.md`
- `skills/ito-data-atlas-agent/SKILL.md`
- `skills/prediction-market-oracle-research/SKILL.md`
- `skills/prediction-market-risk-review/SKILL.md`

これらは research、comparison、planning、risk-review skill である。取引は行わず、投資助言は提供せず、ECC Tools と Itô をマージしない。Itô バックのデータ呼び出しには `ITO_API_KEY` を通じた明示的な gated API アクセスが必要。

## Hermes Skill 境界 (Hermes Skill Boundary)

プレビューパックには 1 つの公開 Hermes 特化 skill が含まれる:

- `skills/hermes-imports/SKILL.md`

これは rc.1 では意図的である。この skill はサニタイズと変換ワークフローであり、private Hermes automation のダンプではない。追加の Hermes 生成 skill は、同じルールを通過した後にのみ ECC に入ること:

- raw workspace export なし;
- live account 名、client データ、finance データ、CRM データ、health データ、private contact graph なし;
- secret 値ではなく capability で記述された provider 要件;
- local absolute path の代わりに repo-relative 例;
- private state なしでワークフローが有用であることを証明する test または docs。

## 参照に触発された Adapter 方向 (Reference-Inspired Adapter Direction)

プレビューパックは外部システムをコピー対象ではなく設計圧力として使用する:

| 参照圧力 (Reference pressure) | ECC preview-pack の解釈 (ECC preview-pack interpretation) |
| --- | --- |
| Claude Code | Native plugin、skills、commands、hooks、MCP 規約、statusline 指向ワークフロー |
| Codex | Instruction-backed plugin metadata、共有 skills、MCP reference config、明示的な hook-parity 注意事項 |
| OpenCode | 共有 hook logic を edge に持つ adapter-backed package/plugin surface |
| Zed-adjacent tools | 検証済み native adapter が存在するまでの instruction-backed ポータビリティ |
| dmux | Session/runtime orchestration シグナルと handoff export。repo validation の代替ではない |
| Orca, Superset, Ghast | Worktree lifecycle、session grouping、notification、workspace preset 向けの reference-only 圧力 |
| Hermes Agent, meta-harness, autocontext-style systems | 公開 artifact、verifier 出力、evaluator/RAG prototype を通じた evaluation、memory、context-routing 圧力 |

## 最終検証コマンド (Final Verification Commands)

公開前に正確なリリースコミットからこれらを実行すること:

```bash
git status --short --branch
node scripts/platform-audit.js --json
npm run preview-pack:smoke
npm run release:approval-gate -- --format json
npm run release:video-suite -- --format json
npm run harness:adapters -- --check
npm run harness:audit -- --format json
npm run observability:ready
npm run security:ioc-scan
npm audit --audit-level=moderate
npm audit signatures
node tests/docs/ecc2-release-surface.test.js
node tests/run-all.js
cd ecc2 && cargo test
```

## 公開ブロッカー (Publication Blockers)

プレビューパックは組み立て済みであり、最初のリリース/パッケージ surface は現在 live である。完全な公開は、これらの live surface と決定が最終証拠ファイルに記録されるまでブロックされたままである:

- 意図したリリースコミットから再生成された最終リリース URL ledger;
- オーナー承認と live URL readback が記録された後に `npm run release:approval-gate -- --format json` が ready true を返すこと;
- 意図したリリースコミットから再実行された最終リリース名/プラグイン公開チェックリスト;
- GitHub prerelease `v2.0.0-rc.1` live readback;
- `next` dist-tag 上の npm `ecc-universal@2.0.0-rc.1` live readback;
- `ecc@ecc` 向け Claude プラグイン tag / marketplace 伝播;
- Codex repo-marketplace 配布証拠と公式 Plugin Directory 可用性ステータス;
- X、LinkedIn、GitHub release、longform コピー内の最終告知 URL;
- ECC Tools billing/プロダクト readiness 証拠が新鮮なまま: May 20 selected-target KV readback と live announcement gate が operator bearer path を通過。native-payments 告知コピーを公開する前に billing readback と gate を直前に再実行すること。

## 結果 (Result)

rc.1 プレビューパックは最終 clean-checkout リリースゲートの準備ができているが、上記の残りの approval-gated リリース、パッケージ、プラグイン、告知ステップなしでは完全な公開には至らない。GitHub と npm は記録済み; プラグイン、video、billing、outbound 承認は未完了のまま。
