# ECC v2.0.0-rc.1 リリース名とプラグイン公開チェックリスト (ECC v2.0.0-rc.1 Release Name And Plugin Publication Checklist)

スナップショット日: 2026-05-18。公開リポジトリ名を `affaan-m/ECC` に変更した後、2026-05-19 に正規リポジトリ決定を更新。GitHub prerelease と npm `next` の readback が成功した後、2026-05-26 にリリース/パッケージ状態を更新。

このチェックリストは、リリース名、パッケージ公開、Claude/Codex プラグイン配布のオペレーターゲートである。それ自体は公開アクションではない。タグ作成、npm 公開、マーケットプレイスフォーム送信、告知投稿の前に、正確なリリースコミットから実行すること。

## 確定済み rc.1 決定 (Fixed rc.1 Decision)

`v2.0.0-rc.1` を **ECC** として出荷する。

- GitHub リポジトリは `affaan-m/ECC` のまま維持する。
- npm パッケージは `ecc-universal` のまま維持する。
- Claude と Codex のプラグイン slug は `ecc` のまま維持する。
- npm prerelease は `latest` ではなく `next` dist-tag で公開する。
- rc.1 の前に npm パッケージ名を `ecc` または `@affaan-m/ecc` にリネームしない。
- rc.1 と GA リリースコピーの正規公開リポジトリとして `affaan-m/ECC` を扱う。

理由:

- `ecc-universal` が現在の動作するインストールとパッケージ surface である。
- npm の `ecc` は無関係な楕円曲線パッケージに占有されている。
- `@affaan-m/ecc` は npm 上で未請求だが、移行計画が必要になる。
- `affaan-m/ECC` が現在のライブ公開 GitHub リポジトリである。
- Claude と Codex はすでに `ecc` として望ましい短い namespace を公開している。

## 現在の Surface 証拠 (Current Surface Evidence)

| サーフェス (Surface) | 現在値 (Current value) | エビデンスコマンド (Evidence command) | 現在の結果 (Current result) | リリースアクション (Release action) |
| --- | --- | --- | --- | --- |
| Git commit | `67e63e63f9bfd074bd6a21bf6bac71f3dfefa58b` | `git rev-parse HEAD` | この ITO-46 証拠更新前のクリーンな `main` から記録 | 最終リリースコミットから再実行 |
| GitHub repo | `affaan-m/ECC` | `git remote get-url origin` | `https://github.com/affaan-m/ECC.git` | rc.1 と GA で維持 |
| npm package | ローカル `ecc-universal@2.0.0-rc.1`、registry `next`、registry `latest` は `1.10.0` | `node -p "require('./package.json').name + '@' + require('./package.json').version"` and `npm view ecc-universal name version dist-tags --json` | ローカル rc.1 準備完了; registry `next` は `2.0.0-rc.1` を指す; `latest` は `1.10.0` のまま | rc.1 を `next` に維持; GA 承認前に `latest` へ移動しない |
| 正確な npm 短名 (Exact npm short name) | `ecc` | `npm view ecc name version description repository.url --json` | 無関係な `ecc@0.0.2` に占有 | 使用しない |
| Scoped npm short name | `@affaan-m/ecc` | `npm view @affaan-m/ecc name version --json` | 404 | 移行計画後の候補のみ |
| Claude plugin | `ecc@2.0.0-rc.1` | `claude plugin validate .claude-plugin/plugin.json`; `claude plugin validate .`; `claude plugin tag .claude-plugin --dry-run` | Claude Code `2.1.143` で検証成功; フルプラグイン検証には想定内のルート `CLAUDE.md` context 警告が 1 件; dry run は `ecc--v2.0.0-rc.1` を作成する | 最終コミットから dry-run tag を再実行し、承認後にのみ tag/push |
| Claude marketplace | `.claude-plugin/marketplace.json` | `claude plugin marketplace add --help`; Anthropic plugin marketplace docs | GitHub repo、git URL、remote marketplace JSON、local path marketplace source がサポートされている | 最終証拠後に post-tag marketplace install/update path を検証 |
| Codex plugin | `ecc@2.0.0-rc.1` | `node tests/plugin-manifest.test.js`; `codex plugin marketplace add --help`; OpenAI Codex plugin docs | Plugin manifest は 54/54 合格; Codex CLI `0.131.0` で local と GitHub-ref repo marketplace smoke が合格 | rc.1 には repo marketplace を使用; OpenAI 公開パスが利用可能になるまで公式 directory listing を主張しない |
| OpenCode package | `ecc-universal@2.0.0-rc.1` | `node -p "require('./.opencode/package.json').name + '@' + require('./.opencode/package.json').version"` | rc.1 パッケージ identity と一致 | npm パッケージ公開に従う |
| Billing 主張 (Billing claim) | ECC Tools selected-target billing エビデンス ready | ECC Tools billing gate と Marketplace account readback | 5月20日 selected-target readback と live selected-target announcement gate が `announcementGateReady: true` で合格; 告知直前に再実行 | 最終リリース/プラグイン/live URL 承認がすべて green になるまで native payments を告知しない |

## 必須ゲート (Required Gate)

最終リリースコミットからこれらのチェックを実行し、リリースアクション前に正確な出力を新しい `publication-evidence-YYYY-MM-DD.md` ファイルに貼り付けること:

```bash
git status --short --branch
git rev-parse HEAD
git remote get-url origin
npm view ecc name version description repository.url --json
npm view @affaan-m/ecc name version --json
npm view ecc-universal name version dist-tags --json
node tests/plugin-manifest.test.js
node tests/docs/ecc2-release-surface.test.js
claude plugin validate .claude-plugin/plugin.json
claude plugin tag .claude-plugin --dry-run
codex plugin marketplace add --help
HOME="$(mktemp -d)" codex plugin marketplace add ./
HOME="$(mktemp -d)" codex plugin marketplace add affaan-m/ECC --ref "$(git rev-parse HEAD)"
npm pack --dry-run --json
npm publish --tag next --dry-run
npm run build:opencode
npm run preview-pack:smoke
npm run release:approval-gate -- --format json
```

リリースマシンでコマンドが利用できない場合は、正確なエラーを記録し、関連する公開アクションをブロックしたままにすること。

## 公開順序 (Publication Order)

| ステップ (Step) | アクション (Action) | 必要なエビデンス (Required evidence) | 停止条件 (Stop condition) |
| --- | --- | --- | --- |
| 1 | 名前とバージョンを凍結 | Package、Claude plugin、Codex plugin、OpenCode package、`VERSION`、リリースドキュメントがすべて `2.0.0-rc.1` と一致 | `preview`/`rc.1` の不一致 |
| 2 | クリーンなリリースブランチを検証 | `git status --short --branch` が意図したリリースコミットのみを示し、無関係な drift がない | 説明不能な dirty file |
| 3 | パッケージとプラグイン manifest を検証 | `node tests/plugin-manifest.test.js` と `node tests/docs/ecc2-release-surface.test.js` が合格 | Manifest または release-surface の失敗 |
| 4 | パッケージ surface を dry-run | `npm pack --dry-run --json`; `npm publish --tag next --dry-run` | 欠落ファイル、誤った dist-tag、publish dry-run の失敗 |
| 5 | Claude 配布を dry-run | `claude plugin validate`; `claude plugin tag .claude-plugin --dry-run`; marketplace source/help 証拠 | 検証、tag、install-smoke の失敗 |
| 6 | Codex repo marketplace を検証 | `codex plugin marketplace add --help`; temp-home local と GitHub-ref repo marketplace add smoke; OpenAI 公式 directory ステータスを記録 | repo marketplace の欠落または未検証の公式 directory ステータス |
| 7 | OpenCode パッケージを検証 | `npm run build:opencode` | ビルド失敗 |
| 8 | リリース URL ledger を再生成 | `release-url-ledger-YYYY-MM-DD.md` で live と approval-gated URL を分離 | プレースホルダー、private URL、announcement URL の drift |
| 9 | GitHub prerelease を検証 | `gh release view v2.0.0-rc.1 --json tagName,url,isPrerelease` | URL 欠落または誤った prerelease フラグ |
| 10 | npm rc を検証 | `npm view ecc-universal version dist-tags --json` が rc.1 を `next` に、latest を GA/stable に示す | rc.1 が `latest` に載る、または registry 出力が不明確 |
| 11 | 公開/プラグイン送信 | Claude 公式送信と Codex repo marketplace 証拠を記録 | フォーム未送信、listing 非表示、または docs ステータス変更 |
| 12 | 告知 | X、LinkedIn、GitHub release、longform コピーが最終 live URL を使用 | 最終 URL がまだ pending |

## 進めてはいけないこと (Do Not Proceed)

- 最終リリースコミットから `npm pack --dry-run --json` を取得する前に、追加の npm ビルドを公開しない。
- 最終リリースコミットから `claude plugin tag .claude-plugin --dry-run` が合格する前に、Claude プラグイン tag を作成または push しない。
- OpenAI が公開送信パスを文書化するか、プラグインが listing されたことを確認しない限り、公式 Codex Plugin Directory listing を主張しない。
- ECC Tools live Marketplace account readback が ready を返すまで、billing、Marketplace、native payments を告知しない。
- rc.1 が公開され、移行ガイドが旧インストール名から新インストール名へマッピングするまで、npm パッケージをリネームしない。
- 必須のプラグイン、video、billing、outbound URL がまだ approval-gated の間は、ソーシャルコピーを投稿しない。

## 外部配布ソース (External Distribution Sources)

- Anthropic Claude Code plugin docs: `https://code.claude.com/docs/en/plugins`
- Anthropic Claude Code marketplace docs:
  `https://code.claude.com/docs/en/plugin-marketplaces`
- OpenAI Codex plugin docs:
  `https://developers.openai.com/codex/plugins/build#add-a-marketplace-from-the-cli`

このスナップショット時点で、Anthropic は GitHub、git URL、remote marketplace JSON、local path source を通じた self-hosted marketplace 配布を文書化している。OpenAI は Codex の repo/personal marketplace 配布を文書化し、公式 Plugin Directory について説明しているが、ECC はこの pass で公式 directory listing を送信または受領していない。
