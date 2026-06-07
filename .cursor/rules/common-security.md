---
description: "Security: 必須チェック、secret 管理、対応プロトコル"
alwaysApply: true
---
# Security Guidelines (Security Guidelines)

## 必須セキュリティチェック (Mandatory Security Checks)

commit 前に必ず:
- [ ] ハードコードされた secret がない（API key、password、token）
- [ ] すべてのユーザー入力がバリデーション済み
- [ ] SQL injection 防止（parameterized query）
- [ ] XSS 防止（sanitized HTML）
- [ ] CSRF 保護が有効
- [ ] Authentication/authorization が検証済み
- [ ] すべての endpoint に rate limiting
- [ ] エラーメッセージが機微データを漏らさない

## Secret 管理 (Secret Management)

- ソースコードに secret をハードコードしない
- 常に環境変数または secret manager を使用
- 起動時に必要な secret が存在することをバリデーション
- 露出した可能性のある secret はローテーション

## セキュリティ対応プロトコル (Security Response Protocol)

セキュリティ issue が見つかった場合:
1. 直ちに STOP
2. **security-reviewer** agent を使用
3. 続行前に CRITICAL issue を修正
4. 露出した secret をローテーション
5. 同様の issue がないか codebase 全体をレビュー
