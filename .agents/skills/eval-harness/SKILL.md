---
name: eval-harness
description: Formal evaluation framework for Claude Code sessions implementing eval-driven development (EDD) principles
allowed-tools: Read, Write, Edit, Bash, Grep, Glob
---

# Eval Harness スキル (Eval Harness Skill)

eval-driven development (EDD) 原則を実装する、Claude Code セッション向けの正式な評価フレームワーク。

## 有効化タイミング (When to Activate)

- AI 支援ワークフロー向けの eval-driven development (EDD) をセットアップするとき
- Claude Code タスク完了の pass/fail 基準を定義するとき
- pass@k メトリクスでエージェントの信頼性を測定するとき
- プロンプトやエージェント変更向けの回帰テストスイートを作成するとき
- モデルバージョン間でエージェント性能をベンチマークするとき

## 哲学 (Philosophy)

Eval-Driven Development は eval を「AI 開発のユニットテスト」として扱う:
- 実装**前に**期待される挙動を定義
- 開発中に継続的に eval を実行
- 変更ごとに回帰を追跡
- 信頼性測定に pass@k メトリクスを使用

## Eval の種類 (Eval Types)

### ケイパビリティ Eval (Capability Evals)
Claude が以前できなかったことをできるかテスト:
```markdown
[CAPABILITY EVAL: feature-name]
Task: Description of what Claude should accomplish
Success Criteria:
  - [ ] Criterion 1
  - [ ] Criterion 2
  - [ ] Criterion 3
Expected Output: Description of expected result
```

### 回帰 Eval (Regression Evals)
変更が既存機能を壊さないことを保証:
```markdown
[REGRESSION EVAL: feature-name]
Baseline: SHA or checkpoint name
Tests:
  - existing-test-1: PASS/FAIL
  - existing-test-2: PASS/FAIL
  - existing-test-3: PASS/FAIL
Result: X/Y passed (previously Y/Y)
```

## グレーダーの種類 (Grader Types)

### 1. コードベースグレーダー (Code-Based Grader)
コードによる決定的チェック:
```bash
# Check if file contains expected pattern
grep -q "export function handleAuth" src/auth.ts && echo "PASS" || echo "FAIL"

# Check if tests pass
npm test -- --testPathPattern="auth" && echo "PASS" || echo "FAIL"

# Check if build succeeds
npm run build && echo "PASS" || echo "FAIL"
```

### 2. モデルベースグレーダー (Model-Based Grader)
Claude を使ってオープンエンドな出力を評価:
```markdown
[MODEL GRADER PROMPT]
Evaluate the following code change:
1. Does it solve the stated problem?
2. Is it well-structured?
3. Are edge cases handled?
4. Is error handling appropriate?

Score: 1-5 (1=poor, 5=excellent)
Reasoning: [explanation]
```

### 3. 人間グレーダー (Human Grader)
手動レビューにフラグ:
```markdown
[HUMAN REVIEW REQUIRED]
Change: Description of what changed
Reason: Why human review is needed
Risk Level: LOW/MEDIUM/HIGH
```

## メトリクス (Metrics)

### pass@k
「k 回の試行のうち少なくとも1回成功」
- pass@1: 初回試行の成功率
- pass@3: 3回以内の成功率
- 典型的な目標: pass@3 > 90%

### pass^k
「k 回の試行すべて成功」
- 信頼性のより高い基準
- pass^3: 3回連続成功
- クリティカルパスに使用

## Eval ワークフロー (Eval Workflow)

### 1. 定義（コーディング前）(Define (Before Coding))
```markdown
## 評価定義: feature-xyz (EVAL DEFINITION: feature-xyz)

### Capability Evals
1. Can create new user account
2. Can validate email format
3. Can hash password securely

### Regression Evals
1. Existing login still works
2. Session management unchanged
3. Logout flow intact

### Success Metrics
- pass@3 > 90% for capability evals
- pass^3 = 100% for regression evals
```

### 2. 実装 (Implement)
定義した eval を通過するコードを書く。

### 3. 評価 (Evaluate)
```bash
# Run capability evals
[Run each capability eval, record PASS/FAIL]

# Run regression evals
npm test -- --testPathPattern="existing"

# Generate report
```

### 4. レポート (Report)
```markdown
EVAL REPORT: feature-xyz
========================

Capability Evals:
  create-user:     PASS (pass@1)
  validate-email:  PASS (pass@2)
  hash-password:   PASS (pass@1)
  Overall:         3/3 passed

Regression Evals:
  login-flow:      PASS
  session-mgmt:    PASS
  logout-flow:     PASS
  Overall:         3/3 passed

Metrics:
  pass@1: 67% (2/3)
  pass@3: 100% (3/3)

Status: READY FOR REVIEW
```

## 統合パターン (Integration Patterns)

### 実装前 (Pre-Implementation)
```
/eval define feature-name
```
`.claude/evals/feature-name.md` に eval 定義ファイルを作成

### 実装中 (During Implementation)
```
/eval check feature-name
```
現在の eval を実行してステータスを報告

### 実装後 (Post-Implementation)
```
/eval report feature-name
```
完全な eval レポートを生成

## Eval の保存 (Eval Storage)

プロジェクト内に eval を保存:
```
.claude/
  evals/
    feature-xyz.md      # Eval definition
    feature-xyz.log     # Eval run history
    baseline.json       # Regression baselines
```

## ベストプラクティス (Best Practices)

1. **コーディング前に eval を定義** — 成功基準について明確に考えることを強制
2. **eval を頻繁に実行** — 早期に回帰を検出
3. **pass@k を時系列で追跡** — 信頼性のトレンドを監視
4. **可能ならコードグレーダーを使用** — 決定的 > 確率的
5. **セキュリティは人間レビュー** — セキュリティチェックを完全自動化しない
6. **eval を高速に保つ** — 遅い eval は実行されない
7. **eval をコードと一緒にバージョン管理** — eval は第一級のアーティファクト

## 例: 認証の追加 (Example: Adding Authentication)

```markdown
## 評価: add-authentication (EVAL: add-authentication)

### Phase 1: Define (10 min)
Capability Evals:
- [ ] User can register with email/password
- [ ] User can login with valid credentials
- [ ] Invalid credentials rejected with proper error
- [ ] Sessions persist across page reloads
- [ ] Logout clears session

Regression Evals:
- [ ] Public routes still accessible
- [ ] API responses unchanged
- [ ] Database schema compatible

### Phase 2: Implement (varies)
[Write code]

### Phase 3: Evaluate
Run: /eval check add-authentication

### Phase 4: Report
EVAL REPORT: add-authentication
==============================
Capability: 5/5 passed (pass@3: 100%)
Regression: 3/3 passed (pass^3: 100%)
Status: SHIP IT
```
