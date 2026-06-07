---
paths:
  - "**/*.php"
  - "**/composer.lock"
  - "**/composer.json"
---
# PHP セキュリティ (PHP Security)

> このファイルは [common/security.md](../common/security.md) を拡張し、PHP 固有の内容を追加する。

## 入出力 (Input and Output)

- フレームワーク境界でリクエスト入力を検証する（`FormRequest`、Symfony Validator、または明示的 DTO バリデーション）。
- テンプレートではデフォルトで出力をエスケープする。生 HTML レンダリングは正当化が必要な例外として扱う。
- クエリパラメータ、Cookie、ヘッダー、アップロードファイルのメタデータを検証なしで信頼しない。

## データベース安全性 (Database Safety)

- 動的クエリには prepared statement（`PDO`、Doctrine、Eloquent query builder）を使用する。
- コントローラ/ビューでの SQL 文字列連結を避ける。
- ORM の mass-assignment を慎重にスコープし、書き込み可能フィールドをホワイトリスト化する。

## シークレットと依存関係 (Secrets and Dependencies)

- シークレットは環境変数または secret manager から読み込み、コミット済み設定ファイルには置かない。
- CI で `composer audit` を実行し、依存追加前に新パッケージの maintainer 信頼性を確認する。
- major バージョンは意図的に pin し、放棄されたパッケージは速やかに削除する。

## 認証とセッション安全性 (Auth and Session Safety)

- パスワード保存には `password_hash()` / `password_verify()` を使用する。
- 認証および権限変更後にセッション識別子を再生成する。
- 状態を変更する Web リクエストでは CSRF 保護を強制する。

## 参照 (Reference)

Laravel 固有のセキュリティガイダンスは skill: `laravel-security` を参照。
