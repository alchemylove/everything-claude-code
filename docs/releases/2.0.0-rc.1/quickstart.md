# ECC v2.0.0-rc.1 クイックスタート (ECC v2.0.0-rc.1 Quickstart)

feature 作業に触れる前にリリース面を検証したい新規コントリビューター向けのパスです。

## クローン (Clone)

```bash
git clone https://github.com/affaan-m/ECC.git
cd ECC
```

クリーンな checkout から始める。プライベート operator 状態、生ワークスペースエクスポート、token、ローカル Hermes ファイルをリポジトリにコピーしない。

## インストール (Install)

```bash
npm ci
```

公開リリース面で使う Node ベースの検証と packaging ツールチェーンをインストールします。

checkout ではなく npm から rc.1 パッケージをインストールする場合：

```bash
npm install ecc-universal@next
```

`next` は現在 `ecc-universal@2.0.0-rc.1` に解決；release-candidate 期間中 `latest` は
`1.10.0` のままです。

## 検証 (Verify)

```bash
node tests/run-all.js
```

期待結果：すべてのテストが失敗ゼロで合格。リリース固有の drift には focused チェックを実行：

```bash
node tests/docs/ecc2-release-surface.test.js
```

次にローカル observability 面をチェック：

```bash
npm run observability:ready
```

これは loop status、session trace、harness audit、ECC2 tool-risk log 向けの
[observability readiness gate](../../architecture/observability-readiness.md) を実行します。

## 最初の Skill (First Skill)

まず `skills/hermes-imports/SKILL.md` を読む。

意図された ECC 2.0 パターンを示す：

- 繰り返し operator ワークフローを取る
- 認証情報、プライベート path、生ワークスペースエクスポート、個人 memory を除去
- durable ワークフロー形状を保つ
- サニタイズ結果を再利用可能な `SKILL.md` として公開

プライベート Hermes ワークフローを wholesale import から始めない。再利用可能な skill を1つ蒸留することから始める。

## Harness の切り替え (Switch Harness)

同じ skill ソースを harness 横断で使う：

- Claude Code は Claude plugin と native hook 経由で ECC を消費。
- Codex は `AGENTS.md`、`.codex-plugin/plugin.json`、MCP reference config 経由で ECC を消費。
- OpenCode は OpenCode package/plugin 面経由で ECC を消費。

ポータブル unit は依然 `skills/*/SKILL.md`。Harness 固有ファイルはそのソースを読み込みまたは適応すべきで、ワークフローを再定義しない。

## 次の Docs (Next Docs)

- [Hermes setup](../../HERMES-SETUP.md)
- [Cross-harness architecture](../../architecture/cross-harness.md)
- [Release notes](release-notes.md)
