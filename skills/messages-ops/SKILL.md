---
name: messages-ops
description: Evidence-first live messaging workflow for ECC. Use when the user wants to read texts or DMs, recover a recent one-time code, inspect a thread before replying, or prove which message source was actually checked.
origin: ECC
---

# メッセージ運用 (Messages Ops)

タスクがライブメッセージ取得（iMessage、DM、最近のワンタイムコード、フォローアップ前のスレッド検査）のときに使用する。

これはメール作業ではない。主なサーフェスがメールボックスなら `email-ops` を使用する。

## スキルスタック (Skill Stack)

関連する場合はこれらの ECC ネイティブスキルをワークフローに取り込む:

- メッセージタスクが実質メールボックス作業の場合は `email-ops`
- DM スレッドがアウトバウンドネットワーク作業に属する場合は `connections-optimizer`
- ライブスレッドがターゲティングやウォームパスアウトリーチに資する場合は `lead-intelligence`
- スレッド内容を永続コンテキストに取り込む必要がある場合は `knowledge-ops`

## 使用タイミング (When to Use)

- ユーザーが「メッセージを読んで」「テキストを確認」「DM を見て」「コードを探して」と言うとき
- タスクがライブスレッドまたはローカルメッセージングサーフェスに届いた最近のコードに依存するとき
- どのソースまたはスレッドを検査したかの証明が欲しいとき

## ガードレール (Guardrails)

- ソースを先に解決する:
  - ローカルメッセージ
  - X / ソーシャル DM
  - 別のブラウザゲート付きメッセージサーフェス
- ソースを名指しせずにスレッドを確認したと主張しない
- 確認済みヘルパーや標準パスがある場合、生の DB アクセスを即興で行わない
- 認証や MFA がサーフェスをブロックする場合、正確なブロッカーを報告する

## ワークフロー (Workflow)

### 1. 正確なスレッドを解決する (1. Resolve the exact thread)

他の作業の前に次を確定する:

- メッセージサーフェス
- 送信者 / 受信者 / サービス
- 時間窓
- タスクが取得、検査、返信準備のいずれか

### 2. 草案の前に読む (2. Read before drafting)

タスクがアウトバウンドフォローアップになり得る場合:

- 最新の受信を読む
- オープンループを特定する
- 必要なら正しいアウトバウンドスキルへ引き渡す

### 3. コードを集中取得タスクとして扱う (3. Handle codes as a focused retrieval task)

ワンタイムコード向け:

- まず最近のローカルメッセージ窓を検索する
- 可能ならサービスまたは送信者で絞る
- コードが見つかるか、集中検索が尽きたら停止する

### 4. 正確な証拠を報告する (4. Report exact evidence)

返すもの:

- 使用したソース
- 可能ならスレッドまたは送信者
- 時間窓
- 正確なステータス:
  - read
  - code-found
  - blocked
  - awaiting reply draft

## 出力形式 (Output Format)

```text
SOURCE
- message surface
- sender / thread / service

RESULT
- message summary or code
- time window

STATUS
- read / code-found / blocked / awaiting reply draft
```

## 落とし穴 (Pitfalls)

- メールボックス作業と DM/テキスト作業を混同しない
- ソースを名指しせずに取得したと主張しない
- 最近のコード検索の依頼なのに広い検索に時間を使わない
- ブロッカーを表に出さずにブロックされた認証パスをリトライし続けない

## 検証 (Verification)

- レスポンスがメッセージソースを名指ししている
- レスポンスに送信者、サービス、スレッド、または明確なブロッカーが含まれる
- 最終状態が明示的で境界がはっきりしている
