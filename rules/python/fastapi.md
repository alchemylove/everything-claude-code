---
paths:
  - "**/app/**/*.py"
  - "**/fastapi/**/*.py"
  - "**/*_api.py"
---
# FastAPI ルール (FastAPI Rules)

FastAPI プロジェクトでは、一般的な Python ルールと併せて本ルールを使用する。

## 構造 (Structure)

- アプリ構築は `create_app()` に置く。
- router は薄く保ち、persistence とビジネスロジックは service または CRUD helper に移す。
- request schema、update schema、response schema は分離する。
- データベース session と auth は dependency に置く。

## 非同期 (Async)

- I/O を行う endpoint には `async def` を使用する。
- async endpoint からは async database と HTTP client を使用する。
- async route から `requests`、sync SQLAlchemy session、ブロッキングな file／network 操作を呼び出さない。

## Dependency Injection

```python
@router.get("/users/{user_id}")
async def get_user(
    user_id: str,
    db: AsyncSession = Depends(get_db),
    current_user: User = Depends(get_current_user),
):
    ...
```

route handler 内で `SessionLocal()` や長寿命 client を作成しない。

## Schema

- response model に password、password hash、access token、refresh token、内部 auth 状態を含めない。
- アプリケーションデータを返す endpoint には `response_model` を使用する。
- Pydantic で表現できるルールは手書き検証ではなく field constraint を使用する。

## セキュリティ (Security)

- CORS origin は環境ごとに設定する。
- wildcard origin と credentialed CORS を併用しない。
- JWT の expiry、issuer、audience、algorithm を検証する。
- auth と write 負荷の高い endpoint に rate limit を適用する。
- ログから credential、cookie、authorization header、token をマスクする。

## テスト (Testing)

- `Depends` で使用している dependency を正確に override する。
- テスト後に `app.dependency_overrides` をクリアする。
- async アプリケーションには async test client を優先する。

skill: `fastapi-patterns` を参照。
