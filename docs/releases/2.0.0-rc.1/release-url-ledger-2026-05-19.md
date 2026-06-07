# ECC v2.0.0-rc.1 リリース URL Ledger (ECC v2.0.0-rc.1 Release URL Ledger)

この ledger は、すでに公開されているリンクと、残りの approval-gated な plugin、video、billing、announcement ステップの後にのみ有効になるリンクを分離する。公開 announcement を投稿する前に、最終リリースコミットから再生成すること。

2026-05-26 に GitHub prerelease と npm `next` package readback が成功した後に refresh。残りの plugin、video、billing、outbound surface は公開前に正確な release commit から再確認すること。

## 現在ライブ (Live Now)

| 面 (Surface) | URL | 検証 (Verification) |
| --- | --- | --- |
| Repository | <https://github.com/affaan-m/ECC> | `git remote get-url origin` が `https://github.com/affaan-m/ECC.git` を返す |
| GitHub prerelease URL | <https://github.com/affaan-m/ECC/releases/tag/v2.0.0-rc.1> | `gh release view v2.0.0-rc.1 --repo affaan-m/ECC --json tagName,url,isPrerelease,isDraft,publishedAt` が prerelease `true`、draft `false`、published `2026-05-25T18:29:31Z` を返した |
| Release pack folder | <https://github.com/affaan-m/ECC/tree/main/docs/releases/2.0.0-rc.1> | In-tree release pack |
| Release notes draft | <https://github.com/affaan-m/ECC/blob/main/docs/releases/2.0.0-rc.1/release-notes.md> | In-tree release copy |
| Hermes setup guide | <https://github.com/affaan-m/ECC/blob/main/docs/HERMES-SETUP.md> | In-tree sanitized Hermes guide |
| May 19 evidence snapshot | <https://github.com/affaan-m/ECC/blob/main/docs/releases/2.0.0-rc.1/publication-evidence-2026-05-19.md> | 現行の最強 identity、video、growth、CI readiness evidence |
| May 18 evidence snapshot | <https://github.com/affaan-m/ECC/blob/main/docs/releases/2.0.0-rc.1/publication-evidence-2026-05-18.md> | 以前の supply-chain と publication-path readiness evidence |
| May 18 operator dashboard | <https://github.com/affaan-m/ECC/blob/main/docs/releases/2.0.0-rc.1/operator-readiness-dashboard-2026-05-18.md> | 以前の prompt-to-artifact dashboard |
| May 19 operator dashboard | <https://github.com/affaan-m/ECC/blob/main/docs/releases/2.0.0-rc.1/operator-readiness-dashboard-2026-05-19.md> | hypergrowth、video、outbound lane 付きの以前の prompt-to-artifact dashboard |
| May 20 operator dashboard | <https://github.com/affaan-m/ECC/blob/main/docs/releases/2.0.0-rc.1/operator-readiness-dashboard-2026-05-20.md> | Marketplace Pro release-gate sync 付きの現行 prompt-to-artifact dashboard |
| npm package page | <https://www.npmjs.com/package/ecc-universal> | `npm view ecc-universal name version dist-tags versions --json` が `latest: 1.10.0`、`next: 2.0.0-rc.1`、`versions` に `2.0.0-rc.1` を含むことを返した |
| npm rc package URL | <https://www.npmjs.com/package/ecc-universal/v/2.0.0-rc.1> | `npm view ecc-universal@2.0.0-rc.1 name version dist.tarball dist.integrity time --json` が version `2.0.0-rc.1`、tarball `https://registry.npmjs.org/ecc-universal/-/ecc-universal-2.0.0-rc.1.tgz`、published time `2026-05-26T00:36:22.940Z` を返した |
| Codex marketplace CLI docs | <https://developers.openai.com/codex/cli/reference#codex-plugin-marketplace> | 公式 docs が GitHub shorthand、Git URL、SSH URL、local marketplace root 向け `codex plugin marketplace add` を列挙 |
| Codex official Plugin Directory status | <https://developers.openai.com/codex/plugins/build#publish-official-public-plugins> | 公式 docs は public Plugin Directory publishing と self-serve management が coming soon と記載 |

## 承認ゲート URL (Approval-Gated URLs)

| 面 (Surface) | 意図する URL またはコマンド (Intended URL or command) | 使用前のゲート (Gate before use) |
| --- | --- | --- |
| Claude plugin tag | `claude plugin tag .claude-plugin --dry-run`、承認後のみ実 tag | クリーンな release commit と plugin tag/push 承認 |
| Codex repo marketplace install | `codex plugin marketplace add affaan-m/ECC --ref v2.0.0-rc.1` | GitHub tag が存在すること; 公式 Plugin Directory submission は別途 |
| ECC Tools native-payments announcement | ECC Tools Marketplace/App URL と operator bearer path 経由の selected-target billing readiness readback | Marketplace-managed selected target が 2026-05-20 に `announcementGate.ready === true` を返した; 公開直前に繰り返す |
| Public announcements | X、LinkedIn、GitHub release、longform URL | 残り plugin、video、billing URL が解決するか明示的に blocked とマークされること; 正確な outbound copy には owner 承認が必要 |

## 投稿前チェック (Pre-Post Check)

公開直前に次を実行する:

```bash
git status --short --branch
gh release view v2.0.0-rc.1 --repo affaan-m/ECC --json tagName,url,isPrerelease
npm view ecc-universal name version dist-tags --json
npm view ecc-universal@2.0.0-rc.1 name version dist.tarball dist.integrity time --json
codex plugin marketplace add --help
rg -n "TODO|TBD|PLACEHOLDER" docs/releases/2.0.0-rc.1
npm run preview-pack:smoke
npm run release:approval-gate -- --format json
```

plugin propagation、公式 Codex Plugin Directory listing、video upload、ECC Tools billing/native payments、最終 outbound readiness を主張する前に、上記 approval-gated URL がクリーンな release commit から解決すること。
