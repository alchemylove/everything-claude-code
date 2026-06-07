---
name: agent-introspection-debugging
description: Structured self-debugging workflow for AI agent failures using capture, diagnosis, contained recovery, and introspection reports.
---

# エージェント内省デバッグ (Agent Introspection Debugging)

エージェント実行が繰り返し失敗している、トークンを消費するだけで進まない、同じツールをループしている、意図したタスクから逸脱している場合にこの skill を使う。

これは隠し runtime ではなく workflow skill である。人間へエスカレーションする前に、エージェントが体系的に自己デバッグする方法を教える。

## 有効化タイミング (When to Activate)

- Maximum tool call / loop-limit 失敗
- 前進なしの繰り返しリトライ
- コンテキスト肥大や prompt drift による出力品質低下
- 期待と現実のファイルシステム / 環境状態の不一致
- 診断と小さな是正で回復可能と思われるツール失敗

## スコープ境界 (Scope Boundaries)

この skill を有効化する対象:
- 盲目的リトライ前の失敗状態の capture
- 一般的なエージェント固有失敗パターンの diagnosis
- contained recovery アクションの適用
- 構造化された人間可読 debug report の生成

主な情報源として使わない対象:
- コード変更後の feature verification → `verification-loop` を使用
- より狭い ECC skill が既にある framework 固有デバッグ
- 現在の harness が自動強制できない runtime 約束

## 4 フェーズループ (Four-Phase Loop)

### フェーズ 1: 失敗 Capture (Phase 1: Failure Capture)

回復を試す前に、失敗を正確に記録する。

Capture 対象:
- 利用可能なら error type、message、stack trace
- 最後の意味のある tool call シーケンス
- エージェントが試みていたこと
- 現在の context pressure: 繰り返し prompt、巨大 pasted log、重複 plan、暴走 note
- 現在の環境仮定: cwd、branch、関連 service state、期待ファイル

最小 capture テンプレート:

```markdown
## 障害キャプチャ (Failure Capture)
- Session / task:
- Goal in progress:
- Error:
- Last successful step:
- Last failed tool / command:
- Repeated pattern seen:
- Environment assumptions to verify:
```

### フェーズ 2: 根本原因 Diagnosis (Phase 2: Root-Cause Diagnosis)

何かを変える前に、失敗を既知パターンに照合する。

| Pattern | Likely Cause | Check |
| --- | --- | --- |
| Maximum tool calls / repeated same command | loop または no-exit observer path | 直近 N 件の tool call で繰り返しを確認 |
| Context overflow / degraded reasoning | 無制限 note、重複 plan、巨大 log | 直近 context の重複と低シグナル bulk を確認 |
| `ECONNREFUSED` / timeout | service 不可用または wrong port | service health、URL、port 仮定を確認 |
| `429` / quota exhaustion | retry storm または backoff 欠如 | 繰り返し call 数と retry 間隔を確認 |
| file missing after write / stale diff | race、wrong cwd、branch drift | path、cwd、git status、実ファイル存在を再確認 |
| tests still failing after “fix” | wrong hypothesis | 失敗 test を特定し bug を再導出 |

Diagnosis 質問:
- logic failure、state failure、environment failure、policy failure のどれか？
- エージェントは本来の objective を失い、誤った subtask を最適化していないか？
- 失敗は deterministic か transient か？
- diagnosis を検証する最小の可逆 action は何か？

### フェーズ 3: Contained Recovery (Phase 3: Contained Recovery)

diagnosis surface を変える最小 action で回復する。

安全な recovery action:
- 繰り返し retry を止め hypothesis を再述
- 低シグナル context を削り、active goal、blocker、evidence のみ残す
- 実 filesystem / branch / process state を再確認
- タスクを 1 つの failing command、1 ファイル、1 test に絞る
- 推測 reasoning から直接 observation へ切り替え
- 高リスクまたは外部ブロック時は人間へ escalate

「reset agent state」や「update harness config」など、現在の環境で実際に real tools 経由で行っていない限り、未サポートの auto-healing を主張しない。

Contained recovery チェックリスト:

```markdown
## リカバリ (Recovery) Action
- Diagnosis chosen:
- Smallest action taken:
- Why this is safe:
- What evidence would prove the fix worked:
```

### フェーズ 4: Introspection Report (Phase 4: Introspection Report)

次のエージェントまたは人間が回復を追える report で締める。

```markdown
## エージェント自己デバッグレポート (Agent Self-Debug Report)
- Session / task:
- Failure:
- Root cause:
- Recovery action:
- Result: success | partial | blocked
- Token / time burn risk:
- Follow-up needed:
- Preventive change to encode later:
```

## 回復ヒューリスティクス (Recovery Heuristics)

次の順で intervention を優先する:

1. 本来の objective を 1 文で再述する。
2. 記憶を信じず world state を検証する。
3. 失敗 scope を縮小する。
4. 1 つの判別 check を実行する。
5. その check が支持する場合のみ plan を変更する。

悪いパターン:
- 言い回しを少し変えて同じ action を 3 回 retry する

良いパターン:
- failure を capture
- pattern を分類
- 1 つの直接 check を実行
- check が支持する場合のみ plan を変更

## ECC との統合 (Integration with ECC)

- 回復後にコードを変更した場合は `verification-loop` を使用。
- 失敗パターンを instinct や後続 skill に残す価値がある場合は `continuous-learning-v2` を使用。
- 技術失敗ではなく decision ambiguity の場合は `council` を使用。
- 競合する local state や repo drift が原因なら `workspace-surface-audit` を使用。

## 出力標準 (Output Standard)

この skill が active のとき、「直しました」だけで終えない。

必ず提供する:
- failure pattern
- root-cause hypothesis
- recovery action
- 状況が改善した、または依然 blocked である evidence
