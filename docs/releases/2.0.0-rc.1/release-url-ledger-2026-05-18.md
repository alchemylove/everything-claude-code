# ECC v2.0.0-rc.1 リリース URL Ledger (ECC v2.0.0-rc.1 Release URL Ledger)

この ledger は、すでに公開されているリンクと、approval-gated なリリース、パッケージ、plugin、announcement ステップの後にのみ有効になるリンクを分離します。公開 announcement を投稿する前に、最終リリースコミットから再生成してください。

ソーススナップショット `81fca2cea6f1399c52c8faa70f9a17e42f0bd447` を 2026-05-18 にキャプチャ。この ledger ファイルは、記述する evidence snapshot の後に docs-only refresh でコミットされる場合があります。

## 現在ライブ (Live Now)

| 面 (Surface) | URL | 検証 (Verification) |
| --- | --- | --- |
| Repository | <https://github.com/affaan-m/everything-claude-code> | `git remote get-url origin` |
| Evidence source commit | <https://github.com/affaan-m/everything-claude-code/commit/81fca2cea6f1399c52c8faa70f9a17e42f0bd447> | evidence capture 時の `git rev-parse HEAD` |
| Release pack folder | <https://github.com/affaan-m/everything-claude-code/tree/main/docs/releases/2.0.0-rc.1> | `81fca2ce` からキャプチャした release pack evidence |
| Release notes draft | <https://github.com/affaan-m/everything-claude-code/blob/main/docs/releases/2.0.0-rc.1/release-notes.md> | In-tree release copy |
| Hermes setup guide | <https://github.com/affaan-m/everything-claude-code/blob/main/docs/HERMES-SETUP.md> | In-tree sanitized Hermes guide |
| May 18 evidence snapshot | <https://github.com/affaan-m/everything-claude-code/blob/main/docs/releases/2.0.0-rc.1/publication-evidence-2026-05-18.md> | 現行の最強 readiness evidence |
| May 18 operator dashboard | <https://github.com/affaan-m/everything-claude-code/blob/main/docs/releases/2.0.0-rc.1/operator-readiness-dashboard-2026-05-18.md> | Prompt-to-artifact dashboard |
| Pushed-head CI | <https://github.com/affaan-m/everything-claude-code/actions/runs/26011460500> | `81fca2ce` で CI 37/37 job 合格、supply-chain IOC scan job を含む |
| Latest Supply-Chain Watch | <https://github.com/affaan-m/everything-claude-code/actions/runs/26010432490> | `25ac57ac` で Supply-Chain Watch 合格; 公開前に最終 release commit から再実行 |
| npm package page | <https://www.npmjs.com/package/ecc-universal> | `npm view ecc-universal name version dist-tags --json` が `latest: 1.10.0` を返した; rc.1 はまだ未公開 |
| Codex marketplace CLI docs | <https://developers.openai.com/codex/cli/reference#codex-plugin-marketplace> | 公式 docs が GitHub shorthand、Git URL、SSH URL、local marketplace root 向け `codex plugin marketplace add` を列挙 |
| Codex official Plugin Directory status | <https://developers.openai.com/codex/plugins/build#publish-official-public-plugins> | 公式 docs は public Plugin Directory publishing と self-serve management が coming soon と記載 |

## 承認ゲート URL (Approval-Gated URLs)

| 面 (Surface) | 意図する URL またはコマンド (Intended URL or command) | 使用前のゲート (Gate before use) |
| --- | --- | --- |
| GitHub prerelease | <https://github.com/affaan-m/everything-claude-code/releases/tag/v2.0.0-rc.1> | `gh release view v2.0.0-rc.1 --repo affaan-m/everything-claude-code --json tagName,url,isPrerelease` が prerelease を返すこと |
| npm rc package | <https://www.npmjs.com/package/ecc-universal/v/2.0.0-rc.1> | `npm publish --tag next` 承認と公開後 `npm view ecc-universal dist-tags --json` |
| Claude plugin tag | `claude plugin tag .claude-plugin --dry-run`、承認後のみ実 tag | クリーンな release commit と plugin tag/push 承認 |
| Codex repo marketplace install | `codex plugin marketplace add affaan-m/everything-claude-code --ref v2.0.0-rc.1` | GitHub tag が存在すること; 公式 Plugin Directory submission は別途 |
| ECC Tools native-payments announcement | ECC Tools Marketplace/App URL と billing readiness readback | Marketplace 管理テストアカウントが `announcementGate.ready === true` を返すこと |
| Public announcements | X、LinkedIn、GitHub release、longform URL | GitHub release、npm、plugin、billing URL が先に解決すること |

## 投稿前チェック (Pre-Post Check)

公開直前に次を実行してください：

```bash
git status --short --branch
gh release view v2.0.0-rc.1 --repo affaan-m/everything-claude-code --json tagName,url,isPrerelease
npm view ecc-universal name version dist-tags --json
codex plugin marketplace add --help
rg -n "TODO|TBD|PLACEHOLDER" docs/releases/2.0.0-rc.1
npm run preview-pack:smoke
```

上記の approval-gated URL がクリーンなリリースコミットから解決するまで、ソーシャルまたは通知コピーを投稿しないでください。
