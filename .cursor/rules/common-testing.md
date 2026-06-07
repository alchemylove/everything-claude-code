---
description: "Testing requirements: 80% coverage、TDD workflow、test 種類"
alwaysApply: true
---
# Testing Requirements (Testing Requirements)

## 最小 Test Coverage: 80%

Test 種類（すべて必須）:
1. **Unit Tests** — 個別の関数、utility、component
2. **Integration Tests** — API endpoint、database 操作
3. **E2E Tests** — 重要なユーザーフロー（言語ごとに framework を選択）

## Test-Driven Development

必須 workflow:
1. まず test を書く（RED）
2. test を実行 — FAIL するはず
3. 最小実装を書く（GREEN）
4. test を実行 — PASS するはず
5. リファクタ（IMPROVE）
6. coverage を検証（80%+）

## Test 失敗のトラブルシューティング (Troubleshooting Test Failures)

1. **tdd-guide** agent を使用
2. test の分離を確認
3. mock が正しいか検証
4. test ではなく実装を修正（test が誤っている場合を除く）

## Agent サポート (Agent Support)

- **tdd-guide** — 新 feature 向けに PROACTIVELY に使用。test-first を強制
