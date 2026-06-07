---
description: "Common patterns: repository、API response、skeleton projects"
alwaysApply: true
---
# Common Patterns (Common Patterns)

## Skeleton Projects

新機能を実装するとき:
1. 実績のある skeleton project を検索
2. 並列 agent で選択肢を評価:
   - Security assessment
   - Extensibility analysis
   - Relevance scoring
   - Implementation planning
3. 最適なものを clone して基盤にする
4. 実証済みの構造内で反復

## デザインパターン (Design Patterns)

### Repository Pattern

データアクセスを一貫した interface の背後にカプセル化:
- 標準操作を定義: findAll, findById, create, update, delete
- 具象実装がストレージ詳細（database、API、file など）を処理
- ビジネスロジックはストレージ機構ではなく抽象 interface に依存
- データソースの差し替えを容易にし、mock での test を簡素化

### API Response 形式 (API Response Format)

すべての API レスポンスに一貫した envelope を使用:
- success/status インジケータを含める
- data payload を含める（エラー時は nullable）
- error message フィールドを含める（成功時は nullable）
- ページネーション付きレスポンス用の metadata（total, page, limit）を含める
