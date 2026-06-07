---
paths:
  - "**/*.php"
  - "**/composer.json"
---
# PHP コーディングスタイル (PHP Coding Style)

> このファイルは [common/coding-style.md](../common/coding-style.md) を拡張し、PHP 固有の内容を追加する。

## 標準 (Standards)

- **PSR-12** のフォーマットと命名規約に従う。
- アプリケーションコードでは `declare(strict_types=1);` を優先する。
- 新規コードでは可能な限りスカラー型ヒント、戻り値型、型付きプロパティを使用する。

## 不変性 (Immutability)

- サービス境界を跨ぐデータには不変 DTO と value object を優先する。
- 可能な場合は `readonly` プロパティまたは不変コンストラクタをリクエスト/レスポンスペイロードに使用する。
- 単純なマップには配列を使い、ビジネス上重要な構造は明示的なクラスに昇格させる。

## フォーマット (Formatting)

- フォーマットには **PHP-CS-Fixer** または **Laravel Pint** を使用する。
- 静的解析には **PHPStan** または **Psalm** を使用する。
- ローカルと CI で同じコマンドが実行されるよう Composer scripts をリポジトリに含める。

## インポート (Imports)

- 参照するクラス、インターフェース、trait には `use` 文を追加する。
- プロジェクトが完全修飾名を明示的に好む場合を除き、グローバル名前空間への依存は避ける。

## エラーハンドリング (Error Handling)

- 例外的な状態には例外を投げる。新規コードでは `false`/`null` を隠れたエラーチャネルとして返さない。
- ドメインロジックに到達する前に、フレームワーク/リクエスト入力を検証済み DTO に変換する。

## 参照 (Reference)

サービス/リポジトリのレイヤリングに関するより広いガイダンスは skill: `backend-patterns` を参照。
