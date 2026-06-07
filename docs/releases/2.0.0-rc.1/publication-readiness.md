# ECC v2.0.0-rc.1 公開準備 (Publication Readiness)

このチェックリストは公開サーフェス向けのリリースゲートです。単体ではエビデンスとして使用しないでください。エビデンス欄には、リリース対象の正確なコミットから取得した最新のコマンド出力または URL を記入してください。

現在の rc.1 命名決定と package/plugin 公開パスについては、
[`naming-and-publication-matrix.md`](naming-and-publication-matrix.md) を参照してください。
5月18日のリリース名、package、Claude plugin、Codex plugin、publication-order ゲートについては、
[`release-name-plugin-publication-checklist-2026-05-18.md`](release-name-plugin-publication-checklist-2026-05-18.md) を参照してください。
組み立て済み rc.1 preview pack の境界については、
[`preview-pack-manifest.md`](preview-pack-manifest.md) を参照してください。
5月12日の dry-run エビデンスパスについては、
[`publication-evidence-2026-05-12.md`](publication-evidence-2026-05-12.md) を参照してください。
5月13日のリリース準備エビデンス更新については、
[`publication-evidence-2026-05-13.md`](publication-evidence-2026-05-13.md) を参照してください。
PR #1850 および PR #1851 後の5月13日 post-hardening エビデンス更新については、
[`publication-evidence-2026-05-13-post-hardening.md`](publication-evidence-2026-05-13-post-hardening.md) を参照してください。
5月15日のキュー、discussion、Linear roadmap、Mini Shai-Hulud/TanStack follow-up、scheduled supply-chain watch、no-lifecycle CI install hardening、GitHub Actions cache purge、AgentShield release-verification、billing-gate、AgentShield #86 evidence-pack provenance、`ecc2` current-dir guard エビデンス更新（PR #1941 まで）については、
[`publication-evidence-2026-05-15.md`](publication-evidence-2026-05-15.md) を参照してください。
5月16日のキュークリーンアップ、recsys skill merge、GateGuard issue triage、AgentShield #87 plugin-cache runtime-confidence エビデンス、AgentShield #88 evidence-pack inspect/readback、AgentShield #89 evidence-pack fleet routing、AgentShield #90 fleet review items、AgentShield #91 checksum-backed policy export、AgentShield #92 checksum-verified policy promotion、ECC-Tools #76 fleet-summary consumption、ECC-Tools #77 hosted finding evidence paths、ECC-Tools #78 harness policy-route linking、operator dashboard refresh、現在の `main` における combined final-gate rerun については、
[`publication-evidence-2026-05-16.md`](publication-evidence-2026-05-16.md) を参照してください。
5月17日のキュークリーンアップ、Japanese localization merge、Dependabot TypeScript および Node type merges、post-merge ja-JP lint repair、Mini Shai-Hulud/TanStack local protection recheck、legacy-tail および Linear progress routing、deterministic preview-pack smoke gate、現在の operator dashboard refresh については、
[`publication-evidence-2026-05-17.md`](publication-evidence-2026-05-17.md) を参照してください。
5月18日の current-head キュー、workflow-security/metrics/uncloud merge batch、PR #1978 review/closure、Mini Shai-Hulud/TanStack local および home protection recheck、npm no-lifecycle install/audit/signature gates、AgentShield project scan、AgentShield `840952a` enterprise/IOC エビデンスミラー、release OIDC publishing-scope hardening、workflow normalization、`67e63e63` までの後続 dashboard/publication-readiness refresh、work-items sync、Linear progress comments、ITO-46 closure、operator dashboard refresh、5月19日 identity/video/growth-pack merge batch までの current-head CI/security scan success については、
[`publication-evidence-2026-05-19.md`](publication-evidence-2026-05-19.md) を参照してください。
同じ5月16日パスからのオペレーター向け prompt-to-artifact readiness dashboard については、
[`operator-readiness-dashboard-2026-05-15.md`](operator-readiness-dashboard-2026-05-15.md) を参照してください。
5月17日の operator dashboard refresh については、
[`operator-readiness-dashboard-2026-05-17.md`](operator-readiness-dashboard-2026-05-17.md) を参照してください。
5月18日の operator dashboard refresh については、
[`operator-readiness-dashboard-2026-05-18.md`](operator-readiness-dashboard-2026-05-18.md) を参照してください。

5月19日の hypergrowth/operator dashboard については、
[`operator-readiness-dashboard-2026-05-19.md`](operator-readiness-dashboard-2026-05-19.md) を参照してください。
現在の5月20日 Marketplace Pro release-gate operator dashboard は
[`operator-readiness-dashboard-2026-05-20.md`](operator-readiness-dashboard-2026-05-20.md) です。
リリース、npm、plugin、video、billing、social、outbound 承認にまたがる最終 owner decision sheet については、
[`owner-approval-packet-2026-05-19.md`](owner-approval-packet-2026-05-19.md) を参照してください。
GitHub prerelease および npm `next` readback 後の5月26日 live/pending release URL ledger については、
[`release-url-ledger-2026-05-19.md`](release-url-ledger-2026-05-19.md) を参照してください。

## リリースアイデンティティマトリクス (Release Identity Matrix)

| サーフェス (Surface) | 期待値 (Expected value) | 正とするソース (Source of truth) | 最新チェック (Latest check) | エビデンスアーティファクト (Evidence artifact) | オーナー (Owner) | ステータス (Status) |
| --- | --- | --- | --- | --- | --- | --- |
| プロダクト名 (Product name) | ECC | `README.md`, plugin manifests, release notes | `rg -n "^# ECC\|displayName.*ECC\|affaan-m/ECC" README.md .codex-plugin/plugin.json docs/releases/2.0.0-rc.1` | `release-name-plugin-publication-checklist-2026-05-18.md` および `release-url-ledger-2026-05-19.md` | Release owner | エビデンス記録済み (Evidence recorded) |
| GitHub repo | `affaan-m/ECC` | Git remote と release URL | `git remote get-url origin` | `release-url-ledger-2026-05-19.md` | Release owner | エビデンス記録済み (Evidence recorded) |
| Git tag | `v2.0.0-rc.1` | GitHub releases | `gh release view v2.0.0-rc.1 --repo affaan-m/ECC` | ライブ prerelease: <https://github.com/affaan-m/ECC/releases/tag/v2.0.0-rc.1>; prerelease true、draft false | Release owner | エビデンス記録済み (Evidence recorded) |
| npm package | `ecc-universal` | `package.json` | `node -p "require('./package.json').name"` | `publication-evidence-2026-05-12.md` | Package owner | エビデンス記録済み (Evidence recorded) |
| npm version | `2.0.0-rc.1` | `VERSION`, `package.json`, lockfiles | `node -p "require('./package.json').version"` | `publication-evidence-2026-05-12.md` | Package owner | エビデンス記録済み (Evidence recorded) |
| npm dist-tag | rc は `next`、GA のみ `latest` | npm registry | `npm view ecc-universal dist-tags --json` | レジストリは `latest: 1.10.0` と `next: 2.0.0-rc.1` | Package owner | エビデンス記録済み (Evidence recorded) |
| Claude plugin slug | `ecc` / `ecc@ecc` install path | `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json` | `node tests/hooks/hooks.test.js` | `publication-evidence-2026-05-12.md` | Plugin owner | エビデンス記録済み (Evidence recorded) |
| Claude plugin manifest | `2.0.0-rc.1`、未サポートの `agents` または明示的 `hooks` フィールドなし | `.claude-plugin/plugin.json`, `.claude-plugin/PLUGIN_SCHEMA_NOTES.md` | `claude plugin validate .claude-plugin/plugin.json` | `publication-evidence-2026-05-12.md` | Plugin owner | エビデンス記録済み (Evidence recorded) |
| Codex plugin manifest | 共有 skill source 付き `2.0.0-rc.1` | `.codex-plugin/plugin.json` | `node tests/docs/ecc2-release-surface.test.js` | `publication-evidence-2026-05-12.md` | Plugin owner | エビデンス記録済み (Evidence recorded) |
| Codex repo marketplace | `.agents/plugins/marketplace.json` 経由で公開される `ecc@2.0.0-rc.1` | `.agents/plugins/marketplace.json`, `.codex-plugin/README.md` | `HOME="$(mktemp -d)" codex plugin marketplace add <local-checkout>` | `publication-evidence-2026-05-15.md` | Plugin owner | repo-marketplace path 検証済み; OpenAI 提出エビデンス前に公式 Plugin Directory listing を主張しない |
| OpenCode package | `ecc-universal` plugin module | `.opencode/package.json`, `.opencode/index.ts` | `npm run build:opencode` | `publication-evidence-2026-05-12.md` | Package owner | エビデンス記録済み (Evidence recorded) |
| Agent metadata | `2.0.0-rc.1` | `agent.yaml`, `.agents/plugins/marketplace.json` | `node tests/scripts/catalog.test.js` | `publication-evidence-2026-05-12.md` | Release owner | エビデンス記録済み (Evidence recorded) |
| マイグレーションコピー (Migration copy) | rc.1 アップグレードパス、GA 主張ではない | `release-notes.md`, `quickstart.md`, `HERMES-SETUP.md` | `npx markdownlint-cli '**/*.md' --ignore node_modules` | `publication-evidence-2026-05-13.md` | Docs owner | エビデンス記録済み (Evidence recorded) |

## 公開ゲート (Publication Gates)

| ゲート (Gate) | 必須エビデンス (Required evidence) | 最新チェック (Latest check) | ブロッカー欄 (Blocker field) | オーナー (Owner) | ステータス (Status) |
| --- | --- | --- | --- | --- | --- |
| GitHub release | tag 存在、release notes が最終 URL を使用、必要なら asset 添付 | `gh release view v2.0.0-rc.1 --json tagName,url,isPrerelease` | `準備完了: v2.0.0-rc.1 prerelease は live; 残りの plugin、video、billing、outbound URL は依然として gated` | Release owner | エビデンス記録済み (Evidence recorded) |
| npm package | `npm pack --dry-run` が期待ファイルを含み、version 一致、rc は `next` へ | `npm pack --dry-run`; `npm view ecc-universal name version dist-tags --json`; `npm view ecc-universal@2.0.0-rc.1 name version dist.tarball dist.integrity time --json` | `準備完了: ecc-universal@2.0.0-rc.1 は next で live; latest は 1.10.0 のまま` | Package owner | エビデンス記録済み (Evidence recorded) |
| Claude plugin | manifest 検証、marketplace JSON が公開 repo を指す、install docs が slug と一致 | `claude plugin validate .claude-plugin/plugin.json`; `claude plugin tag .claude-plugin --dry-run`; isolated temp-home install smoke | `ブロッカー: 実 tag 作成/push には承認が必要` | Plugin owner | クリーン checkout dry-run と install smoke 記録済み |
| Codex plugin | manifest version が package/docs と一致、repo marketplace が plugin root を指し、OpenAI 公式 Plugin Directory ステータスを記録 | `node tests/docs/ecc2-release-surface.test.js`; `node tests/plugin-manifest.test.js`; `codex plugin marketplace add --help`; temp-home `codex plugin marketplace add <local-checkout>` | `ブロッカー: 公式 Plugin Directory listing には OpenAI 提出/listing エビデンスが必要` | Plugin owner | repo-marketplace 配布検証済み; 公式 directory は保留中 |
| OpenCode package | ビルド出力がソースから再生成され、package metadata が最新 | `npm run build:opencode` | `ブロッカー: ローカルビルドはなし; 公開配布は引き続き npm/plugin リリースに従う` | Package owner | エビデンス記録済み (Evidence recorded) |
| ECC Tools billing reference | billing 主張が検証済み Marketplace/App 状態にリンク | `env -u GITHUB_TOKEN gh repo view ECC-Tools/ECC-Tools --json nameWithOwner,isPrivate,viewerPermission` および operator bearer path 経由の内部 `/api/billing/readiness?selectReadyTarget=1` readback | `準備完了: ECC-Tools #92 main CI と ECC-Tools #93 main CI は合格; 2026-05-20 の live selected-target readback は announcementGate.ready === true を返却; payment 告知前に再実行` | ECC Tools owner | billing エビデンス準備完了; 最終コピーは release/plugin/live URL 承認待ち |
| 告知コピー (Announcement copy) | X、LinkedIn、GitHub release、longform コピーが live URL を指す | placeholder-marker scan と `release-url-ledger-2026-05-19.md` | `ブロッカー: GitHub と npm リンクは live; 残りの plugin、video、billing、outbound URL は承認/readback が必要` | Release owner | URL ledger 記録済み; 最終 URL は保留中 |
| 特権 workflow 強化 (Privileged workflow hardening) | release/maintenance workflow が checkout token を永続化しない | `node scripts/ci/validate-workflow-security.js` | `ブロッカー:` | Release owner | post-hardening refresh でエビデンス記録済み |

## 必須コマンドエビデンス (Required Command Evidence)

公開アクションの前に、正確なコミット SHA とコマンド出力を記録してください:

| エビデンス (Evidence) | コマンド (Command) | 必須結果 (Required result) | 記録済み出力 (Recorded output) |
| --- | --- | --- | --- |
| クリーンなリリースブランチ (Clean release branch) | `git status --short --branch` | 意図したリリースコミット上; 無関係ファイルなし | 5月20日時点のベースライン `c2471fe5c535310f8a8008c9ed7ea9f6757b33f2`: `## main...origin/main`; リリース前に正確な最終公開コミットから再実行 |
| Preview-pack smoke | `npm run preview-pack:smoke` | preview pack artifact、Hermes boundary、最終検証コマンド一覧、publication blocker が合格 | `publication-evidence-2026-05-19.md`: ready yes、digest `eebb8a66c33e`、33 artifacts、5 passed、0 failed; 最終の厳密なクリーン checkout リリースパスで再実行 |
| Release approval gate | `npm run release:approval-gate -- --format json` | owner 判断行が承認され、live release/package/plugin/video/billing URL が記録され、launch/outbound コピーに placeholder や private path がない場合のみ ready true | 5月26日時点の状態は意図的に blocked: plugin/video/billing/outbound の owner 判断と URL readback が approval-gated のまま |
| Harness audit | `npm run harness:audit -- --format json` | 80/80 合格 | 現行リリースゲート: 適用可能な 8 カテゴリで 80/80、top actions 0 |
| Adapter scorecard | `npm run harness:adapters -- --check` | PASS | 現行リリースゲート: PASS、11 adapters |
| Observability readiness | `npm run observability:ready` | 21/21 合格 | 現行リリースゲート: 21/21、ready true |
| Release safety gate | `npm run observability:ready -- --format json` | publication readiness、supply-chain、workflow security、package surface、release-surface エビデンスで Release Safety カテゴリ合格 | 現行リリースゲートは Release Safety を 3/3 で維持; 正確な最終リリースコミットから JSON ゲートを再実行 |
| サプライチェーン検証 (Supply-chain verification) | `npm audit --audit-level=moderate`; `npm audit signatures`; `yarn install --immutable --mode=skip-build`; `cd ecc2 && cargo audit -q`; Dependabot alerts; GitGuardian Security Checks | 脆弱性/alert 0、registry signature 検証済み、package-manager lock 受理、GitGuardian clean | 現行 supply-chain ブランチ: `npm audit` は脆弱性 0; `npm audit signatures` は 254 registry signatures と 30 attestations を検証; `@types/node@25.7.0` の pin と `brace-expansion` を `5.0.6` / `1.1.14` へ移した後、Yarn immutable install は lock を受理; PR #2008 CI `26108473648`、post-PR #2006 main CI `26109953093`、PR #2009 CI `26111313938`、post-PR #2009 main CI `26111946778` は失敗 0 で完了 |
| Root suite | `node tests/run-all.js` | 失敗 0 | 5月19日時点のローカルスイート: PR #2013 マージ前に 2568 passed、0 failed; post-PR #2009 の focused regression も worktree detection、observe subdirectory/global fallback、project maintenance CLI、hooks suite で合格 |
| Markdown lint | `npx markdownlint-cli '**/*.md' --ignore node_modules` | 失敗 0 | 現行リリースゲート: `publication-readiness.md`、`publication-evidence-2026-05-19.md`、`docs/ECC-2.0-GA-ROADMAP.md` の focused lint が合格 |
| Package surface | `node tests/scripts/npm-publish-surface.test.js` | 失敗 0; npm tarball に Python bytecode なし | 現行リリースゲート: 2/2 合格 |
| Release surface | `node tests/docs/ecc2-release-surface.test.js` | 失敗 0 | 現行リリースゲート: discussion-count アサーションを post-PR #2005 ベースラインへ更新後、27/27 合格 |
| 任意 Rust surface (Optional Rust surface) | `cd ecc2 && cargo test` | 失敗 0 または明示的延期 | `publication-evidence-2026-05-16.md`: 462/462 合格、既存 warnings のみ |
| キューベースライン (Queue baseline) | trunk、AgentShield、JARVIS、ECC Tools、ECC website 向け `node scripts/platform-audit.js --json` | open PR 20 未満、open issue 20 未満 | PR #2020 後の 5月20日ベースライン: platform audit ready true、open PR 0、open issue 0、discussion gaps 0、conflicting PRs 0、追跡 repo 全体で blocking dirty files 0 |
| Discussion ベースライン (Discussion baseline) | `node scripts/platform-audit.js --json` と `node scripts/discussion-audit.js --json` | 未管理の active discussion キューなし、accepted 回答欠落の回答可能 Q&A なし | post-PR #2005 ベースライン: platform audit は trunk discussions 59 件をサンプル、maintainer touch 必要 0、accepted 回答欠落の回答可能 discussion 0; `docs/architecture/discussion-response-playbook.md` は response templates と security escalation rules を記録 |
| Linear roadmap | Linear project と issue readback | release、security、AgentShield、ECC Tools、legacy、observability レーンを含む詳細 roadmap が存在 | 5月18日の Linear comments に ITO-57 `3fe5b2b7-c4fe-401c-a317-b40d72119cb3` と ITO-44 `fb4a4f33-6c2d-421a-bbdb-63cfad3e3ee4` を含む; 以前のエビデンスは project と 16 issue lanes を記録 |
| Operator readiness dashboard | `npm run operator:dashboard -- --json` | 現在のキュー状態が macro-goal 成果物と未完了ギャップにマップされる | 5月20日ダッシュボードは post-PR #2020 ベースラインから refresh; platform audit ready true、open PR 0、open issue 0、discussion gaps 0、dirty files 0、release video suite current、selected-target billing/env-file path を mirror、publication gates は依然 approval-gated |
| Release URL ledger | `docs/releases/2.0.0-rc.1/release-url-ledger-2026-05-19.md` および placeholder-marker scan | 告知コピー投稿前に live link と approval-gated link を分離 | ledger は public repo/docs/GitHub prerelease/npm/OpenAI Codex documentation URL を記録し、approval-gated チェックが通るまで plugin/video/billing/social URL をブロック |
| リリース名とプラグイン公開チェックリスト (Release name and plugin publication checklist) | `docs/releases/2.0.0-rc.1/release-name-plugin-publication-checklist-2026-05-18.md` | name/package/plugin 値が凍結、最終リリースコマンドが列挙、Claude/Codex 公開パスが現行公式 docs を引用 | チェックリストは rc.1 で `ECC`、`ecc-universal`、plugin slug `ecc` を維持; 最終エビデンス前に npm rename、npm publish、plugin tag、official listing、billing claim、announcement は行わない |

## 公開してはいけない条件 (Do Not Publish If)

- エビデンス記録後に `main` に未レビューの release-surface 変更がある。
- `npm view ecc-universal dist-tags --json` が意図した rc/GA tag と矛盾する。
- Claude plugin validation が利用できない、または意図したリリースコミット向けの clean-checkout install smoke test が記録されていない。
- リリースノートまたは告知ドラフトに placeholder URL、`TODO`、`TBD`、private workspace paths、personal operator references が残っている。
- Billing、Marketplace、plugin-submission コピーが live surface の存在を、live URL 作成前に主張している。
- 同じブランチ上で stale PR salvage 作業が進行中である。

## 告知順序 (Announcement Order)

1. リリースバージョン PR をマージする。
2. リリースコミットから必須コマンドエビデンスを記録する。
3. GitHub prerelease readback を検証する。
4. npm が rc.1 を `latest` ではなく `next` を指していることを確認する。
5. plugin marketplace サーフェスを提出または更新する。
6. release URL ledger を再生成し、最終 live URL でリリースノートを更新する。
7. GitHub release コピーを公開する。
8. 公開 URL が動作することを確認した後にのみ、X、LinkedIn、長文コピーを公開する。
