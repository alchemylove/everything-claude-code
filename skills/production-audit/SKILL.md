---
name: production-audit
description: Local-evidence production readiness audit for shipped apps, pre-launch reviews, post-merge checks, and "what breaks in prod?" questions without sending repo data to an external audit service.
origin: community
---

# Production 監査 (Production Audit)

user がアプリの ship 可否、本番での破損リスク、launch 前の修正点を尋ねるときにこの skill を使用する。stale な community production-audit idea の maintainer-safe 書き換え: production-readiness lens は維持し、unpinned 外部実行と third-party データ共有は除去。

## 使用タイミング (When to Use)

- user が "is this production-ready"、"what would break in prod"、"what did we miss"、"audit this repo"、"ready to ship?" と尋ねる
- feature が merge され、pre-deploy または post-merge risk pass が必要
- public launch、demo、customer rollout、investor walkthrough が近い
- CI は green だが production risk が欲しい（test status のみではない）
- deployed URL、release branch、PR、current checkout で evidence 収集可能

## 使用しないとき (When Not to Use)

- 実装中で lens が line-level secure coding のとき — 先に `security-review`
- 純 library、template、docs-only repo、scaffold（user が packaging/release readiness を望まない限り）
- 正式 compliance 監査を求められたとき。本 skill は engineering triage であり legal/financial/medical/regulatory 認証ではない
- evidence が product idea のみで repo、deployment、CI、runtime surface がないとき

## 仕組み (How It Works)

ローカルと user 承認 evidence から監査を構築する。unpinned remote code の実行、repository contents の third-party への upload、user が特定 tool と data flow を明示承認しない限りの external scanner 呼び出しはしない。

順序:

1. release surface を確立
2. 最近の変更と current branch state を読む
3. repo に実在する runtime、auth、data、payment、background-job、AI、deployment 境界を inspect
4. CI、test、migration、env doc、rollback path を確認
5. 具体的 fix 付きの短い ship/block 推奨を出す

## エビデンス (Evidence) チェックリスト (Evidence Checklist)

安価なローカル signal から:

```text
git status --short --branch
git log --oneline --decorate -20
git diff --stat origin/main...HEAD
```

次に project 固有 surface を inspect:

- package script、CI workflow、release script、Docker file、deployment manifest
- API route、webhook、auth middleware、background worker、cron job、database migration
- 環境変数 doc と startup check
- observability hook、error reporting、log、health check、dashboard
- rollback、seed、migration、backfill 手順
- 最重要 user path の E2E coverage

deployed URL が範囲内なら、その URL に対する browser/HTTP check のみ。safe test account なしに credentialed action はしない。

## リスクレンズ (Risk Lenses)

### セキュリティと Auth (Security And Auth)

- public route、API route、admin route は明確に分離されているか
- auth/authorization は server-side で強制されているか
- secret は client bundle、log、example output、commit ファイルから除外されているか
- rate limit、CSRF、CORS、upload validation は必要箇所にあるか
- AI/agent surface は prompt injection、tool abuse、untrusted content の privileged action 横断を防御しているか

### データ整合性 (Data Integrity)

- migration は forward で clean に走り rollback/recovery plan があるか
- destructive migration、backfill、import は staged か
- DB policy、grant、service-role 境界は tenancy model と一致するか
- write、job、webhook handler の retry は idempotent か

### 決済と Webhook (Payments And Webhooks)

- webhook signature は trusted payload field 解析前に検証されるか
- 各 payment/subscription/fulfillment webhook は idempotent か
- replay、duplicate delivery、out-of-order は処理されるか
- test-mode と live-mode credential は分離されているか

### 運用 (Operations)

- clean checkout から doc 通り起動できるか
- 必須 env は命名・検証・fail-fast か
- dependency 到達を証明する health check があるか
- deploy、rollback、incident owner path は doc 化されているか
- log は有用で secret/個人データを漏らさないか

### ユーザーエクスペリエンス (User Experience)

- launch-critical path は desktop/mobile でカバーされているか
- mobile で input zoom、layout overlap、blocked submission がないか
- loading、empty、error、permission-denied は何が起きたか伝えるか
- 重要操作失敗時の support/recovery path があるか

## スコアリング (Scoring)

スコアは優先順位付けのためであり数学的確実性を意味しない。

| Band | Score | Meaning |
| --- | --- | --- |
| Blocked | 0-49 | top risk 修正まで ship しない |
| Risky | 50-69 | 小規模 rollout または internal beta のみ |
| Launchable With Caveats | 70-84 | 列挙 risk を owner が受け入れれば ship |
| Strong | 85-100 | 利用可能 evidence から明らかな launch blocker なし |

次が真ならスコア上限 `69`:

- 機微 data で auth/authorization 欠如
- payment/fulfillment webhook が non-idempotent
- 必須 migration を安全に実行できない
- secret が client bundle、log、commit に露出
- 高影響 release の rollback path なし

CI が非 green または launch-critical path が E2E 未検証なら上限 `84`。

## 出力形式 (Output Format)

1 文で先頭:

```text
Production audit: 76/100, launchable with caveats, with webhook idempotency and rollback docs as the two risks to fix before public launch.
```

次を列挙:

- `Blockers`: deploy 前の must-fix
- `High-value fixes`: スコア改善の次 fix
- `Evidence checked`: inspect した file、command、CI、URL、PR
- `Evidence missing`: 提供されれば confidence が変わるもの
- `Next action`: 1 つの具体 fix または verification step

strength は短く。user は readiness を求めているので有用なのは残リスクと next action。

## 例 (Example)

User:

```text
is this ready to ship?
```

Response:

```text
Production audit: 68/100, risky, because Stripe webhooks are verified but not idempotent and there is no rollback note for the pending migration.

Blockers:
- Add idempotency for `checkout.session.completed` before fulfilling orders.
- Write and test the rollback path for `20260511_add_billing_state.sql`.

High-value fixes:
- Add a health check that verifies database and payment-provider reachability.
- Add one E2E path for upgrade, webhook fulfillment, and billing-page refresh.

Evidence checked:
- `api/stripe/webhook.ts`
- `db/migrations/20260511_add_billing_state.sql`
- GitHub Actions run for the release branch

Next action: Want me to patch webhook idempotency first?
```

## アンチパターン (Anti-Patterns)

- デフォルト監査 path として `npx <package>@latest` や remote scanner を実行
- 明示承認なしに source、secret、customer data、private topology を外部 audit service に upload
- evidence checked を名指しせず score のみ
- green CI を production readiness とみなす
- 汎用 "let me know what you want to do." で終える

## 関連 (See Also)

- Skill: `security-review`
- Skill: `deployment-patterns`
- Skill: `e2e-testing`
- Skill: `tdd-workflow`
- Skill: `verification-loop`
