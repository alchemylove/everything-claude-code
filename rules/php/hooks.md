---
paths:
  - "**/*.php"
  - "**/composer.json"
  - "**/phpstan.neon"
  - "**/phpstan.neon.dist"
  - "**/psalm.xml"
---
# PHP フック (PHP Hooks)

> このファイルは [common/hooks.md](../common/hooks.md) を拡張し、PHP 固有の内容を追加する。

## PostToolUse フック (PostToolUse Hooks)

`~/.claude/settings.json` で設定:

- **Pint / PHP-CS-Fixer**: 編集した `.php` ファイルを自動フォーマット。
- **PHPStan / Psalm**: 型付きコードベースで PHP 編集後に静的解析を実行。
- **PHPUnit / Pest**: 挙動に影響する編集時は、触れたファイルまたはモジュールの対象テストを実行。

## 警告 (Warnings)

- 編集ファイルに `var_dump`、`dd`、`dump`、`die()` が残っている場合は警告。
- 編集した PHP ファイルが生 SQL を追加したり CSRF/セッション保護を無効化した場合は警告。
