# JoyCode Adapter ガイド (JoyCode Adapter Guide)

JoyCode は selective installer 経由で ECC を利用できます。adapter は共有 ECC commands、agents、skills、およびフラット化された rules をプロジェクトローカルの `.joycode/` ディレクトリにインストールします。

## インストール (Install)

インストールプランをプレビュー：

```bash
node scripts/install-plan.js --target joycode --profile full
```

現在のプロジェクトに適用：

```bash
node scripts/install-apply.js --target joycode --profile full
```

より小さなインストールの場合は、モジュールを明示的に選択：

```bash
node scripts/install-apply.js --target joycode --modules rules-core,commands-core,workflow-quality
```

## レイアウト (Layout)

プロジェクト adapter は次の配下に管理対象ファイルを書き込みます：

```text
.joycode/
  agents/
  commands/
  rules/
  skills/
  mcp-configs/
  scripts/
  ecc-install-state.json
```

rules は名前空間付きファイル名にフラット化されるため、JoyCode プロジェクトは `rules/common/coding-style.md` のようなネストされた rule ディレクトリを受け取りません。commands、agents、skills は ECC の他の場所と同じ構造を維持します。
full profile には、他の ECC プロジェクトローカル adapter が使う共有 MCP とセットアップヘルパーファイルも含まれます。

## アンインストール (Uninstall)

手動でファイルを削除するのではなく、ECC の管理対象アンインストールパスを使用してください：

```bash
node scripts/uninstall.js --target joycode
```

アンインストールコマンドは `.joycode/ecc-install-state.json` を読み、ECC がインストールしたファイルのみを削除します。ユーザーが作成した JoyCode ファイルは保持されます。

## ソース PR (Source PR)

この adapter は、古い PR #1429 から有用なプロジェクトローカル JoyCode の意図を救済し、スタンドアロンの shell installer を ECC の現在の install-state と uninstall 機構に置き換えます。
