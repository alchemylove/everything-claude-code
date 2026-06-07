---
description: 現在のタスクを中断したりコンテキストを失ったりせず、横道の質問に素早く答える。回答後は自動的に作業を再開する。
---

# Aside コマンド (Aside Command)

タスクの途中で質問し、即座に的確な回答を得て — そのまま中断した地点から作業を続ける。現在のタスク、ファイル、コンテキストは一切変更しない。

## 使用するタイミング (When to Use)

- Claude が作業中に気になったことを、勢いを失わずに聞きたいとき
- Claude が現在編集中のコードについて素早い説明が必要なとき
- タスクを脱線させずに、判断について第二の意見や確認が欲しいとき
- Claude が先に進む前に、エラー・概念・パターンを理解したいとき
- 現在のタスクとは無関係なことを、新しいセッションを始めずに聞きたいとき

## 使い方 (Usage)

```
/aside <your question>
/aside what does this function actually return?
/aside is this pattern thread-safe?
/aside why are we using X instead of Y here?
/aside what's the difference between foo() and bar()?
/aside should we be worried about the N+1 query we just added?
```

## プロセス (Process)

### ステップ 1: 現在のタスク状態を凍結する (Step 1: Freeze the current task state)

何かに答える前に、心の中で次を記録する:
- アクティブなタスクは何か？（どのファイル、機能、問題に取り組んでいたか）
- `/aside` が呼ばれた瞬間、どのステップが進行中だったか
- 次に何が起こる予定だったか

aside 中はファイルの編集・作成・削除を一切行わない。

### ステップ 2: 質問に直接答える (Step 2: Answer the question directly)

完全で有用な最短の形で質問に答える。

- 推論ではなく、答えから始める
- 短く — 詳しい説明が必要なら、タスク完了後に深掘りを提案する
- 現在のファイルやコードに関する質問なら、正確に参照する（必要ならファイルパスと行番号）
- 回答にファイル読み取りが必要なら読む — ただし読み取りのみ、書き込みはしない

応答形式:

```
ASIDE: [質問を簡潔に言い換え]

[ここに回答]

— Back to task: [何をしていたかの一行説明]
```

### ステップ 3: メインタスクを再開する (Step 3: Resume the main task)

回答を出したら、中断した地点から直ちにアクティブなタスクを続行する。aside の回答がブロッカーや現在のアプローチの見直し理由を示した場合を除き（エッジケース参照）、再開の許可は求めない。

---

## エッジケース (Edge Cases)

**質問が未指定（`/aside` のみ）:**
次のように応答:
```
ASIDE: no question provided

What would you like to know? (ask your question and I'll answer without losing the current task context)

— Back to task: [何をしていたかの一行説明]
```

**質問が現在のタスクに潜在的な問題を示す場合:**
再開前に明確にフラグを立てる:
```
ASIDE: [回答]

WARNING: Note: This answer suggests [issue] with the current approach. Want to address this before continuing, or proceed as planned?
```
ユーザーの判断を待ってから再開する。

**質問が実はタスクの方向転換である場合（横道の質問ではない）:**
構築内容の変更を示唆する質問（例: `/aside actually, let's use Redis instead`）なら、次のように確認:
```
ASIDE: That sounds like a direction change, not just a side question.
Do you want to:
  (a) Answer this as information only and keep the current plan
  (b) Pause the current task and change approach
```
ユーザーの回答を待つ — 推測で進めない。

**質問が現在開いているファイルやコードに関する場合:**
ライブコンテキストから答える。セッション中に既に読んでいれば直接参照する。未読なら今読む（読み取りのみ）し、file:line で答える。

**アクティブなタスクがない場合（`/aside` 呼び出し時に進行中の作業がない）:**
応答形式を統一するため標準ラッパーを使う:
```
ASIDE: [質問を簡潔に言い換え]

[ここに回答]

— Back to task: no active task to resume
```

**長い回答が必要な質問:**
要点を簡潔に答え、次を提案:
```
That's the short version. Want a deeper explanation after we finish [current task]?
```

**`/aside` を連続で複数回:**
順にそれぞれ答える。最後の回答の後、メインタスクを再開する。aside の連鎖でもタスク状態を失わない。

**aside の回答がコード変更が必要であることを示す場合:**
必要な変更を記録するが、aside 中は行わない:
```
ASIDE: [回答]

 Worth fixing: [何を変更すべきか]. I'll flag this after the current task unless you want to address it now.
```

**質問が曖昧または漠然としている場合:**
回答に必要な情報を得るための最短の確認質問を1つだけ行う。複数の質問はしない。

---

## 出力例 (Example Output)

```
User: /aside what does fetchWithRetry() actually do?

ASIDE: what does fetchWithRetry() do?

fetchWithRetry() (src/api/retry.ts:12) attempts the request up to 3 times with
exponential backoff (250ms → 500ms → 1s). It only retries on 5xx responses and
network errors — 4xx errors are treated as final and not retried.

— Back to task: refactoring the auth middleware in src/middleware/auth.ts
```

```
User: /aside is the approach we're taking thread-safe?

ASIDE: is the current approach thread-safe?

No — the shared cache object in src/cache/store.ts:34 is mutated without locking.
Under concurrent requests this is a race condition. It's low risk in a single-process
Node.js server but would be a real problem with worker threads or clustering.

WARNING: Note: This could affect the feature we're building. Want to address this now or continue and fix it in a follow-up?
```

---

## 注意事項 (Notes)

- aside 中はファイルを変更しない — 読み取り専用のみ
- aside は会話の一時停止であり、新しいタスクではない — 元のタスクは必ず再開する
- 回答は焦点を絞る: 目的はユーザーを素早く unblock することであり、講義ではない
- aside が大きな議論につながっても、ブロッカーが明らかでない限り現在のタスクを先に完了する
- aside はタスク結果に明示的に関連しない限りセッションファイルに保存しない
