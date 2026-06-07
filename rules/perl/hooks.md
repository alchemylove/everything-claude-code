---
paths:
  - "**/*.pl"
  - "**/*.pm"
  - "**/*.t"
  - "**/*.psgi"
  - "**/*.cgi"
---
# Perl フック (Perl Hooks)

> このファイルは [common/hooks.md](../common/hooks.md) を拡張し、Perl 固有の内容を追加する。

## PostToolUse フック (PostToolUse Hooks)

`~/.claude/settings.json` で設定:

- **perltidy**: 編集後に `.pl` と `.pm` ファイルを自動フォーマット
- **perlcritic**: `.pm` ファイル編集後にリントチェックを実行

## 警告 (Warnings)

- スクリプト以外の `.pm` ファイルでの `print` について警告する — `say` またはロギングモジュール（例: `Log::Any`）を使用すること
