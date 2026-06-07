# テスト要件 (Testing Requirements)

## 最低テストカバレッジ: 80% (Minimum Test Coverage: 80%)

テスト種別（すべて必須）:
1. **Unit Tests** - 個別の関数、ユーティリティ、コンポーネント
2. **Integration Tests** - API endpoints、データベース操作
3. **E2E Tests** - 重要なユーザーフロー（フレームワークは言語ごとに選択）

## テスト駆動開発 (Test-Driven Development)

必須ワークフロー:
1. 先にテストを書く（RED）
2. テストを実行 — FAIL すること
3. 最小実装を書く（GREEN）
4. テストを実行 — PASS すること
5. リファクタリング（IMPROVE）
6. カバレッジを検証（80%+）

## テスト失敗のトラブルシューティング (Troubleshooting Test Failures)

1. **tdd-guide** agent を使用する
2. テストの分離を確認する
3. mock が正しいか検証する
4. 実装を修正する（テストが誤りでない限り、テストは修正しない）

## Agent サポート (Agent Support)

- **tdd-guide** - 新 feature では積極的に使用し、テストファーストを強制する
