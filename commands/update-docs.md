---
description: scripts、schemas、routes、exports などの信頼できる情報源からドキュメントを同期します。
---

# ドキュメントの更新 (Update Documentation)

コードベースとドキュメントを同期し、信頼できる情報源から生成します。

## ステップ 1: 信頼できる情報源の特定 (Step 1: Identify Sources of Truth)

| 情報源 | 生成物 |
|--------|-----------|
| `package.json` scripts | 利用可能なコマンドリファレンス |
| `.env.example` | 環境変数ドキュメント |
| `openapi.yaml` / route files | API エンドポイントリファレンス |
| Source code exports | 公開 API ドキュメント |
| `Dockerfile` / `docker-compose.yml` | インフラセットアップドキュメント |

## ステップ 2: スクリプトリファレンスの生成 (Step 2: Generate Script Reference)

1. `package.json`（または `Makefile`、`Cargo.toml`、`pyproject.toml`）を読み取る
2. 説明付きですべての scripts/commands を抽出
3. リファレンステーブルを生成：

```markdown
| Command | Description |
|---------|-------------|
| `npm run dev` | ホットリロード付きで開発サーバーを起動 |
| `npm run build` | 型チェック付きの本番ビルド |
| `npm test` | カバレッジ付きでテストスイートを実行 |
```

## ステップ 3: 環境ドキュメントの生成 (Step 3: Generate Environment Documentation)

1. `.env.example`（または `.env.template`、`.env.sample`）を読み取る
2. 目的付きですべての変数を抽出
3. 必須とオプションに分類
4. 期待されるフォーマットと有効な値を文書化

```markdown
| Variable | Required | Description | Example |
|----------|----------|-------------|---------|
| `DATABASE_URL` | Yes | PostgreSQL 接続文字列 | `postgres://user:pass@host:5432/db` |
| `LOG_LEVEL` | No | ログの詳細度（デフォルト: info） | `debug`, `info`, `warn`, `error` |
```

## ステップ 4: コントリビューションガイドの更新 (Step 4: Update Contributing Guide)

以下を含む `docs/CONTRIBUTING.md` を生成または更新：
- 開発環境セットアップ（前提条件、インストール手順）
- 利用可能なスクリプトとその目的
- テスト手順（実行方法、新規テストの書き方）
- コードスタイルの強制（linter、formatter、pre-commit hooks）
- PR 提出チェックリスト

## ステップ 5: ランブックの更新 (Step 5: Update Runbook)

以下を含む `docs/RUNBOOK.md` を生成または更新：
- デプロイ手順（ステップバイステップ）
- ヘルスチェックエンドポイントとモニタリング
- よくある問題とその修正
- ロールバック手順
- アラートとエスカレーションパス

## ステップ 6: 鮮度チェック (Step 6: Staleness Check)

1. 90日以上変更されていないドキュメントファイルを検出
2. 最近のソースコード変更と照合
3. 手動レビュー用に古くなっている可能性のあるドキュメントをフラグ

## ステップ 7: サマリーの表示 (Step 7: Show Summary)

```
Documentation Update
──────────────────────────────
Updated:  docs/CONTRIBUTING.md (scripts table)
Updated:  docs/ENV.md (3 new variables)
Flagged:  docs/DEPLOY.md (142 days stale)
Skipped:  docs/API.md (no changes detected)
──────────────────────────────
```

## ルール (Rules)

- **単一の信頼できる情報源**: 常にコードから生成し、生成セクションを手動編集しない
- **手動セクションの保持**: 生成セクションのみ更新し、手書きの文章はそのまま残す
- **生成コンテンツのマーク**: 生成セクションの周囲に `<!-- AUTO-GENERATED -->` マーカーを使用
- **未指示のドキュメント作成禁止**: コマンドが明示的に要求した場合のみ新規ドキュメントファイルを作成
