# 共通パターン (Common Patterns)

## スケルトンプロジェクト (Skeleton Projects)

新機能を実装するとき:
1. 実戦で検証されたスケルトンプロジェクトを検索する
2. 並列 agent でオプションを評価する:
   - セキュリティ評価
   - 拡張性分析
   - 関連性スコアリング
   - 実装計画
3. 最適なものを基盤として clone する
4. 実証済みの構造内で反復する

## デザインパターン (Design Patterns)

### Repository パターン (Repository Pattern)

一貫した interface の背後にデータアクセスをカプセル化する:
- 標準操作を定義する: findAll、findById、create、update、delete
- 具象実装がストレージ詳細（database、API、file など）を扱う
- ビジネスロジックはストレージ機構ではなく抽象 interface に依存する
- データソースの差し替えを容易にし、mock によるテストを簡素化する

### API レスポンス形式 (API Response Format)

すべての API レスポンスに一貫した envelope を使用する:
- success／status インジケータを含める
- data payload を含める（エラー時は nullable）
- error message フィールドを含める（成功時は nullable）
- ページネーション付きレスポンス用の metadata（total、page、limit）を含める
