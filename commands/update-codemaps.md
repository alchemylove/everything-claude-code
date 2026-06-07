---
description: プロジェクト構造をスキャンし、トークン効率の良いアーキテクチャコードマップを生成します。
---

# コードマップの更新 (Update Codemaps)

コードベース構造を分析し、トークン効率の良いアーキテクチャドキュメントを生成します。

## ステップ 1: プロジェクト構造のスキャン (Step 1: Scan Project Structure)

1. プロジェクトタイプを特定（monorepo、単一アプリ、ライブラリ、マイクロサービス）
2. すべてのソースディレクトリを検出（src/、lib/、app/、packages/）
3. エントリポイントをマッピング（main.ts、index.ts、app.py、main.go など）

## ステップ 2: コードマップの生成 (Step 2: Generate Codemaps)

`docs/CODEMAPS/`（または `.reports/codemaps/`）にコードマップを作成または更新：

| ファイル | 内容 |
|------|----------|
| `architecture.md` | 高レベルシステム図、サービス境界、データフロー |
| `backend.md` | API ルート、ミドルウェアチェーン、service → repository マッピング |
| `frontend.md` | ページツリー、コンポーネント階層、状態管理フロー |
| `data.md` | データベーステーブル、リレーション、マイグレーション履歴 |
| `dependencies.md` | 外部サービス、サードパーティ統合、共有ライブラリ |

### コードマップ形式 (Codemap Format)

各コードマップはトークン効率重視 — AI コンテキスト消費に最適化：

```markdown
# Backend Architecture

## Routes
POST /api/users → UserController.create → UserService.create → UserRepo.insert
GET  /api/users/:id → UserController.get → UserService.findById → UserRepo.findById

## Key Files
src/services/user.ts (business logic, 120 lines)
src/repos/user.ts (database access, 80 lines)

## Dependencies
- PostgreSQL (primary data store)
- Redis (session cache, rate limiting)
- Stripe (payment processing)
```

## ステップ 3: 差分検出 (Step 3: Diff Detection)

1. 以前のコードマップが存在する場合、差分パーセンテージを計算
2. 変更が 30% を超える場合、上書き前に差分を表示しユーザー承認を要求
3. 変更が 30% 以下の場合、その場で更新

## ステップ 4: メタデータの追加 (Step 4: Add Metadata)

各コードマップに鮮度ヘッダーを追加：

```markdown
<!-- Generated: 2026-02-11 | Files scanned: 142 | Token estimate: ~800 -->
```

## ステップ 5: 分析レポートの保存 (Step 5: Save Analysis Report)

`.reports/codemap-diff.txt` にサマリーを書き込む：
- 前回スキャン以降に追加/削除/変更されたファイル
- 新しく検出された依存関係
- アーキテクチャの変更（新ルート、新サービスなど）
- 90日以上更新されていないドキュメントの鮮度警告

## ヒント (Tips)

- **高レベル構造**に焦点を当て、実装の詳細は避ける
- 完全なコードブロックより **ファイルパスと関数シグネチャ** を優先
- 効率的なコンテキスト読み込みのため、各コードマップを **1000 トークン未満** に保つ
- 冗長な説明の代わりにデータフローには ASCII 図を使用
- 大規模な機能追加やリファクタリングセッションの後に実行
