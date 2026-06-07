---
description: プロジェクトのビルドシステムを検出し、最小限の安全な変更でビルド/型エラーを段階的に修正する。
---

# ビルドと修正 (Build and Fix)

最小限の安全な変更でビルドと型エラーを段階的に修正する。

## ステップ 1: ビルドシステムの検出 (Step 1: Detect Build System)

プロジェクトのビルドツールを特定し、ビルドを実行する:

| Indicator | Build Command |
|-----------|---------------|
| `package.json` with `build` script | `npm run build` or `pnpm build` |
| `tsconfig.json` (TypeScript only) | `npx tsc --noEmit` |
| `Cargo.toml` | `cargo build 2>&1` |
| `pom.xml` | `mvn compile` |
| `build.gradle` | `./gradlew compileJava` |
| `go.mod` | `go build ./...` |
| `pyproject.toml` | `python -m compileall -q .` or `mypy .` |

## ステップ 2: エラーの解析とグループ化 (Step 2: Parse and Group Errors)

1. ビルドコマンドを実行し stderr をキャプチャする
2. ファイルパスでエラーをグループ化する
3. 依存順でソートする（ロジックエラーより先に import/型を修正）
4. 進捗追跡のため総エラー数を数える

## ステップ 3: 修正ループ（1エラーずつ） (Step 3: Fix Loop (One Error at a Time))

各エラーについて:

1. **ファイルを読む** — Read ツールでエラー周辺のコンテキスト（エラー前後10行）を確認する
2. **診断** — 根本原因を特定する（不足 import、型の誤り、構文エラー）
3. **最小限で修正** — Edit ツールでエラーを解消する最小の変更を行う
4. **ビルドを再実行** — エラーが消え、新規エラーが入っていないことを確認する
5. **次へ** — 残りのエラーを続ける

## ステップ 4: ガードレール (Step 4: Guardrails)

次の場合は停止してユーザーに確認する:
- 修正が**解消したより多くのエラーを導入**した場合
- **同じエラーが3回試行後も残る**場合（より深い問題の可能性）
- 修正に**アーキテクチャ変更**が必要な場合（ビルド修正だけではない）
- ビルドエラーが**不足依存関係**に起因する場合（`npm install`、`cargo add` 等が必要）

## ステップ 5: サマリー (Step 5: Summary)

結果を表示する:
- 修正したエラー（ファイルパス付き）
- 残っているエラー（あれば）
- 新たに導入したエラー（ゼロであるべき）
- 未解決問題への推奨次ステップ

## リカバリ戦略 (Recovery Strategies)

| Situation | Action |
|-----------|--------|
| Missing module/import | パッケージがインストール済みか確認し、install コマンドを提案する |
| Type mismatch | 両方の型定義を読み、より狭い型を修正する |
| Circular dependency | import グラフでサイクルを特定し、抽出を提案する |
| Version conflict | `package.json` / `Cargo.toml` のバージョン制約を確認する |
| Build tool misconfiguration | 設定ファイルを読み、動作するデフォルトと比較する |

安全のため1エラーずつ修正する。リファクタリングより最小 diff を優先する。
