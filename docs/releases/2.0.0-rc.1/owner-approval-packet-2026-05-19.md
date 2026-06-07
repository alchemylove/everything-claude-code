# ECC v2.0.0-rc.1 オーナー承認パケット (ECC v2.0.0-rc.1 Owner Approval Packet)

スナップショット日: 2026-05-19。

このパケットは rc.1 公開ローンチ向けの最終人間判断シートです。単体では何も公開しません。意図したリリースコミットから最終エビデンスコマンドを再実行した後、各リリースアクションを承認・延期・ブロックするために使用してください。

このパケットが拡張するクリーンエビデンスベースラインのソースコミット:
`9819626459a662773be7d0b1c18d82c1316b8c36`。

## 現行エビデンス (Current Evidence)

| エビデンス (Evidence) | 現在の記録状態 (Current recorded state) | 承認前に再実行 (Repeat before approval) |
| --- | --- | --- |
| Platform audit | ready true、open PR 0、open issue 0、discussion gaps 0、dirty files 0 | はい (yes) |
| Preview pack smoke | ready true、digest `531328aaaa53`、5/5 checks | はい (yes) |
| Release approval gate | ready false、digest `ef8f49f727b7`、4/6 checks 合格; owner decisions と live URL readbacks は保留中 | はい (yes) |
| GitHub prerelease | ライブ: <https://github.com/affaan-m/ECC/releases/tag/v2.0.0-rc.1>; prerelease true、draft false、公開 `2026-05-25T18:29:31Z` | はい (yes) |
| npm `next` publish | ライブ: <https://www.npmjs.com/package/ecc-universal/v/2.0.0-rc.1>; `next` は `2.0.0-rc.1` を指す、`latest` は `1.10.0` のまま | はい (yes) |
| Video suite | ready true、source assets 15/15、suite artifacts 13/13、publish candidates 12/12 | はい (yes) |
| Release surface tests | 5月26日 URL/package refresh 後に 28/28 合格 | はい (yes) |
| Full local suite | PR #2013 マージ前に 2568/2568 合格; PR #2011 マージ前に focused GateGuard regression も再び 91/91 合格 | はい (yes) |
| GitHub CI | PR #1998、#1999、#2000、#2001、#2002、#2004、#2008、post-PR #2006 `main`、PR #2009、post-PR #2009 `main`、post-PR #2011 `main`、post-PR #2013 `main` はすべて required checks 合格後にマージまたは前進 | current head を検証 |

## 判断レジスタ (Decision Register)

| 判断 (Decision) | 承認 / 延期 / ブロック (Approve / defer / block) | 先に必要なエビデンス (Evidence required first) | 備考 (Notes) |
| --- | --- | --- | --- |
| GitHub prerelease | approve | live prerelease readback | ライブ: <https://github.com/affaan-m/ECC/releases/tag/v2.0.0-rc.1>。残りの plugin/video/billing URL は approval-gated のまま。 |
| npm `next` publish | approve | `npm pack --dry-run`, `npm publish --tag next`, registry dist-tag readback | `ecc-universal@2.0.0-rc.1` は `next` に公開済み; rc.1 中は `latest` は `1.10.0` のまま。 |
| Claude plugin tag | defer | `claude plugin validate .claude-plugin/plugin.json`, `claude plugin tag .claude-plugin --dry-run` | 実 tag の作成と push は release 承認後のみ。 |
| Codex repo marketplace | defer | temp-home marketplace add smoke と現行公式 Plugin Directory ステータス | repo-marketplace 配布のみ主張; listing エビデンスなしに公式 Plugin Directory listing を主張しない。 |
| ECC Tools billing 文言 (ECC Tools billing language) | defer | target account と billing/product 状態の live readiness readback | gate が live になるまで native payments または Marketplace-managed Pro を告知しない。 |
| Video アップロード (Video upload) | defer | owner が primary launch cut と short clip を選択、self-eval が clean のまま | 承認済みカットのみアップロード; 編集可能な timeline/project 出力は保持。 |
| X、LinkedIn、GitHub Discussion、longform | defer | live release、npm、plugin、video、billing URL ledger 更新 | 個人アカウント投稿と outbound コピーには明示的承認が必要。 |
| スポンサー、パートナー、コンサル、カンファレンス、ポッドキャスト outreach | defer | 最終公開 URL と owner 承認済み outbound コピー | owner が正確なバッチを承認するまで draft を送信しない。 |

## 最終 URL 記入 (Final URL Fill-In)

承認済み公開アクション完了後、以下のサーフェスを更新してください:

| サーフェス (Surface) | 最終値ソース (Final value source) | 更新対象 (Update targets) |
| --- | --- | --- |
| GitHub prerelease URL | `gh release view v2.0.0-rc.1 --repo affaan-m/ECC --json url` | release notes、URL ledger、social copy |
| npm rc package URL | `npm view ecc-universal@2.0.0-rc.1 name version dist.tarball dist.integrity time --json` | URL ledger、quickstart、release notes |
| Claude plugin tag URL | push 済み `ecc--v2.0.0-rc.1` tag または marketplace readback | URL ledger、plugin docs、release notes |
| Codex repo-marketplace エビデンス (Codex repo-marketplace evidence) | temp-home `codex plugin marketplace add <local-checkout>` readback | URL ledger、publication readiness |
| Primary launch video URL | アップロード済み owner 承認 primary launch video | GitHub release、X、LinkedIn、longform |
| Short clip URL | アップロード済み承認 clip | X thread、LinkedIn、partner/sponsor/talk pack |
| ECC Tools billing/readiness URL | live readiness readback または明示的 blocked ステータス | sponsor copy、Pro copy、release notes |

## 最終エビデンスコマンド (Final Evidence Commands)

公開を承認する前に、正確なリリースコミットから以下を実行してください:

```bash
git status --short --branch
node scripts/platform-audit.js --json
npm run preview-pack:smoke -- --format json
npm run release:approval-gate -- --format json
npm run release:video-suite -- --format json
npm run harness:adapters -- --check
npm run harness:audit -- --format json
npm run observability:ready
npm run security:ioc-scan
npm audit --audit-level=moderate
npm audit signatures
node tests/docs/ecc2-release-surface.test.js
node tests/hooks/gateguard-fact-force.test.js
node tests/run-all.js
cd ecc2 && cargo test
```

## 承認文 (Approval Text)

短く明示的な承認を使用してください。例:

```text
コミット <sha> から最終エビデンスコマンドが通過した後、rc.1 GitHub prerelease、npm next publish、Claude plugin tag、およびリリース告知を承認する。
動画アップロードは <primary-video> と <shorts-list> を承認する。
スポンサー、パートナー、コンサル、カンファレンス、ポッドキャスト向けアウトバウンドは、正確なバッチを承認するまでブロックのままとする。
```

## 承認してはいけない条件 (Do Not Approve If)

- 最終ブランチが dirty である、または意図したリリースコミットと一致しない。
- 必須エビデンスコマンドが失敗した、または書面による延期なしにスキップされた。
- リリースコピーが readback 前に live billing、plugin marketplace 伝播、npm `next`、または公式 Codex Plugin Directory 掲載を主張している。
- 告知コピーに古い URL、private path、または未解決の live-link 判断が含まれる。
- 選択した動画カットに黒フレーム、音声欠落、古い URL、弱いプロダクト証拠、または未レビューのキャプションがある。
- アウトバウンドバッチが送信される形で正確にレビューされていない。

このパケット単体では、アウトバウンドメール、個人アカウント投稿、package publish、plugin tag、または billing 告知は許可されません。
