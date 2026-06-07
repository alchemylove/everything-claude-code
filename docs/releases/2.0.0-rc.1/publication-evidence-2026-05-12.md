# ECC v2.0.0-rc.1 公開証跡 — 2026-05-12 (ECC v2.0.0-rc.1 Publication Evidence — 2026-05-12)

これは dry-run リリース証跡のみです。GitHub release、npm publication、plugin tag、marketplace submission、announcement post は作成しません。

## ソースコミット (Source Commit)

| 項目 (Field) | エビデンス (Evidence) |
| --- | --- |
| Upstream main base | `0598af70a51346bae34d987b9bed143386055967` |
| Evidence branch | `codex/release-publication-evidence` |
| Evidence scope | このブランチの package hygiene と release-doc 更新を含む working tree |
| Git remote | `https://github.com/affaan-m/everything-claude-code.git` |
| Local status caveat | working tree に無関係な未追跡 `docs/drafts/` ディレクトリが存在 |

実際のリリース担当者は、公開前に最終リリースコミットから clean checkout でこれらのチェックを繰り返す必要があります。

## レジストリとリリース状態 (Registry And Release State)

| サーフェス (Surface) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| GitHub prerelease | `gh release view v2.0.0-rc.1 --repo affaan-m/everything-claude-code --json tagName,url,isPrerelease` | `release not found`（未作成） |
| npm dist-tags | `npm view ecc-universal dist-tags --json` | `{ "latest": "1.10.0" }` |
| npm package metadata | `node -p "require('./package.json').name + '@' + require('./package.json').version"` | `ecc-universal@2.0.0-rc.1` |
| Product identity | `rg -n "Everything Claude Code" README.md CHANGELOG.md docs/releases/2.0.0-rc.1` | README と rc.1 release docs に存在 |

## npm ドライラン (npm Dry Run)

最初の pack pass では、広い package `files` エントリが untracked なローカル `__pycache__` パスを含んでいたため、tarball にローカル Python bytecode cache ファイルが露出しました。このブランチでは明示的な package-file 除外と regression test を追加し、Python bytecode が package surface に現れた場合は `npm pack` が失敗するようにしています。

| コマンド (Command) | 結果 (Result) |
| --- | --- |
| `node tests/scripts/npm-publish-surface.test.js` | `2/2` 合格; Python bytecode 除外アサーションを含む |
| `npm pack --dry-run --json` | `ecc-universal-2.0.0-rc.1.tgz`; `entryCount: 965`; `size: 1565968`; `unpackedSize: 4934637`; `hasBytecode: false` |
| `npm publish --tag next --dry-run --json` | dry-run ターゲットは `tag next` 付き npm registry; `entryCount: 965`; `hasBytecode: false` |

一時的な install smoke:

| コマンド (Command) | 結果 (Result) |
| --- | --- |
| `npm pack --pack-destination /tmp/ecc-publication-smoke-dd9ud5 --json` | ローカル install smoke 用に `ecc-universal-2.0.0-rc.1.tgz` を作成 |
| `npm install --prefix /tmp/ecc-publication-smoke-dd9ud5 /tmp/ecc-publication-smoke-dd9ud5/ecc-universal-2.0.0-rc.1.tgz` | 8 packages を追加 |
| `node /tmp/ecc-publication-smoke-dd9ud5/node_modules/ecc-universal/scripts/ecc.js --help` | ECC selective-install CLI ヘルプを出力 |
| `node /tmp/ecc-publication-smoke-dd9ud5/node_modules/ecc-universal/scripts/catalog.js profiles --json` | 6 つの install profile を返却: `minimal`, `core`, `developer`, `security`, `research`, `full` |
| `find /tmp/ecc-publication-smoke-dd9ud5/node_modules/ecc-universal -path '*__pycache__*' -o -name '*.pyc' -o -name '*.pyo' -o -name '*.pyd'` | 出力なし |

## プラグインと Harness 証跡 (Plugin And Harness Evidence)

| サーフェス (Surface) | コマンド (Command) | 結果 (Result) |
| --- | --- | --- |
| Claude plugin manifest | `claude plugin validate .claude-plugin/plugin.json` | 合格 |
| Claude plugin tag preflight | `claude plugin tag .claude-plugin --dry-run` | 無関係な未追跡 `docs/drafts/` によりブロック |
| Claude plugin tag forced dry-run | `claude plugin tag .claude-plugin --dry-run --force` | HEAD で `ecc--v2.0.0-rc.1` を作成する; メンテナーが判断しない限り実リリースでは `--force` を使用しない |
| Codex marketplace CLI | `codex plugin marketplace --help` と subcommand help | `add`, `upgrade`, `remove` をサポート; `add` は repo と local marketplace root をサポート |
| OpenCode package | `npm run build:opencode` | 合格 |
| Claude hook/plugin route | `node tests/hooks/hooks.test.js` | `236/236` 合格 |
| Codex release surface | `node tests/docs/ecc2-release-surface.test.js` | `18/18` 合格 |
| Agent/catalog metadata | `node tests/scripts/catalog.test.js` | `7/7` 合格 |
| Observability gate | `npm run observability:ready` | `16/16` 合格 |

## クリーンチェックアウト Claude Plugin スモーク (Clean-Checkout Claude Plugin Smoke)

この follow-up pass では、コミット `bfacf37715b39655cbc2c48f12f2a35c67cb0253` から detached clean worktree `/tmp/ecc-clean-plugin-evidence` を使用しました。隔離された temp home（`HOME=/tmp/ecc-clean-plugin-home`）と temp local project（`/tmp/ecc-plugin-install-smoke`）を使ったため、ユーザーの実際の Claude plugin config には書き込みませんでした。

| コマンド (Command) | 結果 (Result) |
| --- | --- |
| `git -C /tmp/ecc-clean-plugin-evidence status --short --branch` | dirty または untracked ファイルなしの `## HEAD (no branch)` |
| `claude plugin validate .claude-plugin/plugin.json` | 合格 |
| `claude plugin validate .claude-plugin/marketplace.json` | 合格 |
| `claude plugin tag .claude-plugin --dry-run` | `--force` なしで合格; HEAD で `ecc--v2.0.0-rc.1` を作成し `refs/tags/ecc--v2.0.0-rc.1` を push する |
| `claude plugin marketplace add /tmp/ecc-clean-plugin-evidence --scope local`（temp `HOME`） | local settings に marketplace `ecc` を追加 |
| `claude plugin list --available --json`（temp `HOME`） | `ecc@ecc`、version `2.0.0-rc.1`、source `./` を一覧 |
| `claude plugin install ecc@ecc --scope local`（temp `HOME`） | local scope に `ecc@ecc` をインストール |
| `claude plugin list --json`（temp `HOME`） | `ecc@ecc`、version `2.0.0-rc.1`、enabled、local scope、install path は `/tmp/ecc-clean-plugin-home/.claude/plugins/cache/ecc/ecc/2.0.0-rc.1` 配下 |
| `claude plugin uninstall ecc@ecc --scope local`（temp `HOME`） | アンインストール成功; 最終 plugin list は `[]` |

## アナウンスメントプレースホルダーチェック (Announcement Placeholder Check)

forbidden-placeholder scan は、forbidden placeholder を名前で示す `publication-readiness` checklist の行のみを返した。launch-pack placeholder の実体は見つからなかった。

## 残存ブロッカー (Remaining Blockers)

- GitHub prerelease `v2.0.0-rc.1` を作成または検証する。
- npm dist-tag `next` で `ecc-universal@2.0.0-rc.1` を公開する。
- 明示的な承認後にのみ Claude plugin tag を作成して push する。clean checkout dry run と temp install smoke は現在合格している。
- ライブ Claude/Codex/OpenCode marketplace submission パスを確認するか、手動 submission の担当者とステータスを記録する。
- launch copy で使用する前に ECC Tools billing/App/Marketplace の主張を検証する。
- リリースと package/plugin URL が存在した後、live URL で announcement copy を更新する。
