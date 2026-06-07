---
name: parallel-execution-optimizer
description: Use when the user wants a task done much faster through parallel work, concurrent agents, batched tool calls, isolated worktrees, or many independent verification lanes without losing correctness.
origin: ECC
tools: Read, Write, Edit, Bash, Grep, Glob
---

# 並列実行オプティマイザ (Parallel Execution Optimizer)

速度が独立作業の同時実行から得られるときにこの skill を使用する: repo 検査、file read、API check、browser check、build/test lane、deploy readback、multi-worktree implementation pass など。

## コアパターン (Core Pattern)

行動前に urgency を dependency graph に変換する。

1. objective と done signal を定義する。
2. 作業を lane に分割する。
3. 各 lane を parallel、sequential、gated に分類する。
4. 独立した read/check を同時実行する。
5. write は file、worktree、branch、service、dataset ごとに隔離する。
6. lane が互換である証拠が揃ってから merge する。
7. 曖昧な速度主張ではなく verification table で締める。

## Lane マトリクス (Lane Matrix)

大規模 push の前に簡潔なマトリクスを書く:

```text
Lane | Can run in parallel? | Write surface | Risk | Verification
Repo scan | yes | none | low | rg/git status outputs
Backend patch | maybe | src/api | medium | unit tests
Frontend patch | maybe | app/components | medium | browser screenshot
Deploy readback | after build | remote service | high | live URL + logs
```

write surface が衝突しないときのみ lane を parallel 実行する。

## 実行ルール (Execution Rules)

- file read、search、status check、metadata query を batch する。
- 大きく無関係な implementation lane には isolated worktree を使う。
- 長時間 test、build、backfill、deploy は別 session で開始し、意図的に poll する。
- lane が plan を変える blocker を発見したら依存 lane を止め、マトリクスを更新する。
- user が継続 service を明示要求しない限り、background process を turn を超えて残さない。
- 明示的 gate なしに destructive command、migration、同一 table への write、live customer 影響 deploy を parallel 化しない。

## 出力形式 (Output Shape)

報告時は次を使用:

```text
Parallel execution result:
- Lanes run: 5
- Lanes completed: 4
- Blocked lane: deploy readback, waiting on DNS propagation
- Fast path found: batched repo scan + focused tests
- Verification: lint pass, unit pass, live smoke pass
```

## 失敗モード (Failure Modes)

- 衝突する編集を生む過剰な concurrency
- タスクではなく tool のベンチマーク
- 正しさが証明される前の「速い＝完了」扱い
- 実行中 session の poll 忘れ
- スキップした check を成功サマリで隠す
