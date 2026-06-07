# セキュリティガイドライン (Security Guidelines)

## 必須セキュリティチェック (Mandatory Security Checks)

コミット前に必ず実施:
- [ ] ハードコードされた secret なし（API keys、passwords、tokens）
- [ ] すべてのユーザー入力を検証済み
- [ ] SQL injection 対策（パラメータ化クエリ）
- [ ] XSS 対策（サニタイズ済み HTML）
- [ ] CSRF 保護が有効
- [ ] 認証・認可を検証済み
- [ ] すべての endpoint に rate limiting
- [ ] エラーメッセージが機微データを漏洩しない

## Secret 管理 (Secret Management)

- ソースコードに secret をハードコードしない
- 環境変数または secret manager を常に使用する
- 起動時に必須 secret の存在を検証する
- 漏洩の可能性がある secret はローテーションする

## セキュリティ対応プロトコル (Security Response Protocol)

セキュリティ問題が見つかった場合:
1. 直ちに STOP する
2. **security-reviewer** agent を使用する
3. CRITICAL な問題を修正してから続行する
4. 漏洩した secret をローテーションする
5. コードベース全体で同様の問題をレビューする
