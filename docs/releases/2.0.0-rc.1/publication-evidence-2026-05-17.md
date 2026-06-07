# ECC v2.0.0-rc.1 公開証跡 - 2026-05-17 (ECC v2.0.0-rc.1 Publication Evidence - 2026-05-17)

これはリリース準備の証跡のみです。GitHub リリース、npm 公開、plugin tag、marketplace 提出、またはアナウンス投稿は作成しません。

## ソースコミット (Source Commit)

| 項目 (Field) | エビデンス (Evidence) |
| --- | --- |
| Upstream main | `e6c16b40b80b3b323586c9e8341faa87c01a728c` |
| Git remote | `https://github.com/affaan-m/everything-claude-code.git` |
| Evidence scope | 日本語・タイ語ローカライズマージバッチ、マージ後 ja-JP markdown anchor 修復、Zed install-target サポート、Mini Shai-Hulud/TanStack protection 再チェック、`gh-token-monitor` token-store IOC カバレッジ、AgentShield policy-promotion Action 出力ミラー、ECC-Tools hosted promotion judge audit-trace ミラー、ECC-Tools billing announcement preflight ミラー、ECC-Tools production Marketplace readback-state ミラー、legacy-tail dashboard routing、Linear progress readiness、deterministic preview-pack smoke gate 後の現在の `main` |
| Local status caveat | `git status --short --branch` は `## main...origin/main` と無関係な未追跡 `docs/drafts/` を表示; 生成された証跡ファイルは記述するソーススナップショット後にコミットされる |

実際のリリースオペレーターは、公開前に最終リリースコミットからすべての公開向けチェックを、厳密にクリーンな checkout で繰り返す必要があります。

## キューとディスカッション状態 (Queue And Discussion State)

| サーフェス (Surface) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| Trunk PRs | `gh pr list --state open --limit 50 --json number,title` | open PR 0 件 |
| Trunk issues | `gh issue list --state open --limit 50 --json number,title` | open issue 0 件 |
| Platform audit | `node scripts/platform-audit.js --json --allow-untracked docs/drafts/` | Ready; 追跡 repo は open PR 0 件、open issue 0 件、discussion maintainer-touch gap 0 件、accepted 回答欠落の回答可能 Q&A 0 件、blocking dirty file 0 件を報告 |
| Operator dashboard | `npm run operator:dashboard -- --markdown --allow-untracked docs/drafts/ --write docs/releases/2.0.0-rc.1/operator-readiness-dashboard-2026-05-17.md` | `e6c16b40b80b3b323586c9e8341faa87c01a728c` 向けに現在の dashboard を生成; dashboard ready true、release/npm/plugin/billing/announcement gates が approval-gated のため publication ready false |

platform audit で追跡されたリポジトリ:

- `affaan-m/everything-claude-code`
- `affaan-m/agentshield`
- `affaan-m/JARVIS`
- `ECC-Tools/ECC-Tools`
- `ECC-Tools/ECC-website`

## マージとトリアージバッチ (Merge And Triage Batch)

| 項目 (Item) | 結果 (Result) |
| --- | --- |
| Issue #1957 | README と hooks docs がサポート済み手動 hook インストールを既に文書化していることを確認後、メンテナーガイダンスでクローズ |
| Issue #1958 | supply-chain IOC scan と protection パス後、前のキューバッチでクローズ |
| PR #1962 | ESLint 10 が現在の Node 18 サポート契約より新しい Node engine range を要求するため、マージせずクローズ |
| PR #1961 | TypeScript 6.0.3 を `344a9bdf9c45c7589dedd3c66a8a2ebf2cbf2e5b` としてマージ; メンテナーパッチで `.opencode/tsconfig.json` に Node types を追加; 完全な GitHub Actions matrix がパス |
| PR #1963 | `@types/node` 25.8.0 を `b66ae3fbe070ef1fd2b610b4011f1345b4d75875` としてマージ; メンテナーパッチで npm lockfile を同期; 完全な GitHub Actions matrix がパス |
| PR #1953 | 日本語ローカライズを `9495b109e2c5fc5b1044ddfa1e2179f9d4aa86be` としてマージ; メンテナーパッチでローカライズ済み security/sponsorship リンクを修正、stale cubic-reported frontmatter 項目を翻訳、`docs/zh-CN` から `docs/ja-JP` パリティに欠落ファイル 0 件を確認、CodeRabbit、GitGuardian、cubic パス後に承認 |
| Post-merge trunk fix | `afe0ae8d725f7773147dc4aa7943a45846853a0d` を push し、`docs/ja-JP/skills/autonomous-loops/SKILL.md` から壊れた intra-file anchor を削除; PR #1953 後に `main` の root lint を復元 |
| Issue #1951 | PR #1953 マージ時に completed として自動クローズ |
| Zed adapter commit | `2371a3cf0543365c1c18e84eba786b1abcb28941` を push し、selective install target 経由の project-local Zed サポート、README Zed ガイダンス、`.zed/settings.json` planning カバレッジを追加 |
| Zed Windows CI fix | `744f4169972fd81618c3114ea1ca5ffb85ef4c82` を push し、Windows path separator 間で Zed install-plan source-path アサーションを正規化 |
| Discussion #1896 | `main` 上の Zed サポートを確認するメンテナー更新を追加、dry-run コマンドを文書化、BYOK/OpenRouter secrets は ECC 管理の project ファイルではなく Zed/local user settings に残ることを明確化 |
| PR #1967 | 2 件のメンテナークリーンアップコメント適用、markdownlint と language-switcher カバレッジ検証、現在の head で CodeRabbit、GitGuardian、Greptile、cubic パス後に承認し、タイ語ローカライズを `6b282aaa4389e9411e86bfe09d8f4de8018dcf8e` としてマージ |
| Supply-chain token-store scanner slice | `36d390aa7d733d458963a203b91998d3aec477b2` を push し、Mini Shai-Hulud `~/.config/gh-token-monitor/token` dead-man-switch token store を検出、incident-response runbook を更新、fixture カバレッジを追加; ローカル sweep はクリーンのまま、GitHub Actions `26003629550` がパス |
| Legacy-tail dashboard slice | `f397216aee5a0ca7d168726d3cc41eb47f728b37` と dashboard 再生成コミットを push し、ローカライズ tail 証跡を ITO-55 に紐付け、stale legacy work が release-current として扱われないようにした |
| Linear progress readiness slice | `355c4f128183aa7f7ce9da9485af07d257d67f69` と dashboard 再生成コミット `1a384dc5dbd24a3be725e1b26c169bddb6c850b6` を push し、大きなマージバッチ後に Linear progress 証跡の更新を要求 |
| Preview-pack smoke slice | `3215e655eff70b9fea5382ce5996666a1f48d1af` を push し、`npm run preview-pack:smoke` を追加。preview-pack artifacts、Hermes import boundaries、verification commands、approval-gated publication blockers をカバー; lint と dashboard フォローアップコミットは `27dc2918a24a50b8dd5e23dba2aa6a05bd17c0d7` まで着地 |
| AgentShield hardening-output slice | AgentShield `1124535345d7040242ecd3803f65bcd4dcaf6ec2` を push し、package-manager hardening status/count 出力と、registry credentials、lifecycle-script drift、release-age gate drift 向けの redacted GitHub Action job-summary 証跡を公開 |
| AgentShield policy-promotion Action slice | AgentShield `1593925dca025632dd8a6454509fce3fe7517cdf` を push し、policy-promotion status/count/digest 出力と、owner approval、protected rollout、runtime smoke 向けの GitHub Action job-summary review items を公開; 同じ Action job は promoted policy で scan したとき runtime smoke verified とマーク |
| ECC-Tools policy-promotion telemetry slice | ECC-Tools `86589517b11b95f1b0216ae7737563fb67ee1604` を push し、AgentShield policy-promotion Action 出力を hosted security review findings と Hosted Promotion Readiness スコアリングにルーティング |
| ECC-Tools policy-promotion operator UX slice | ECC-Tools `16c537fd385458c438ff32fb4211079b2f8ea1c4` を push し、hosted security job comments と check-runs に policy-promotion Action 出力の status、pack、review item count、remaining action count、digest を表示 |
| ECC-Tools hosted promotion judge audit trace slice | ECC-Tools `05d4e8296e37ba72e471beaa23ea4c81eb2aa31f` を push し、raw provider 出力を公開せずに hosted promotion judge request fingerprints と allowed-citation audit traces を表示 |
| ECC-Tools billing announcement preflight slice | ECC-Tools `91a441b92342b842832ac28b018ee46f0c4a906f` を push し、privileged API 呼び出し前の安全な Marketplace readback input と endpoint 検証向けに `npm run billing:announcement-gate -- --preflight` を追加 |
| ECC-Tools production Marketplace readback-state slice | ECC-Tools `eb6941290b2fa70db01a51084e9e79a160238468` を push し、production Cloudflare secret 名に `INTERNAL_API_SECRET` が含まれるが、production KV には現在 `account-billing:*` または `billing-state:*` レコードがないことを記録 |

## リリースゲートコマンド (Release Gate Commands)

| ゲート (Gate) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| Root lint | `npm run lint` | ja-JP autonomous-loop anchor 修復後に合格 |
| Root suite | `npm test` | 2487 合格、0 失敗 |
| GitHub Actions CI | `gh run view 25989533576 --json status,conclusion,jobs` | 37/37 job が green で正常完了（Security Scan とすべての Windows test job を含む） |
| Harness audit | `node scripts/harness-audit.js --format json` | 70/70、top actions なし |
| Observability readiness | `npm run observability:ready -- --format json` | 21/21、ready yes |
| Workflow security | `node scripts/ci/validate-workflow-security.js` | 8 workflow files を検証 |
| Supply-chain IOC scan | `node scripts/ci/scan-supply-chain-iocs.js --root ~/GitHub --home --json`; `node scripts/ci/scan-supply-chain-iocs.js --root ~/Documents/GitHub --home --json` | 合格; 各 workspace sweep は user-level persistence targets を含む 1,879 files を検査、findings 0 |
| npm audit | `npm audit --audit-level=high` | 脆弱性 0 |
| npm signatures | `agentshield`、`everything-claude-code`、`ECC-Tools`、`ECC-website`、`JARVIS/frontend` 向け `npm audit signatures` | 主要 ECC Node package roots 全体で合格 |
| Preview-pack smoke | `npm run preview-pack:smoke` | 合格; ready yes; digest `dfb1ed014607`; 5 checks 合格、0 失敗 |
| AgentShield enterprise CI output slice | AgentShield local `npm run build`、focused action tests、`npm run typecheck`、`npm run lint`、full `npm test`、`git diff --check`; GitHub Actions `25994354007`、`25994354011`、`25994354026` | ローカルゲート合格; リモート CI、Test GitHub Action、Self-Scan が `1124535` で正常完了 |
| AgentShield policy-promotion Action output slice | AgentShield local `npm run build`、`npx vitest run tests/action-promotion.test.ts tests/action.test.ts`、`npm run typecheck`、`npm run lint`、full `npm test`、`git diff --check`; GitHub Actions `25995929182`、`25995929190`、`25995929161` | ローカルゲート合格; リモート CI、Test GitHub Action、Self-Scan が `1593925` で正常完了 |
| ECC-Tools policy-promotion hosted telemetry slice | ECC-Tools local focused vitest（policy-promotion Action-output routing、hosted-promotion readiness）、`npm run typecheck`、`npm run lint`、full `npm test`、`git diff --check`; GitHub Actions `25996758218` | ローカルゲート合格; リモート CI が `8658951` で正常完了 |
| ECC-Tools policy-promotion operator UX slice | ECC-Tools local focused vitest（hosted findings/comments/checks 内 policy-promotion Action output values）、`npm run typecheck`、`npm run lint`、full `npm test`、`git diff --check`; GitHub Actions `25997300046` | ローカルゲート合格; リモート CI が `16c537f` で正常完了 |
| ECC-Tools hosted promotion judge audit trace slice | ECC-Tools local focused vitest（hosted model-judge audit traces）、`npm run typecheck`、`npm run lint`、full `npm test`、`git diff --check`; GitHub Actions `25997840703` | ローカルゲート合格; リモート CI が `05d4e82` で正常完了 |
| ECC-Tools billing announcement preflight slice | ECC-Tools local focused vitest preflight tests、`npm run typecheck`、`npm run lint`、full `npm test`、`git diff --check`; GitHub Actions `25998238507` | ローカルゲート合格; リモート CI が `91a441b` で正常完了 |
| ECC-Tools production Marketplace readback-state slice | ECC-Tools local `npm test`、`git diff --check`; Cloudflare `wrangler secret list` が名前で `INTERNAL_API_SECRET` 存在を確認; `wrangler kv key list`（`account-billing:`、`billing-state:`）は両方空; GitHub Actions `25998610438` | ローカルゲート合格; リモート CI が `eb69412` で正常完了; Marketplace purchase/webhook records が KV を populate するまで live announcement はブロック |
| GitHub queues | `gh pr list`; `gh issue list`; `node scripts/platform-audit.js --json --allow-untracked docs/drafts/` | open PR 0、open issue 0、discussion maintainer-touch gaps 0、accepted 回答欠落の回答可能 Q&A 0、GitHub fetch errors 0; 生成 evidence コミット後、追跡 repo セット全体で platform audit ready |
| Operator dashboard | `npm run operator:dashboard -- --markdown --allow-untracked docs/drafts/ --write docs/releases/2.0.0-rc.1/operator-readiness-dashboard-2026-05-17.md` | `e6c16b40b80b3b323586c9e8341faa87c01a728c` 向けに dashboard 生成; platform ready true、dashboard ready true、macro publication gates は未完了 |
| GitHub Actions CI | `gh run watch 26003629550 --repo affaan-m/everything-claude-code --exit-status` | `36d390aa7d733d458963a203b91998d3aec477b2` で正常完了（Validate Components、Lint、Security Scan、Coverage、full OS/Node/package-manager matrix を含む） |

## 現在の公開ブロッカー (Current Publication Blockers)

- このパスでは GitHub prerelease `v2.0.0-rc.1` はまだ作成されていない。
- npm `ecc-universal@2.0.0-rc.1` はまだ `next`
  dist-tag に公開されていない。
- Claude plugin tag と marketplace 伝播は approval-gated のまま。
- Codex repo-marketplace 配布は rc.1 で検証済みだが、公式
  Plugin Directory 公開は OpenAI の self-serve publishing
  surface によりブロックされている。
- ECC Tools billing/native-payments コピーは、Marketplace
  purchase/webhook パスが production `account-billing:*` と
  `billing-state:*` レコードを書き込み、その後 `npm run billing:announcement-gate --
  --account <github-login>` が announcement-ready gate を返すまでブロックされている。
- リリースノート、X、LinkedIn、GitHub release、longform コピーは、リリース/package/plugin URL が存在した後の最終ライブ URL がまだ必要。
- ローカル checkout には無関係な未追跡 `docs/drafts/` が残っているため、実際の公開前には厳密なクリーン checkout によるリリースパスが依然必要。

## 結果 (Result)

追跡された公開 PR キュー、issue キュー、discussion キューは 2026年5月17日時点でクリーンであり、現在の `main` は上記の Node、harness、observability、workflow-security、npm audit/signature、supply-chain IOC ゲートをパスした。これは公開準備を改善するが、`publication-readiness.md` にある approval-gated なリリース、package、plugin、billing、アナウンス手順の代替にはならない。
