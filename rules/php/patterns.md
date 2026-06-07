---
paths:
  - "**/*.php"
  - "**/composer.json"
---
# PHP パターン (PHP Patterns)

> このファイルは [common/patterns.md](../common/patterns.md) を拡張し、PHP 固有の内容を追加する。

## 薄いコントローラ、明示的なサービス (Thin Controllers, Explicit Services)

- コントローラは transport に集中させる: 認証、バリデーション、シリアライズ、ステータスコード。
- ビジネスルールは HTTP ブートストラップなしでテストしやすい application/domain サービスへ移す。

## DTO と value object (DTOs and Value Objects)

- 形状が複雑な連想配列は、リクエスト、コマンド、外部 API ペイロード用の DTO に置き換える。
- 金額、識別子、日付範囲など制約のある概念には value object を使用する。

## 依存性注入 (Dependency Injection)

- フレームワークのグローバルではなく、インターフェースまたは狭いサービス契約に依存する。
- コンストラクタ経由で協調者を渡し、service locator 参照なしでサービスをテスト可能にする。

## 境界 (Boundaries)

- モデル層が永続化以上の役割を担う場合、ORM モデルをドメイン判断から分離する。
- サードパーティ SDK は小さなアダプタでラップし、コードベースの残りは相手側ではなく自前の契約に依存させる。

## 参照 (Reference)

エンドポイント規約とレスポンス形状のガイダンスは skill: `api-design` を参照。
Laravel 固有のアーキテクチャガイダンスは skill: `laravel-patterns` を参照。
