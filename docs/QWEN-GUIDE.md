# Qwen CLI Adapter ガイド (Qwen CLI Adapter Guide)

ECC は管理対象の command、agent、skill、rule、MCP 面を Qwen CLI のホームディレクトリにインストールできます。

## インストール (Install)

ECC リポジトリのルートから：

```bash
./install.sh --target qwen --profile minimal
```

ファイルをコピーする前に、より大きなインストールをプレビュー：

```bash
./install.sh --target qwen --profile full --dry-run
```

Qwen adapter は `~/.qwen/` に書き込み、管理対象ファイルの所有権を `~/.qwen/ecc-install-state.json` に記録します。

## インストール後のレイアウト (Installed Layout)

管理対象インストールは次を投入できます：

```text
~/.qwen/
  QWEN.md
  agents/
  commands/
  mcp-configs/
  rules/
  skills/
  ecc-install-state.json
```

インストーラーは rules のソースレイアウトを保持するため、言語ルールセットは `~/.qwen/rules/common/` や `~/.qwen/rules/typescript/` などのパスに残ります。

## 更新 (Updating)

ECC の更新を pull した後、同じインストールコマンドを再実行してください。インストーラーは install-state ファイルを使って ECC 管理ファイルを更新し、`~/.qwen/` 内の無関係なユーザーファイルは要求しません。

## アンインストール (Uninstalling)

Qwen ディレクトリ全体を削除するのではなく、管理対象のアンインストールパスを使用してください：

```bash
node scripts/uninstall.js --target qwen
```

これは `~/.qwen/ecc-install-state.json` に記録されたファイルを削除し、無関係な Qwen 設定はそのまま残します。

## スコープ (Scope)

このターゲットは、古い PR #1352 より意図的に狭く設計されています。保守可能な Qwen install-target の意図を現在の selective installer に移植し、Qwen の hook/event 契約が確認されるまで未検証の hook-runtime クレームは避けます。
