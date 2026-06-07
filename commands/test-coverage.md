---
description: カバレッジを分析し、不足を特定し、目標閾値に向けて不足しているテストを生成します。
---

# テストカバレッジ (Test Coverage)

テストカバレッジを分析し、不足を特定し、80%+ のカバレッジに向けて不足しているテストを生成します。

## ステップ 1: テストフレームワークの検出 (Step 1: Detect Test Framework)

| 指標 | カバレッジコマンド |
|-----------|-----------------|
| `jest.config.*` または `package.json` jest | `npx jest --coverage --coverageReporters=json-summary` |
| `vitest.config.*` | `npx vitest run --coverage` |
| `pytest.ini` / `pyproject.toml` pytest | `pytest --cov=src --cov-report=json` |
| `Cargo.toml` | `cargo llvm-cov --json` |
| JaCoCo 付き `pom.xml` | `mvn test jacoco:report` |
| `go.mod` | `go test -coverprofile=coverage.out ./...` |

## ステップ 2: カバレッジレポートの分析 (Step 2: Analyze Coverage Report)

1. カバレッジコマンドを実行
2. 出力を解析（JSON サマリーまたはターミナル出力）
3. **80% 未満のカバレッジ**のファイルを、最も低い順にリスト
4. 各カバレッジ不足ファイルについて以下を特定：
   - テストされていない関数またはメソッド
   - 不足している分岐カバレッジ（if/else、switch、エラーパス）
   - 分母を膨らませるデッドコード

## ステップ 3: 不足テストの生成 (Step 3: Generate Missing Tests)

各カバレッジ不足ファイルについて、以下の優先順位でテストを生成：

1. **Happy path** — 有効な入力によるコア機能
2. **Error handling** — 無効な入力、欠落データ、ネットワーク障害
3. **Edge cases** — 空配列、null/undefined、境界値（0、-1、MAX_INT）
4. **Branch coverage** — 各 if/else、switch case、三項演算子

### テスト生成ルール (Test Generation Rules)

- ソースの隣にテストを配置: `foo.ts` → `foo.test.ts`（またはプロジェクトの慣習）
- プロジェクトの既存テストパターンを使用（import スタイル、アサーションライブラリ、モック手法）
- 外部依存（データベース、API、ファイルシステム）をモック
- 各テストは独立していること — テスト間で共有される可変状態を持たない
- テスト名は説明的に: `test_create_user_with_duplicate_email_returns_409`

## ステップ 4: 検証 (Step 4: Verify)

1. フルテストスイートを実行 — すべてのテストが通ること
2. カバレッジを再実行 — 改善を確認
3. まだ 80% 未満の場合、残りの不足に対してステップ 3 を繰り返す

## ステップ 5: レポート (Step 5: Report)

前後の比較を表示：

```
Coverage Report
──────────────────────────────
File                   Before  After
src/services/auth.ts   45%     88%
src/utils/validation.ts 32%    82%
──────────────────────────────
Overall:               67%     84%  PASS:
```

## 重点領域 (Focus Areas)

- 複雑な分岐を持つ関数（サイクロマティック複雑度が高い）
- エラーハンドラーと catch ブロック
- コードベース全体で使用されるユーティリティ関数
- API エンドポイントハンドラー（リクエスト → レスポンスのフロー）
- エッジケース: null、undefined、空文字列、空配列、ゼロ、負の数
