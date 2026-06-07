---
description: "PHP security: common ルールの拡張"
globs: ["**/*.php", "**/composer.lock", "**/composer.json"]
alwaysApply: false
---
# PHP Security (PHP Security)

> このファイルは common security ルールを PHP 固有の内容で拡張する。

## Database Safety

- 動的 query には prepared statement（`PDO`、Doctrine、Eloquent query builder）を使用。
- ORM mass-assignment を慎重にスコープし、書き込み可能フィールドを whitelist。

## Secrets and Dependencies

- secret は commit された config ファイルではなく、環境変数または secret manager から読み込む。
- CI で `composer audit` を実行し、依存追加前に package の信頼性をレビュー。

## Auth and Session Safety

- password 保存には `password_hash()` / `password_verify()` を使用。
- authentication と privilege 変更後に session identifier を再生成。
- 状態変更 web リクエストでは CSRF 保護を強制。
