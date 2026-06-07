---
name: database-migrations
description: Database migration best practices for schema changes, data migrations, rollbacks, and zero-downtime deployments across PostgreSQL, MySQL, and common ORMs (Prisma, Drizzle, Kysely, Django, TypeORM, golang-migrate).
origin: ECC
---

# データベースマイグレーションパターン (Database Migration Patterns)

本番環境システム用の安全で可逆的なデータベーススキーマ変更。

## アクティベーション時期 (When to Activate)

- データベーステーブルの作成または変更
- 列またはインデックスの追加/削除
- データマイグレーション（バックフィル、変換）の実行
- ゼロダウンタイムスキーマ変更を計画
- 新しいプロジェクト用のマイグレーションツール設定

## コア原則 (Core Principles)

1. **すべての変更はマイグレーション** — 本番環境データベースを手動で変更しない
2. **マイグレーションは本番環境で前方のみ** — ロールバックは新しい前向きマイグレーション使用
3. **スキーマとデータマイグレーションは分離** — 1つのマイグレーションでDDLとDMLを混ぜない
4. **本番環境サイズのデータに対してマイグレーションをテスト** — 100行で機能するマイグレーション10M上でロックされる場合がある
5. **マイグレーションは展開後は不変** — 本番環境で実行されたマイグレーションを編集しない

## マイグレーション安全チェックリスト (Migration Safety Checklist)

マイグレーションを適用する前に：

- [ ] マイグレーションはUPとDOWNの両方を持つ（または明示的に不可逆としてマーク）
- [ ] 大型テーブルの完全テーブルロックなし（並行操作使用）
- [ ] 新しい列はデフォルトまたはnullable（デフォルトなしでNOT NULLを追加しない）
- [ ] インデックスは並行して作成（既存テーブルのCREATE TABLEでインライン化しない）
- [ ] データバックフィルはスキーマ変更から分離したマイグレーション
- [ ] 本番環境データのコピーに対してテスト
- [ ] ロールバック計画を文書化

## PostgreSQL パターン (PostgreSQL Patterns)

### 列を安全に追加 (Adding a Column Safely)

```sql
-- GOOD: Nullable列、ロックなし
ALTER TABLE users ADD COLUMN avatar_url TEXT;

-- GOOD: デフォルト付きの列（Postgres 11+は即座、書き直しなし）
ALTER TABLE users ADD COLUMN is_active BOOLEAN NOT NULL DEFAULT true;

-- BAD: 既存テーブルのデフォルトなしで NOT NULL（完全書き直し必須）
ALTER TABLE users ADD COLUMN is_active BOOLEAN NOT NULL;
```

詳細についてはドキュメントを参照してください。
