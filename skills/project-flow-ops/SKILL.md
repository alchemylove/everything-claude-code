---
name: project-flow-ops
description: Operate execution flow across GitHub and Linear by triaging issues and pull requests, linking active work, and keeping GitHub public-facing while Linear remains the internal execution layer. Use when the user wants backlog control, PR triage, or GitHub-to-Linear coordination.
origin: ECC
---

# Project Flow Ops (Project Flow Ops)

この skill はバラバラな GitHub issue、PR、Linear task を一つの execution flow にまとめる。

問題が coordination であり coding でないときに使用する。

## 使用タイミング (When to Use)

- オープン PR または issue backlog の triage
- Linear に置くべきものと GitHub のみに残すものの判断
- active GitHub work を internal execution lane にリンク
- PR を merge、port/rebuild、close、park に分類
- review comment、CI failure、stale issue が execution をブロックしていないか監査

## 運用モデル (Operating Model)

- **GitHub** は public と community の truth
- **Linear** は active scheduled work の internal execution truth
- すべての GitHub issue に Linear issue は不要
- Linear を create/update するのは work が次のときのみ:
  - active
  - delegated
  - scheduled
  - cross-functional
  - internal tracking に十分重要

## コアワークフロー (Core Workflow)

### 1. まず public surface を読む

収集:

- GitHub issue または PR state
- author と branch status
- review comment
- CI status
- linked issue

### 2. work を分類

各 item は次のいずれかで終わる:

| State | Meaning |
|-------|---------|
| Merge | self-contained、policy 準拠、ready |
| Port/Rebuild | 有用な idea だが ECC 内で手動 re-land すべき |
| Close | 方向性が誤り、stale、unsafe、duplicate |
| Park | 有用かもしれないが今は未スケジュール |

### 3. Linear が必要か判断

次のときのみ Linear create/update:

- execution が actively planned
- 複数 repo または workstream が関与
- internal ownership または sequencing が必要
- 大きな program lane の一部

機械的 mirror はしない。

### 4. 二システムの整合を保つ

work が active のとき:

- GitHub issue/PR は public に何が起きているかを示す
- Linear は owner、priority、execution lane を internal に追跡

ship または reject 時:

- public resolution を GitHub に post
- Linear task を accordingly mark

## レビュールール (Review Rules)

- title、summary、trust だけで merge しない。full diff を使う
- external-source feature は valuable だが self-contained でないなら ECC 内 rebuild
- CI red は classify して fix または block。merge-ready のふりをしない
- 本当の blocker が product direction なら tooling に隠さず言う

## 出力形式 (Output Format)

返却:

```text
PUBLIC STATUS
- issue / PR state
- CI / review state

CLASSIFICATION
- merge / port-rebuild / close / park
- one-paragraph rationale

LINEAR ACTION
- create / update / no Linear item needed
- project / lane if applicable

NEXT OPERATOR ACTION
- exact next move
```

## 適用例 (Good Use Cases)

- 「オープン PR backlog を監査し merge vs rebuild を教えて」
- 「GitHub issue を ECC 1.x と ECC 2.0 program lane にマップ」
- 「Linear issue が必要か GitHub のみでよいか確認」
