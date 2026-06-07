---
description: アーキテクチャ、async の正しさ、依存性注入、Pydantic スキーマ、セキュリティ、パフォーマンス、テスト容易性の観点で FastAPI アプリケーションをレビューする。
---

# FastAPI レビュー (FastAPI Review)

`fastapi-reviewer` エージェントを起動し、FastAPI に特化したレビューを行う。

## 使い方 (Usage)

```text
/fastapi-review [file-or-directory]
```

## レビュー領域 (Review Areas)

- App factory、ルーター境界、ミドルウェア、例外ハンドラー。
- Pydantic のリクエスト/レスポンススキーマ分離。
- データベースセッション、認証、ページネーション、設定の依存性注入。
- async データベースと外部 HTTP パターン。
- CORS、認証、レート制限、ロギング、シークレット処理。
- OpenAPI メタデータとドキュメント化されたレスポンスモデル。
- テストクライアントのセットアップと依存性のオーバーライド。

## 期待される出力 (Expected Output)

```text
[SEVERITY] Short issue title
File: path/to/file.py:42
Issue: What is wrong and why it matters.
Fix: Concrete change to make.
```

## 関連 (Related)

- Agent: `fastapi-reviewer`
- Skill: `fastapi-patterns`
- Command: `/python-review`
- Skill: `security-scan`
