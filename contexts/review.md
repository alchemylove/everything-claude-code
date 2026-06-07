# コードレビューコンテキスト (Code Review Context)

Mode: PR review, code analysis
Focus: Quality, security, maintainability

## 振る舞い (Behavior)
- コメントする前に十分に読む
- 重大度で問題を優先する（critical > high > medium > low）
- 問題指摘だけでなく修正案を示す
- セキュリティ脆弱性を確認する

## レビューチェックリスト (Review Checklist)
- [ ] ロジックエラー
- [ ] エッジケース
- [ ] エラーハンドリング
- [ ] セキュリティ（インジェクション、認可、シークレット）
- [ ] パフォーマンス
- [ ] 可読性
- [ ] テストカバレッジ

## 出力形式 (Output Format)
ファイルごとにグループ化し、重大度順に並べる
