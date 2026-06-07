---
paths:
  - "**/*.php"
  - "**/phpunit.xml"
  - "**/phpunit.xml.dist"
  - "**/composer.json"
---
# PHP テスト (PHP Testing)

> このファイルは [common/testing.md](../common/testing.md) を拡張し、PHP 固有の内容を追加する。

## フレームワーク (Framework)

デフォルトのテストフレームワークは **PHPUnit**。プロジェクトで **Pest** が設定されている場合は新規テストに Pest を優先し、フレームワークの混在を避ける。

## カバレッジ (Coverage)

```bash
vendor/bin/phpunit --coverage-text
# or
vendor/bin/pest --coverage
```

CI では **pcov** または **Xdebug** を優先し、カバレッジ閾値は口伝ではなく CI に保持する。

## テスト構成 (Test Organization)

- 高速な unit テストとフレームワーク/データベース統合テストを分離する。
- 大きな手書き配列の代わりに factory/builder で fixture を用意する。
- HTTP/コントローラテストは transport とバリデーションに集中し、ビジネスルールはサービスレベルのテストへ移す。

## Inertia (Inertia)

プロジェクトが Inertia.js を使用する場合、生 JSON アサーションの代わりに `assertInertia` と `AssertableInertia` でコンポーネント名と props を検証することを優先する。

## 参照 (Reference)

リポジトリ全体の RED -> GREEN -> REFACTOR ループは skill: `tdd-workflow` を参照。
Laravel 固有のテストパターン（PHPUnit と Pest）は skill: `laravel-tdd` を参照。
