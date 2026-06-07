# ECC v2.0.0-rc.1 命名と公開マトリクス (ECC v2.0.0-rc.1 Naming And Publication Matrix)

スナップショット日: 2026-05-19。GitHub prerelease と npm `next` readback 成功後、2026-05-26 に公開状態を更新。

このマトリクスは公開リポジトリ名 `affaan-m/ECC` への変更後の rc.1 アイデンティティを記録します。計画用エビデンスであり、公開アクションではありません。

## 決定 (Decision)

`v2.0.0-rc.1` では、公開アイデンティティを **ECC** として出荷する。

コピー、plugin slug、ステータスサーフェス、図、リリース資料では `affaan-m/ECC` を正規 GitHub repo、`ECC` をプロダクト名として使用する。別途 post-rc 移行計画が存在するまで、npm package と package エントリポイントは `ecc-universal` のまま維持する。

理由:

- 現在のインストールサーフェスは `ecc-universal` プラス `ecc` plugin slug として既に機能している;
- 正確な npm package 名 `ecc` は無関係な楕円曲線暗号 package により占有されている;
- `affaan-m/ECC` が live な公開 GitHub repo である;
- Claude と Codex plugin サーフェスは `ecc` として既に十分短い;
- rc.1 は npm/package 改名前にリリース、plugin、公開パイプラインを証明すべきである。

## 現行値 (Current Values)

| サーフェス (Surface) | 現在値 (Current value) | エビデンスコマンド (Evidence command) | 現在の結果 (Current result) | リリース判断 (Release decision) |
| --- | --- | --- | --- | --- |
| プロダクト表示名 (Product display name) | `ECC` | `rg -n "^# ECC\|displayName.*ECC\|affaan-m/ECC" README.md .codex-plugin/plugin.json docs/releases/2.0.0-rc.1` | README、plugin manifest、release copy、URL ledger 全体に存在 | rc.1 と GA で維持 |
| GitHub repo | `affaan-m/ECC` | `git remote get-url origin` | `https://github.com/affaan-m/ECC.git` | rc.1 と GA で維持 |
| npm package | `ecc-universal` | `node -p "require('./package.json').name"` | `ecc-universal` | rc.1 で維持 |
| npm package version | ローカル `2.0.0-rc.1`、registry latest `1.10.0`、registry next `2.0.0-rc.1` | `node -p "require('./package.json').version"` and `npm view ecc-universal name version dist-tags --json` | ローカル rc.1 は準備完了; registry latest は `1.10.0` のまま; `next` は `2.0.0-rc.1` を指す | rc は `next` に維持、`latest` には移動しない |
| 正確な npm 短名 (Exact npm short name) | `ecc` | `npm view ecc name version description repository.url --json` | 無関係な `ecc@0.0.2` に占有、"Elliptic curve cryptography functions." | 使用しない |
| スコープ付き npm 短名 (Scoped npm short name) | `@affaan-m/ecc` | `npm view @affaan-m/ecc name version --json` | Registry 404 | npm scope policy が許可すれば将来の scoped package 候補 |
| 旧 package 名 (Former package name) | `everything-claude-code` | `npm view everything-claude-code name version dist-tags --json` | Registry は 2026-02-07 に unpublished と報告 | rc.1 では復活させない |
| Claude plugin slug | `ecc` | `node -p "require('./.claude-plugin/plugin.json').name"` | `ecc` | 維持 |
| Claude plugin version | `2.0.0-rc.1` | `claude plugin validate .claude-plugin/plugin.json`; `claude plugin tag .claude-plugin --dry-run` | Claude Code `2.1.143` で検証合格; dry run は `ecc--v2.0.0-rc.1` を作成する | release-tag gate 準備完了 |
| Claude marketplace エントリ (Claude marketplace entry) | `ecc` | `.claude-plugin/marketplace.json`; `claude plugin marketplace add --help`; Anthropic plugin marketplace docs | version と repo が現行 rc.1 surface を指す; GitHub、git URL、remote marketplace JSON、local path marketplace source がサポート | 維持 |
| Codex plugin slug | `ecc` | `node -p "require('./.codex-plugin/plugin.json').name"` | `ecc` | 維持 |
| Codex plugin version | `2.0.0-rc.1` | `node tests/plugin-manifest.test.js`; `node tests/docs/ecc2-release-surface.test.js` | Plugin manifest 54/54 合格; Codex CLI `0.131.0` で release surface 21/21 合格 | Codex marketplace/manual marketplace gate 準備完了 |
| Codex repo marketplace | `ecc` | `.agents/plugins/marketplace.json`; `codex plugin marketplace add --help`; OpenAI Codex plugin docs | repo marketplace add は GitHub shorthand、Git URL、SSH URL、local root、`--ref`、`--sparse` をサポート; local と GitHub-ref temp-home add smoke が合格 | rc.1 Codex 配布パスとして使用 |
| OpenCode package | `ecc-universal` | `node -p "require('./.opencode/package.json').name"` | `ecc-universal` | 維持 |
| OpenCode build | 生成された package 出力 | `npm run build:opencode` | 合格 | package dry-run gate 準備完了 |
| npm pack surface | 縮小された runtime package | `NPM_CONFIG_USERCONFIG=/dev/null npm pack --dry-run --json` | `ecc-universal-2.0.0-rc.1.tgz`、2228 entries、packed 4,348,504 bytes、unpacked 13,024,929 bytes を生成 | 最終 release-commit からの再実行が必要 |

## 公開パス (Publication Paths)

| パス (Path) | 現在のエビデンス (Current evidence) | 次に必要なアクション (Required next action) | ブロッカー (Blocker) |
| --- | --- | --- | --- |
| GitHub release | `v2.0.0-rc.1` prerelease が <https://github.com/affaan-m/ECC/releases/tag/v2.0.0-rc.1> で live | release notes を URL ledger と整合; フォローアップ release 編集前にエビデンスを再実行 | 残りの plugin、video、billing、outbound URL は gated のまま |
| npm | `ecc-universal@2.0.0-rc.1` が `next` で live; registry latest は `1.10.0` のまま | rc は `next` に維持; GA 承認前に `latest` へ移動しない | 残りの plugin、video、billing、outbound URL は gated のまま |
| Claude plugin | `claude plugin validate .claude-plugin/plugin.json` 合格; `claude plugin tag --help` が release tag flow が `{name}--v{version}` tag を作成して push できることを確認 | clean release commit から `claude plugin tag .claude-plugin --dry-run` を実行し、release 承認後のみ tag/push | この pass では plugin release tag は未作成 |
| Claude marketplace | `.claude-plugin/marketplace.json` が `ecc` と公開 repo を指す | tag 存在後に marketplace update/install path を検証 | 外部 marketplace 伝播は未検証 |
| Codex plugin | `codex plugin marketplace` が local と Git marketplace source をサポート; `.codex-plugin/plugin.json` が存在; `.agents/plugins/marketplace.json` が repo root から `ecc` を公開; temp-home local と GitHub-ref marketplace add が合格 | repo-marketplace コマンドで rc.1 docs を公開し、OpenAI 公式 Plugin Directory path を監視 | OpenAI 提出エビデンス前に公式 Plugin Directory listing を主張しない |
| OpenCode package | `.opencode/package.json` がソースからビルドし npm package 内に同梱 | release commit から `npm run build:opencode` と package dry-run を再実行 | OpenCode CLI 1.2.21 はこの pass で別途 plugin publication コマンドを公開していない |
| ECC Tools billing 主張 (ECC Tools billing claim) | README と launch copy が ECC Tools / marketplace 文脈に言及 | ECC-Tools #89/#90/#91 が selected-target billing readback、selected-target announcement gating、無視される `--env-file` サポートを追加; #92 が non-breaking operator bearer path を追加; #93 が live selected-target gate pass を記録 | billing エビデンス準備完了; payment 告知前に live selected-target gate を再実行 |
| ソーシャルと longform コピー (Social and longform copy) | X thread、LinkedIn copy、article outline、GitHub release copy が存在 | 古い URL を置換し、残りの plugin/video/billing/outbound gate 承認後のみ公開 | GitHub prerelease と npm URL は live; plugin、video、billing、outbound URL は未確定 |

## ITO-46 ブロッカーレジスタ (ITO-46 Blocker Register)

| チャネル (Channel) | 現在のステータス (Current status) | 必要なメタデータ/エビデンス (Required metadata/evidence) | オーナー (Owner) | ブロッカーまたはフォローアップ (Blocker or follow-up) |
| --- | --- | --- | --- | --- |
| GitHub release | <https://github.com/affaan-m/ECC/releases/tag/v2.0.0-rc.1> で live prerelease | tag、release URL、prerelease flag、最終 release notes、URL ledger | Release owner | release 編集は最終エビデンスと owner 承認の背後に置く |
| npm | `ecc-universal@2.0.0-rc.1` が `next` に公開済み; registry latest は `1.10.0` | pack summary、publish readback、`next` dist-tag readback、registry signature エビデンス | Package owner | GA 承認前に rc.1 を `latest` へ移動しない |
| 短い npm 名 (Short npm name) | `ecc` は占有済み; `@affaan-m/ecc` は 404 | 名前可用性出力と移行計画 | Release owner | rc.1 では `ecc-universal` を維持; scoped rename は post-rc のみ |
| Claude plugin | `ecc@2.0.0-rc.1` は検証合格; tag dry run は `ecc--v2.0.0-rc.1` を作成する | `claude plugin validate .`, `claude plugin tag .claude-plugin --dry-run`, marketplace install/update smoke | Plugin owner | 実 tag push と marketplace 伝播には release 承認が必要 |
| Claude marketplace | docs と CLI が GitHub、git URL、remote marketplace JSON、local path source をサポート | 公開 repo marketplace JSON、support/contact metadata、post-tag install smoke | Plugin owner | この pass では外部公式 listing は未提出 |
| Codex repo marketplace | Codex CLI `0.131.0` で local と GitHub-ref temp-home marketplace add smoke が合格 | `.codex-plugin/plugin.json`、`.agents/plugins/marketplace.json`、repo/personal marketplace エビデンス | Plugin owner | 公式 Plugin Directory listing には OpenAI submission/listing エビデンスが必要 |
| Codex 公式 Plugin Directory (Codex official Plugin Directory) | OpenAI docs が curated 公式 directory を説明; ECC は listing エビデンスを提出または受領していない | directory submission link または利用可能になった OpenAI 承認パス | Plugin owner | ITO-56/ITO-46 フォローアップとして追跡; 公式 listing を主張しない |
| OpenCode package | `npm run build:opencode` 合格 | npm tarball 内にビルド済み `.opencode` package metadata | Package owner | 別途公開 plugin channel は未特定; npm に従う |
| Billing/native payments | Marketplace Pro target readback、selected-target announcement preflight、env-file operator path、non-breaking operator bearer、live selected-target gate が合格 | 2026-05-20 selected-target readback、webhook provenance、selected-target announcement gate、ECC-Tools #91 `--env-file` サポート、ECC-Tools #92 operator bearer、ECC-Tools #93 live gate エビデンス | ECC Tools owner | rc.1 告知直前に live gate を再実行; 最終コピーは release/plugin/live URL 承認待ち |
| ソーシャル/longform コピー (Social/longform copy) | draft 存在; GitHub と npm リンクは live | 最終 live GitHub、npm、Claude、Codex、video、billing URL | Release owner | 残りの plugin/video/billing/outbound 承認後のみ公開 |

## rc.1 後の package 改名 (Package Rename After rc.1)

package レイヤーが rc.1 後に `ecc-universal` からより短い npm サーフェスへ移行する場合、段階的移行として実施する:

1. 代替 package に検証済みオーナー、deprecation 計画、インストール移行があるまで `ecc-universal` を npm package として維持する。
2. 公開 docs、release notes、plugin marketplace エントリ、npm metadata、外部リンクでは `affaan-m/ECC` を正規 repo として維持する。
3. package 改名を告知する前に、新しい npm/package サーフェスを予約または作成する。
4. 旧コマンド、package 名、plugin slug、docs URL を新名称へマッピングする互換ガイドを出荷する。

## このパスで記録したエビデンス (Evidence Captured In This Pass)

```text
git rev-parse HEAD
67e63e63f9bfd074bd6a21bf6bac71f3dfefa58b

node -p "require('./package.json').name + '@' + require('./package.json').version"
ecc-universal@2.0.0-rc.1

node -p "require('./.claude-plugin/plugin.json').name + '@' + require('./.claude-plugin/plugin.json').version"
ecc@2.0.0-rc.1

node -p "require('./.codex-plugin/plugin.json').name + '@' + require('./.codex-plugin/plugin.json').version"
ecc@2.0.0-rc.1

node -p "require('./.opencode/package.json').name + '@' + require('./.opencode/package.json').version"
ecc-universal@2.0.0-rc.1

npm view ecc name version description repository.url --json
ecc@0.0.2 は無関係な楕円曲線暗号 package に占有されている。

npm view ecc-universal name version dist-tags --json
registry latest は 1.10.0; next は 2.0.0-rc.1。

npm view ecc-universal@2.0.0-rc.1 name version dist.tarball dist.integrity time --json
registry は version 2.0.0-rc.1、rc tarball URL、公開時刻
2026-05-26T00:36:22.940Z を返却。

claude plugin validate .claude-plugin/plugin.json
Claude Code 2.1.143 で検証合格。

claude plugin validate .
想定内の警告 1 件で検証合格: ルート CLAUDE.md は plugin context として
読み込まれない; plugin context は skills 経由で出荷する。

claude plugin tag .claude-plugin --dry-run
tag ecc--v2.0.0-rc.1 を作成して push する。

node tests/docs/ecc2-release-surface.test.js
release-surface チェック 21 件合格。

node tests/plugin-manifest.test.js
plugin-manifest チェック 54 件合格。

npm run build:opencode
合格。

npm pack --dry-run --json
ecc-universal-2.0.0-rc.1.tgz を生成。2228 entries、packed 4,348,504 bytes、
unpacked 13,024,929 bytes。

npm publish --tag next --dry-run
dry run は tag next で ecc-universal@2.0.0-rc.1 を npm に公開する。

codex plugin marketplace add --help
GitHub shorthand、HTTP(S) Git URLs、SSH URLs、local marketplace roots、
--ref、Git-only --sparse をサポート。

HOME="$(mktemp -d)" codex plugin marketplace add <local-checkout>
marketplace ecc を追加し、installed marketplace root を
<local-checkout> として記録。実 Codex config には触れない。

HOME="$(mktemp -d)" codex plugin marketplace add affaan-m/ECC --ref "$(git rev-parse HEAD)"
GitHub repo から marketplace ecc を追加。pin は
67e63e63f9bfd074bd6a21bf6bac71f3dfefa58b。実 Codex config には触れない。
```
