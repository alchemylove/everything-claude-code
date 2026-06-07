---
description: ~/.claude/session-data/ から直近の session ファイルを読み込み、前回セッション終了時点の文脈をそのまま引き継いで作業を再開する。
---

# セッション再開コマンド (Resume Session Command)

最後に保存した session state を読み込み、作業に入る前に文脈を完全に把握する。
このコマンドは `/save-session` の対になる操作である。

## 使用タイミング (When to Use)

- 前日の作業を続けるために新しいセッションを開始するとき
- context limit のため新しいセッションを立ち上げたあと
- 別ソースから session ファイルを引き継ぐとき（ファイルパスを指定するだけ）
- session ファイルがあり、作業前に Claude に完全に吸収させたいとき

## 使い方 (Usage)

```
/resume-session                                                      # loads most recent file in ~/.claude/session-data/
/resume-session 2024-01-15                                           # loads most recent session for that date
/resume-session ~/.claude/session-data/2024-01-15-abc123de-session.tmp  # loads a current short-id session file
/resume-session ~/.claude/sessions/2024-01-15-session.tmp               # loads a specific legacy-format file
```

## 手順 (Process)

### ステップ 1: session ファイルを探す (Step 1: Find the session file)

引数がない場合:

1. `~/.claude/session-data/` を確認
2. 最終更新が最も新しい `*-session.tmp` を選ぶ
3. フォルダが存在しない、または一致ファイルがない場合はユーザーに伝える:
   ```
   No session files found in ~/.claude/session-data/
   Run /save-session at the end of a session to create one.
   ```
   その後停止する。

引数がある場合:

- 日付形式（`YYYY-MM-DD`）なら、まず `~/.claude/session-data/`、次に legacy の
  `~/.claude/sessions/` で `YYYY-MM-DD-session.tmp`（legacy 形式）または
  `YYYY-MM-DD-<shortid>-session.tmp`（現行形式）に一致するファイルを検索し、
  その日付で最終更新が最も新しいものを読み込む
- ファイルパス形式なら、そのファイルを直接読む
- 見つからなければ明確に報告して停止

### ステップ 2: session ファイル全体を読む (Step 2: Read the entire session file)

ファイル全体を読む。まだ要約しない。

### ステップ 3: 理解の確認 (Step 3: Confirm understanding)

次の固定形式で構造化ブリーフィングを返す:

```
SESSION LOADED: [actual resolved path to the file]
════════════════════════════════════════════════

PROJECT: [project name / topic from file]

WHAT WE'RE BUILDING:
[2-3 sentence summary in your own words]

CURRENT STATE:
PASS: Working: [count] items confirmed
 In Progress: [list files that are in progress]
 Not Started: [list planned but untouched]

WHAT NOT TO RETRY:
[list every failed approach with its reason — this is critical]

OPEN QUESTIONS / BLOCKERS:
[list any blockers or unanswered questions]

NEXT STEP:
[exact next step if defined in the file]
[if not defined: "No next step defined — recommend reviewing 'What Has NOT Been Tried Yet' together before starting"]

════════════════════════════════════════════════
Ready to continue. What would you like to do?
```

### ステップ 4: ユーザーを待つ (Step 4: Wait for the user)

自動で作業を開始しない。ファイルに触らない。ユーザーが次に何をするか言うまで待つ。

session ファイルに next step が明確にあり、ユーザーが "continue" や "yes" などと言った場合 — その next step をそのまま実行する。

next step が未定義の場合 — どこから始めるかユーザーに尋ね、必要なら "What Has NOT Been Tried Yet" セクションから案を示す。

---

## エッジケース (Edge Cases)

**同一日付の複数セッション**（`2024-01-15-session.tmp`、`2024-01-15-abc123de-session.tmp`）:
legacy の id なし形式でも現行の short-id 形式でも、その日付で最終更新が最も新しい一致ファイルを読み込む。

**session ファイルが参照するファイルがディスクに存在しない:**
ブリーフィングで注記する — "WARNING: `path/to/file.ts` referenced in session but not found on disk."

**session ファイルが 7 日より古い:**
ギャップを注記する — "WARNING: This session is from N days ago (threshold: 7 days). Things may have changed." — その後通常どおり続行。

**ユーザーがファイルパスを直接指定（例: チームメイトから転送）:**
読み込み、同じブリーフィング手順に従う — ソースに関わらず形式は同じ。

**session ファイルが空または不正:**
報告: "Session file found but appears empty or unreadable. You may need to create a new one with /save-session."

---

## 出力例 (Example Output)

```
SESSION LOADED: /Users/you/.claude/session-data/2024-01-15-abc123de-session.tmp
════════════════════════════════════════════════

PROJECT: my-app — JWT Authentication

WHAT WE'RE BUILDING:
User authentication with JWT tokens stored in httpOnly cookies.
Register and login endpoints are partially done. Route protection
via middleware hasn't been started yet.

CURRENT STATE:
PASS: Working: 3 items (register endpoint, JWT generation, password hashing)
 In Progress: app/api/auth/login/route.ts (token works, cookie not set yet)
 Not Started: middleware.ts, app/login/page.tsx

WHAT NOT TO RETRY:
FAIL: Next-Auth — conflicts with custom Prisma adapter, threw adapter error on every request
FAIL: localStorage for JWT — causes SSR hydration mismatch, incompatible with Next.js

OPEN QUESTIONS / BLOCKERS:
- Does cookies().set() work inside a Route Handler or only Server Actions?

NEXT STEP:
In app/api/auth/login/route.ts — set the JWT as an httpOnly cookie using
cookies().set('token', jwt, { httpOnly: true, secure: true, sameSite: 'strict' })
then test with Postman for a Set-Cookie header in the response.

════════════════════════════════════════════════
Ready to continue. What would you like to do?
```

---

## 注意事項 (Notes)

- 読み込み時に session ファイルを変更しない — 読み取り専用の履歴記録
- ブリーフィング形式は固定 — 空でもセクションをスキップしない
- "What Not To Retry" は常に表示する（"None" だけでも）— 見落とし防止のため重要
- 再開後、新しいセッションの終わりに `/save-session` で新しい日付付きファイルを作り直すこともある
