---
description: "PHP hooks: common ルールの拡張"
globs: ["**/*.php", "**/composer.json", "**/phpstan.neon", "**/phpstan.neon.dist", "**/psalm.xml"]
alwaysApply: false
---
# PHP Hooks (PHP Hooks)

> このファイルは common hooks ルールを PHP 固有の内容で拡張する。

## PostToolUse Hooks

`~/.claude/settings.json` で設定:

- **Pint / PHP-CS-Fixer**: 編集した `.php` ファイルを auto-format。
- **PHPStan / Psalm**: typed codebase で PHP 編集後に static analysis を実行。
- **PHPUnit / Pest**: 振る舞いに影響する編集時、触れたファイルまたはモジュールの targeted test を実行。

## 警告 (Warnings)

- 編集ファイルに残った `var_dump`, `dd`, `dump`, `die()` を警告。
- 編集した PHP ファイルが raw SQL を追加したり CSRF/session 保護を無効化した場合に警告。
