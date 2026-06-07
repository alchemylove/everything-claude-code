---
description: 各変更後に検証しながら、dead code を安全に特定・削除する。
---

# リファクタとクリーンアップ (Refactor Clean)

各ステップでテスト検証を行いながら、dead code を安全に特定・削除する。

## ステップ 1: Dead code の検出 (Step 1: Detect Dead Code)

プロジェクト種別に応じた分析ツールを実行する:

| Tool | What It Finds | Command |
|------|--------------|---------|
| knip | Unused exports, files, dependencies | `npx knip` |
| depcheck | Unused npm dependencies | `npx depcheck` |
| ts-prune | Unused TypeScript exports | `npx ts-prune` |
| vulture | Unused Python code | `vulture src/` |
| deadcode | Unused Go code | `deadcode ./...` |
| cargo-udeps | Unused Rust dependencies | `cargo +nightly udeps` |

ツールがない場合は Grep で import がゼロの export を探す:
```
# Find exports, then check if they're imported anywhere
```

## ステップ 2: 所見の分類 (Step 2: Categorize Findings)

所見を安全度 tier に分類する:

| Tier | Examples | Action |
|------|----------|--------|
| **SAFE** | Unused utilities, test helpers, internal functions | Delete with confidence |
| **CAUTION** | Components, API routes, middleware | Verify no dynamic imports or external consumers |
| **DANGER** | Config files, entry points, type definitions | Investigate before touching |

## ステップ 3: 安全な削除ループ (Step 3: Safe Deletion Loop)

各 SAFE 項目について:

1. **フルテストスイートを実行** — ベースラインを確立（すべて green）
2. **dead code を削除** — Edit tool で surgical に除去
3. **テストスイートを再実行** — 何も壊れていないことを確認
4. **テストが失敗した場合** — 直ちに `git checkout -- <file>` で revert し、この項目をスキップ
5. **テストが通った場合** — 次の項目へ

## ステップ 4: CAUTION 項目の扱い (Step 4: Handle CAUTION Items)

CAUTION 項目を削除する前に:
- dynamic import を検索: `import()`、`require()`、`__import__`
- 文字列参照を検索: config 内の route 名、component 名
- public package API から export されていないか確認
- 外部 consumer がないか確認（公開パッケージなら dependents を確認）

## ステップ 5: 重複の統合 (Step 5: Consolidate Duplicates)

dead code 削除後、次を探す:
- ほぼ重複する関数（類似度 >80%）— 1 つに統合
- 冗長な型定義 — 統合
- 価値を足さない wrapper 関数 — inline 化
- 目的のない re-export — 間接参照を除去

## ステップ 6: サマリー (Step 6: Summary)

結果を報告する:

```
Dead Code Cleanup
──────────────────────────────
Deleted:   12 unused functions
           3 unused files
           5 unused dependencies
Skipped:   2 items (tests failed)
Saved:     ~450 lines removed
──────────────────────────────
All tests passing PASS:
```

## ルール (Rules)

- **テスト実行なしに削除しない**
- **1 件ずつ削除** — 原子的変更で rollback が容易
- **不明ならスキップ** — dead code を残す方が本番を壊すよりまし
- **クリーン中にリファクタしない** — 関心を分離（先に clean、後で refactor）
