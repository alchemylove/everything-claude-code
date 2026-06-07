---
description: 現在のセッション状態を日付付きファイルとして ~/.claude/session-data/ に保存し、完全なコンテキストを持った状態で将来のセッションで作業を再開できるようにします。
---

# セッション保存コマンド (Save Session Command)

このセッションで起こったすべてのこと（何を構築したか、何がうまくいったか、何が失敗したか、何が残っているか）をキャプチャし、日付付きファイルに書き込みます。これにより、次のセッションはこのセッションが中断した場所から正確に再開できます。

## 使用するタイミング (When to Use)

- Claude Code を閉じる前の作業セッションの終了時
- コンテキスト制限に達する前（まずこれを実行し、その後新しいセッションを開始する）
- 記憶しておきたい複雑な問題を解決した後
- 将来のセッションにコンテキストを引き継ぐ必要がある任意のタイミング

## プロセス (Process)

### ステップ 1: コンテキストの収集 (Step 1: Gather context)

ファイルを書き込む前に、以下を収集します：

- このセッション中に変更されたすべてのファイルを確認する（git diff を使用するか、会話から思い出す）
- 議論した内容、試みた内容、決定した内容を振り返る
- 発生したエラーとその解決方法（または未解決の場合はその旨）を記録する
- 関連がある場合、現在のテスト/ビルドの状態を確認する

### ステップ 2: セッションフォルダが存在しない場合は作成する (Step 2: Create the sessions folder if it doesn't exist)

ユーザーの Claude ホームディレクトリに正規のセッションフォルダを作成します：

```bash
mkdir -p ~/.claude/session-data
```

### ステップ 3: セッションファイルを書き込む (Step 3: Write the session file)

`~/.claude/session-data/YYYY-MM-DD-<short-id>-session.tmp` を作成します。今日の実際の日付と、`session-manager.js` の `SESSION_FILENAME_REGEX` が適用するルールを満たす short-id を使用します：

- 互換性のある文字: 英字 `a-z` / `A-Z`、数字 `0-9`、ハイフン `-`、アンダースコア `_`
- 互換性のある最小長: 1文字
- 新規ファイルの推奨スタイル: 小文字、数字、ハイフンで8文字以上（衝突を避けるため）

有効な例: `abc123de`、`a1b2c3d4`、`frontend-worktree-1`、`ChezMoi_2`
新規ファイルでは避けるべき例: `A`、`test_id1`、`ABC123de`

完全な有効ファイル名の例: `2024-01-15-abc123de-session.tmp`

レガシーファイル名 `YYYY-MM-DD-session.tmp` も引き続き有効ですが、新しいセッションファイルは同日の衝突を避けるため short-id 形式を推奨します。

### ステップ 4: 以下のすべてのセクションをファイルに記入する (Step 4: Populate the file with all sections below)

すべてのセクションを正直に記入してください。セクションをスキップしないでください。セクションに本当に内容がない場合は「Nothing yet」または「N/A」と記入してください。不完全なファイルは、正直な空のセクションよりも悪い結果をもたらします。

### ステップ 5: ファイルをユーザーに表示する (Step 5: Show the file to the user)

書き込み後、完全な内容を表示し、以下のように尋ねます：

```
Session saved to [actual resolved path to the session file]

Does this look accurate? Anything to correct or add before we close?
```

確認を待ちます。要求があれば編集します。

---

## セッションファイル形式 (Session File Format)

```markdown
# Session: YYYY-MM-DD

**Started:** [approximate time if known]
**Last Updated:** [current time]
**Project:** [project name or path]
**Topic:** [one-line summary of what this session was about]

---

## What We Are Building

[1-3 paragraphs describing the feature, bug fix, or task. Include enough
context that someone with zero memory of this session can understand the goal.
Include: what it does, why it's needed, how it fits into the larger system.]

---

## What WORKED (with evidence)

[List only things that are confirmed working. For each item include WHY you
know it works — test passed, ran in browser, Postman returned 200, etc.
Without evidence, move it to "Not Tried Yet" instead.]

- **[thing that works]** — confirmed by: [specific evidence]
- **[thing that works]** — confirmed by: [specific evidence]

If nothing is confirmed working yet: "Nothing confirmed working yet — all approaches still in progress or untested."

---

## What Did NOT Work (and why)

[This is the most important section. List every approach tried that failed.
For each failure write the EXACT reason so the next session doesn't retry it.
Be specific: "threw X error because Y" is useful. "didn't work" is not.]

- **[approach tried]** — failed because: [exact reason / error message]
- **[approach tried]** — failed because: [exact reason / error message]

If nothing failed: "No failed approaches yet."

---

## What Has NOT Been Tried Yet

[Approaches that seem promising but haven't been attempted. Ideas from the
conversation. Alternative solutions worth exploring. Be specific enough that
the next session knows exactly what to try.]

- [approach / idea]
- [approach / idea]

If nothing is queued: "No specific untried approaches identified."

---

## Current State of Files

[Every file touched this session. Be precise about what state each file is in.]

| File              | Status         | Notes                      |
| ----------------- | -------------- | -------------------------- |
| `path/to/file.ts` | PASS: Complete    | [what it does]             |
| `path/to/file.ts` |  In Progress | [what's done, what's left] |
| `path/to/file.ts` | FAIL: Broken      | [what's wrong]             |
| `path/to/file.ts` |  Not Started | [planned but not touched]  |

If no files were touched: "No files modified this session."

---

## Decisions Made

[Architecture choices, tradeoffs accepted, approaches chosen and why.
These prevent the next session from relitigating settled decisions.]

- **[decision]** — reason: [why this was chosen over alternatives]

If no significant decisions: "No major decisions made this session."

---

## Blockers & Open Questions

[Anything unresolved that the next session needs to address or investigate.
Questions that came up but weren't answered. External dependencies waiting on.]

- [blocker / open question]

If none: "No active blockers."

---

## Exact Next Step

[If known: The single most important thing to do when resuming. Be precise
enough that resuming requires zero thinking about where to start.]

[If not known: "Next step not determined — review 'What Has NOT Been Tried Yet'
and 'Blockers' sections to decide on direction before starting."]

---

## Environment & Setup Notes

[Only fill this if relevant — commands needed to run the project, env vars
required, services that need to be running, etc. Skip if standard setup.]

[If none: omit this section entirely.]
```

---

## 出力例 (Example Output)

```markdown
# Session: 2024-01-15

**Started:** ~2pm
**Last Updated:** 5:30pm
**Project:** my-app
**Topic:** httpOnly クッキーによる JWT 認証の構築

---

## What We Are Building

Next.js アプリのユーザー認証システム。ユーザーはメールアドレスとパスワードで登録し、
httpOnly クッキー（localStorage ではなく）に保存された JWT を受け取り、保護されたルートは
ミドルウェアを通じて有効なトークンを確認します。目標は、トークンを JavaScript に
公開することなく、ブラウザのリフレッシュ間でセッションの永続性を実現することです。

---

## What WORKED (with evidence)

- **`/api/auth/register` endpoint** — confirmed by: Postman POST がユーザーオブジェクト付きの 200 を返し、
  Supabase ダッシュボードで行が確認でき、bcrypt ハッシュが正しく保存されている
- **JWT generation in `lib/auth.ts`** — confirmed by: ユニットテストが通る
  （`npm test -- auth.test.ts`）、jwt.io でデコードしたトークンが正しいペイロードを表示
- **Password hashing** — confirmed by: テストで `bcrypt.compare()` が true を返す

---

## What Did NOT Work (and why)

- **Next-Auth library** — failed because: カスタム Prisma アダプターと競合し、
  すべてのリクエストで "Cannot use adapter with credentials provider in this configuration" をスロー。
  デバッグする価値なし — 我々のセットアップには意見が強すぎる。
- **Storing JWT in localStorage** — failed because: SSR レンダリングは localStorage が利用可能になる前に行われ、
  すべてのページ読み込みで React のハイドレーション不一致エラーが発生。
  このアプローチは Next.js SSR と根本的に互換性がない。

---

## What Has NOT Been Tried Yet

- ログインルートのレスポンスで JWT を httpOnly クッキーとして保存する（最も可能性の高いソリューション）
- `next/headers` の `cookies()` を使用してサーバーコンポーネントでトークンを読み取る
- クッキーの存在を確認してルートを保護する middleware.ts を書く

---

## Current State of Files

| File                             | Status         | Notes                                           |
| -------------------------------- | -------------- | ----------------------------------------------- |
| `app/api/auth/register/route.ts` | PASS: Complete    | 動作確認済み、テスト済み                                   |
| `app/api/auth/login/route.ts`    |  In Progress | トークンは生成されるがクッキーの設定はまだ      |
| `lib/auth.ts`                    | PASS: Complete    | JWT ヘルパー、すべてテスト済み                         |
| `middleware.ts`                  |  Not Started | ルート保護、クッキー読み取りロジックが先に必要 |
| `app/login/page.tsx`             |  Not Started | UI は未着手                                  |

---

## Decisions Made

- **httpOnly cookie over localStorage** — reason: XSS トークン盗難を防ぎ、SSR で動作する
- **Custom auth over Next-Auth** — reason: Next-Auth が Prisma セットアップと競合し、闘う価値がない

---

## Blockers & Open Questions

- `cookies().set()` は Route Handler 内で動作するのか、それとも Server Actions 内でのみか？確認が必要。

---

## Exact Next Step

`app/api/auth/login/route.ts` で、JWT を生成した後、
`cookies().set('token', jwt, { httpOnly: true, secure: true, sameSite: 'strict' })` を使用して
httpOnly クッキーとして設定する。
その後 Postman でテスト — レスポンスに `Set-Cookie` ヘッダーが含まれるはず。
```

---

## 注意事項 (Notes)

- 各セッションには独自のファイルが割り当てられます — 以前のセッションのファイルに追記しないでください
- 「What Did NOT Work」セクションが最も重要です — このセクションがないと、将来のセッションは失敗したアプローチを盲目的に再試行します
- ユーザーがセッション途中（終了時ではなく）での保存を求めた場合、現時点でわかっていることを保存し、進行中の項目を明確にマークしてください
- このファイルは次のセッションの開始時に `/resume-session` を通じて Claude に読み込まれることを目的としています
- 正規のグローバルセッションストアを使用してください: `~/.claude/session-data/`
- 新しいセッションファイルには short-id ファイル名形式（`YYYY-MM-DD-<short-id>-session.tmp`）を推奨します
