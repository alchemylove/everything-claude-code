---
description: "PHP testing: common ルールの拡張"
globs: ["**/*.php", "**/phpunit.xml", "**/phpunit.xml.dist", "**/composer.json"]
alwaysApply: false
---
# PHP Testing (PHP Testing)

> このファイルは common testing ルールを PHP 固有の内容で拡張する。

## Framework

デフォルト test framework は **PHPUnit**。**Pest** も project が既に使用している場合は可。

## Coverage

```bash
vendor/bin/phpunit --coverage-text
# or
vendor/bin/pest --coverage
```

## Test 構成 (Test Organization)

- 高速 unit test と framework/database integration test を分離。
- 大きな手書き array の代わりに factory/builder で fixture を作成。
- HTTP/controller test は transport と validation に集中。ビジネスルールは service レベル test に移す。
