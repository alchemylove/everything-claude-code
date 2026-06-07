---
description: "PHP patterns: common ルールの拡張"
globs: ["**/*.php", "**/composer.json"]
alwaysApply: false
---
# PHP Patterns (PHP Patterns)

> このファイルは common patterns ルールを PHP 固有の内容で拡張する。

## Thin Controllers, Explicit Services

- controller は transport に集中: auth、validation、serialization、status code。
- ビジネスルールは HTTP bootstrapping なしで test しやすい application/domain service に移す。

## DTOs and Value Objects

- shape の重い associative array は request、command、外部 API payload 用の DTO に置き換える。
- money、identifier、制約付き概念には value object を使用。

## Dependency Injection

- framework グローバルではなく interface または狭い service contract に依存。
- service-locator 検索なしで test 可能にするため、collaborator は constructor 経由で渡す。
