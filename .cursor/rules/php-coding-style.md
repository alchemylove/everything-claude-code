---
description: "PHP coding style: common ルールの拡張"
globs: ["**/*.php", "**/composer.json"]
alwaysApply: false
---
# PHP Coding Style (PHP Coding Style)

> このファイルは common coding style ルールを PHP 固有の内容で拡張する。

## 標準 (Standards)

- **PSR-12** のフォーマットと命名規則に従う。
- application コードでは `declare(strict_types=1);` を優先。
- 新規コードで可能な限り scalar type hint、return type、typed property を使用。

## 不変性 (Immutability)

- service 境界を越えるデータには不変 DTO と value object を優先。
- 可能な場合は request/response payload に `readonly` property または不変 constructor を使用。
- 単純な map には array を保持。ビジネス上重要な構造は明示的な class に昇格。

## フォーマット (Formatting)

- フォーマットに **PHP-CS-Fixer** または **Laravel Pint** を使用。
- static analysis に **PHPStan** または **Psalm** を使用。
