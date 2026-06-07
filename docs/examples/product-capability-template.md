# プロダクト Capability テンプレート (Product Capability Template)

プロダクト intent はあるが実装制約がまだ暗黙のときにこれを使う。

目的は、もう一つの曖昧な planning doc ではなく durable capability contract を作ること。

## Capability

- **Capability name:**
- **Source:** PRD / issue / discussion / roadmap / founder note
- **Primary actor:**
- **Outcome after ship:**
- **Success signal:**

## プロダクト Intent (Product Intent)

ユーザーに見える約束を1短い段落で記述。

## 制約 (Constraints)

実装開始前に真であるべきルールを列挙：

- business rules
- scope boundaries
- invariants
- rollout constraints
- migration constraints
- backwards compatibility constraints
- billing / auth / compliance constraints

## Actor と Surface (Actors and Surfaces)

- actor(s)
- UI surfaces
- API surfaces
- automation / operator surfaces
- reporting / dashboard surfaces

## 状態と遷移 (States and Transitions)

明示的状態と許可遷移でライフサイクルを記述。

例：

- `draft -> active -> paused -> completed`
- `pending -> approved -> provisioned -> revoked`

## インターフェース契約 (Interface Contract)

- inputs
- outputs
- required side effects
- failure states
- retries / recovery
- idempotency expectations

## データへの含意 (Data Implications)

- source of truth
- new entities or fields
- ownership boundaries
- retention / deletion expectations

## セキュリティとポリシー (Security and Policy)

- trust boundaries
- permission requirements
- abuse paths
- policy / governance requirements

## 非目標 (Non-Goals)

この capability が明示的に担わないものを列挙。

## 未解決質問 (Open Questions)

実装をブロックする未解決決定を記録。

## ハンドオフ (Handoff)

- **Ready for implementation?**
- **Needs architecture review?**
- **Needs product clarification?**
- **Next ECC lane:** `project-flow-ops` / `tdd-workflow` / `verification-loop` / other
