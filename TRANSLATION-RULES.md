# 翻訳実行計画 (Translation Execution Plan)

Composer 2.5 Multitask で `agents/` と `SKILL.md` の説明文翻訳を並列実行するための計画。2026-06-07 に確定。

## スコープ (Scope)

| 対象 | 件数 |
|------|------|
| `agents/*.md` | 63 |
| `skills/*/SKILL.md` | 251 |
| `.agents/skills/*/SKILL.md` | 33 |
| **合計** | **347** |

## バッチ設計方針 (Batch Design)

- **コンテキスト枯渇防止**: 1 Multitask タブ = 最大 15 ファイル
- **ループ防止**: 各タスクの完了条件を明示（「変更したファイル一覧を出力して終了」）
- **独立性**: バッチ間に依存なし → 全 26 バッチを同時起動可能
- **翻訳範囲**: 本文の説明文・見出しのみ（frontmatter の `description` は翻訳しない。[翻訳ルール](#翻訳ルール) 参照）

## バッチ一覧 (Batch List)

合計 **26 バッチ**（agents 6 + skills 17 + .agents/skills 3）。

### agents/ — 6 バッチ

#### A1（11 ファイル）

- `agents/a11y-architect.md`
- `agents/architect.md`
- `agents/build-error-resolver.md`
- `agents/chief-of-staff.md`
- `agents/code-architect.md`
- `agents/code-explorer.md`
- `agents/code-reviewer.md`
- `agents/code-simplifier.md`
- `agents/comment-analyzer.md`
- `agents/conversation-analyzer.md`
- `agents/cpp-build-resolver.md`

#### A2（11 ファイル）

- `agents/cpp-reviewer.md`
- `agents/csharp-reviewer.md`
- `agents/dart-build-resolver.md`
- `agents/database-reviewer.md`
- `agents/django-build-resolver.md`
- `agents/django-reviewer.md`
- `agents/doc-updater.md`
- `agents/docs-lookup.md`
- `agents/e2e-runner.md`
- `agents/fastapi-reviewer.md`
- `agents/flutter-reviewer.md`

#### A3（11 ファイル）

- `agents/fsharp-reviewer.md`
- `agents/gan-evaluator.md`
- `agents/gan-generator.md`
- `agents/gan-planner.md`
- `agents/go-build-resolver.md`
- `agents/go-reviewer.md`
- `agents/harmonyos-app-resolver.md`
- `agents/harness-optimizer.md`
- `agents/healthcare-reviewer.md`
- `agents/homelab-architect.md`
- `agents/java-build-resolver.md`

#### A4（11 ファイル）

- `agents/java-reviewer.md`
- `agents/kotlin-build-resolver.md`
- `agents/kotlin-reviewer.md`
- `agents/loop-operator.md`
- `agents/marketing-agent.md`
- `agents/mle-reviewer.md`
- `agents/network-architect.md`
- `agents/network-config-reviewer.md`
- `agents/network-troubleshooter.md`
- `agents/opensource-forker.md`
- `agents/opensource-packager.md`

#### A5（11 ファイル）

- `agents/opensource-sanitizer.md`
- `agents/performance-optimizer.md`
- `agents/planner.md`
- `agents/pr-test-analyzer.md`
- `agents/python-reviewer.md`
- `agents/pytorch-build-resolver.md`
- `agents/react-build-resolver.md`
- `agents/react-reviewer.md`
- `agents/refactor-cleaner.md`
- `agents/rust-build-resolver.md`
- `agents/rust-reviewer.md`

#### A6（8 ファイル）

- `agents/security-reviewer.md`
- `agents/seo-specialist.md`
- `agents/silent-failure-hunter.md`
- `agents/swift-build-resolver.md`
- `agents/swift-reviewer.md`
- `agents/tdd-guide.md`
- `agents/type-design-analyzer.md`
- `agents/typescript-reviewer.md`

### skills/ — 17 バッチ

#### S1（15 ファイル）

- `skills/accessibility/SKILL.md`
- `skills/agent-architecture-audit/SKILL.md`
- `skills/agent-eval/SKILL.md`
- `skills/agent-harness-construction/SKILL.md`
- `skills/agent-introspection-debugging/SKILL.md`
- `skills/agent-payment-x402/SKILL.md`
- `skills/agent-sort/SKILL.md`
- `skills/agentic-engineering/SKILL.md`
- `skills/agentic-os/SKILL.md`
- `skills/ai-first-engineering/SKILL.md`
- `skills/ai-regression-testing/SKILL.md`
- `skills/android-clean-architecture/SKILL.md`
- `skills/angular-developer/SKILL.md`
- `skills/api-connector-builder/SKILL.md`
- `skills/api-design/SKILL.md`

#### S2（15 ファイル）

- `skills/architecture-decision-records/SKILL.md`
- `skills/article-writing/SKILL.md`
- `skills/automation-audit-ops/SKILL.md`
- `skills/autonomous-agent-harness/SKILL.md`
- `skills/autonomous-loops/SKILL.md`
- `skills/backend-patterns/SKILL.md`
- `skills/benchmark/SKILL.md`
- `skills/benchmark-optimization-loop/SKILL.md`
- `skills/blender-motion-state-inspection/SKILL.md`
- `skills/blueprint/SKILL.md`
- `skills/brand-voice/SKILL.md`
- `skills/browser-qa/SKILL.md`
- `skills/bun-runtime/SKILL.md`
- `skills/canary-watch/SKILL.md`
- `skills/carrier-relationship-management/SKILL.md`

#### S3（15 ファイル）

- `skills/cisco-ios-patterns/SKILL.md`
- `skills/ck/SKILL.md`
- `skills/claude-devfleet/SKILL.md`
- `skills/click-path-audit/SKILL.md`
- `skills/clickhouse-io/SKILL.md`
- `skills/code-tour/SKILL.md`
- `skills/codebase-onboarding/SKILL.md`
- `skills/coding-standards/SKILL.md`
- `skills/compose-multiplatform-patterns/SKILL.md`
- `skills/configure-ecc/SKILL.md`
- `skills/connections-optimizer/SKILL.md`
- `skills/content-engine/SKILL.md`
- `skills/content-hash-cache-pattern/SKILL.md`
- `skills/context-budget/SKILL.md`
- `skills/continuous-agent-loop/SKILL.md`

#### S4（15 ファイル）

- `skills/continuous-learning/SKILL.md`
- `skills/continuous-learning-v2/SKILL.md`
- `skills/cost-aware-llm-pipeline/SKILL.md`
- `skills/cost-tracking/SKILL.md`
- `skills/council/SKILL.md`
- `skills/cpp-coding-standards/SKILL.md`
- `skills/cpp-testing/SKILL.md`
- `skills/crosspost/SKILL.md`
- `skills/csharp-testing/SKILL.md`
- `skills/customer-billing-ops/SKILL.md`
- `skills/customs-trade-compliance/SKILL.md`
- `skills/dart-flutter-patterns/SKILL.md`
- `skills/dashboard-builder/SKILL.md`
- `skills/data-scraper-agent/SKILL.md`
- `skills/data-throughput-accelerator/SKILL.md`

#### S5（15 ファイル）

- `skills/database-migrations/SKILL.md`
- `skills/deep-research/SKILL.md`
- `skills/defi-amm-security/SKILL.md`
- `skills/deployment-patterns/SKILL.md`
- `skills/design-system/SKILL.md`
- `skills/django-celery/SKILL.md`
- `skills/django-patterns/SKILL.md`
- `skills/django-security/SKILL.md`
- `skills/django-tdd/SKILL.md`
- `skills/django-verification/SKILL.md`
- `skills/dmux-workflows/SKILL.md`
- `skills/docker-patterns/SKILL.md`
- `skills/documentation-lookup/SKILL.md`
- `skills/dotnet-patterns/SKILL.md`
- `skills/dynamic-workflow-mode/SKILL.md`

#### S6（15 ファイル）

- `skills/e2e-testing/SKILL.md`
- `skills/ecc-guide/SKILL.md`
- `skills/ecc-tools-cost-audit/SKILL.md`
- `skills/email-ops/SKILL.md`
- `skills/energy-procurement/SKILL.md`
- `skills/enterprise-agent-ops/SKILL.md`
- `skills/error-handling/SKILL.md`
- `skills/eval-harness/SKILL.md`
- `skills/evm-token-decimals/SKILL.md`
- `skills/exa-search/SKILL.md`
- `skills/fal-ai-media/SKILL.md`
- `skills/fastapi-patterns/SKILL.md`
- `skills/finance-billing-ops/SKILL.md`
- `skills/flox-environments/SKILL.md`
- `skills/flutter-dart-code-review/SKILL.md`

#### S7（15 ファイル）

- `skills/foundation-models-on-device/SKILL.md`
- `skills/frontend-a11y/SKILL.md`
- `skills/frontend-design-direction/SKILL.md`
- `skills/frontend-patterns/SKILL.md`
- `skills/frontend-slides/SKILL.md`
- `skills/fsharp-testing/SKILL.md`
- `skills/gan-style-harness/SKILL.md`
- `skills/gateguard/SKILL.md`
- `skills/git-workflow/SKILL.md`
- `skills/github-ops/SKILL.md`
- `skills/golang-patterns/SKILL.md`
- `skills/golang-testing/SKILL.md`
- `skills/google-workspace-ops/SKILL.md`
- `skills/healthcare-cdss-patterns/SKILL.md`
- `skills/healthcare-emr-patterns/SKILL.md`

#### S8（15 ファイル）

- `skills/healthcare-eval-harness/SKILL.md`
- `skills/healthcare-phi-compliance/SKILL.md`
- `skills/hermes-imports/SKILL.md`
- `skills/hexagonal-architecture/SKILL.md`
- `skills/hipaa-compliance/SKILL.md`
- `skills/homelab-network-readiness/SKILL.md`
- `skills/homelab-network-setup/SKILL.md`
- `skills/homelab-pihole-dns/SKILL.md`
- `skills/homelab-vlan-segmentation/SKILL.md`
- `skills/homelab-wireguard-vpn/SKILL.md`
- `skills/hookify-rules/SKILL.md`
- `skills/inventory-demand-planning/SKILL.md`
- `skills/investor-materials/SKILL.md`
- `skills/investor-outreach/SKILL.md`
- `skills/ios-icon-gen/SKILL.md`

#### S9（15 ファイル）

- `skills/iterative-retrieval/SKILL.md`
- `skills/ito-basket-compare/SKILL.md`
- `skills/ito-data-atlas-agent/SKILL.md`
- `skills/ito-market-intelligence/SKILL.md`
- `skills/ito-trade-planner/SKILL.md`
- `skills/java-coding-standards/SKILL.md`
- `skills/jira-integration/SKILL.md`
- `skills/jpa-patterns/SKILL.md`
- `skills/knowledge-ops/SKILL.md`
- `skills/kotlin-coroutines-flows/SKILL.md`
- `skills/kotlin-exposed-patterns/SKILL.md`
- `skills/kotlin-ktor-patterns/SKILL.md`
- `skills/kotlin-patterns/SKILL.md`
- `skills/kotlin-testing/SKILL.md`
- `skills/laravel-patterns/SKILL.md`

#### S10（15 ファイル）

- `skills/laravel-plugin-discovery/SKILL.md`
- `skills/laravel-security/SKILL.md`
- `skills/laravel-tdd/SKILL.md`
- `skills/laravel-verification/SKILL.md`
- `skills/latency-critical-systems/SKILL.md`
- `skills/lead-intelligence/SKILL.md`
- `skills/liquid-glass-design/SKILL.md`
- `skills/llm-trading-agent-security/SKILL.md`
- `skills/logistics-exception-management/SKILL.md`
- `skills/make-interfaces-feel-better/SKILL.md`
- `skills/manim-video/SKILL.md`
- `skills/market-research/SKILL.md`
- `skills/marketing-campaign/SKILL.md`
- `skills/mcp-server-patterns/SKILL.md`
- `skills/messages-ops/SKILL.md`

#### S11（15 ファイル）

- `skills/mle-workflow/SKILL.md`
- `skills/motion-advanced/SKILL.md`
- `skills/motion-foundations/SKILL.md`
- `skills/motion-patterns/SKILL.md`
- `skills/motion-ui/SKILL.md`
- `skills/mysql-patterns/SKILL.md`
- `skills/nanoclaw-repl/SKILL.md`
- `skills/nestjs-patterns/SKILL.md`
- `skills/netmiko-ssh-automation/SKILL.md`
- `skills/network-bgp-diagnostics/SKILL.md`
- `skills/network-config-validation/SKILL.md`
- `skills/network-interface-health/SKILL.md`
- `skills/nextjs-turbopack/SKILL.md`
- `skills/nodejs-keccak256/SKILL.md`
- `skills/nutrient-document-processing/SKILL.md`

#### S12（15 ファイル）

- `skills/nuxt4-patterns/SKILL.md`
- `skills/openclaw-persona-forge/SKILL.md`
- `skills/opensource-pipeline/SKILL.md`
- `skills/parallel-execution-optimizer/SKILL.md`
- `skills/perl-patterns/SKILL.md`
- `skills/perl-security/SKILL.md`
- `skills/perl-testing/SKILL.md`
- `skills/plan-orchestrate/SKILL.md`
- `skills/plankton-code-quality/SKILL.md`
- `skills/postgres-patterns/SKILL.md`
- `skills/prediction-market-oracle-research/SKILL.md`
- `skills/prediction-market-risk-review/SKILL.md`
- `skills/prisma-patterns/SKILL.md`
- `skills/product-capability/SKILL.md`
- `skills/product-lens/SKILL.md`

#### S13（15 ファイル）

- `skills/production-audit/SKILL.md`
- `skills/production-scheduling/SKILL.md`
- `skills/project-flow-ops/SKILL.md`
- `skills/prompt-optimizer/SKILL.md`
- `skills/python-patterns/SKILL.md`
- `skills/python-testing/SKILL.md`
- `skills/pytorch-patterns/SKILL.md`
- `skills/quality-nonconformance/SKILL.md`
- `skills/quarkus-patterns/SKILL.md`
- `skills/quarkus-security/SKILL.md`
- `skills/quarkus-tdd/SKILL.md`
- `skills/quarkus-verification/SKILL.md`
- `skills/ralphinho-rfc-pipeline/SKILL.md`
- `skills/react-patterns/SKILL.md`
- `skills/react-performance/SKILL.md`

#### S14（15 ファイル）

- `skills/react-testing/SKILL.md`
- `skills/recsys-pipeline-architect/SKILL.md`
- `skills/recursive-decision-ledger/SKILL.md`
- `skills/redis-patterns/SKILL.md`
- `skills/regex-vs-llm-structured-text/SKILL.md`
- `skills/remotion-video-creation/SKILL.md`
- `skills/repo-scan/SKILL.md`
- `skills/research-ops/SKILL.md`
- `skills/returns-reverse-logistics/SKILL.md`
- `skills/rules-distill/SKILL.md`
- `skills/rust-patterns/SKILL.md`
- `skills/rust-testing/SKILL.md`
- `skills/safety-guard/SKILL.md`
- `skills/santa-method/SKILL.md`
- `skills/scientific-db-pubmed-database/SKILL.md`

#### S15（15 ファイル）

- `skills/scientific-db-uspto-database/SKILL.md`
- `skills/scientific-pkg-gget/SKILL.md`
- `skills/scientific-thinking-literature-review/SKILL.md`
- `skills/scientific-thinking-scholar-evaluation/SKILL.md`
- `skills/search-first/SKILL.md`
- `skills/security-bounty-hunter/SKILL.md`
- `skills/security-review/SKILL.md`
- `skills/security-scan/SKILL.md`
- `skills/seo/SKILL.md`
- `skills/skill-comply/SKILL.md`
- `skills/skill-scout/SKILL.md`
- `skills/skill-stocktake/SKILL.md`
- `skills/social-graph-ranker/SKILL.md`
- `skills/social-publisher/SKILL.md`
- `skills/springboot-patterns/SKILL.md`

#### S16（15 ファイル）

- `skills/springboot-security/SKILL.md`
- `skills/springboot-tdd/SKILL.md`
- `skills/springboot-verification/SKILL.md`
- `skills/strategic-compact/SKILL.md`
- `skills/swift-actor-persistence/SKILL.md`
- `skills/swift-concurrency-6-2/SKILL.md`
- `skills/swift-protocol-di-testing/SKILL.md`
- `skills/swiftui-patterns/SKILL.md`
- `skills/tdd-workflow/SKILL.md`
- `skills/team-agent-orchestration/SKILL.md`
- `skills/team-builder/SKILL.md`
- `skills/terminal-ops/SKILL.md`
- `skills/tinystruct-patterns/SKILL.md`
- `skills/token-budget-advisor/SKILL.md`
- `skills/ui-demo/SKILL.md`

#### S17（11 ファイル）

- `skills/ui-to-vue/SKILL.md`
- `skills/uncloud/SKILL.md`
- `skills/unified-notifications-ops/SKILL.md`
- `skills/verification-loop/SKILL.md`
- `skills/video-editing/SKILL.md`
- `skills/videodb/SKILL.md`
- `skills/visa-doc-translate/SKILL.md`
- `skills/vite-patterns/SKILL.md`
- `skills/windows-desktop-e2e/SKILL.md`
- `skills/workspace-surface-audit/SKILL.md`
- `skills/x-api/SKILL.md`

### .agents/skills/ — 3 バッチ

#### AS1（11 ファイル）

- `.agents/skills/agent-introspection-debugging/SKILL.md`
- `.agents/skills/agent-sort/SKILL.md`
- `.agents/skills/api-design/SKILL.md`
- `.agents/skills/article-writing/SKILL.md`
- `.agents/skills/backend-patterns/SKILL.md`
- `.agents/skills/brand-voice/SKILL.md`
- `.agents/skills/bun-runtime/SKILL.md`
- `.agents/skills/coding-standards/SKILL.md`
- `.agents/skills/content-engine/SKILL.md`
- `.agents/skills/crosspost/SKILL.md`
- `.agents/skills/deep-research/SKILL.md`

#### AS2（11 ファイル）

- `.agents/skills/dmux-workflows/SKILL.md`
- `.agents/skills/documentation-lookup/SKILL.md`
- `.agents/skills/e2e-testing/SKILL.md`
- `.agents/skills/eval-harness/SKILL.md`
- `.agents/skills/everything-claude-code/SKILL.md`
- `.agents/skills/exa-search/SKILL.md`
- `.agents/skills/fal-ai-media/SKILL.md`
- `.agents/skills/frontend-patterns/SKILL.md`
- `.agents/skills/frontend-slides/SKILL.md`
- `.agents/skills/investor-materials/SKILL.md`
- `.agents/skills/investor-outreach/SKILL.md`

#### AS3（11 ファイル）

- `.agents/skills/market-research/SKILL.md`
- `.agents/skills/mcp-server-patterns/SKILL.md`
- `.agents/skills/mle-workflow/SKILL.md`
- `.agents/skills/nextjs-turbopack/SKILL.md`
- `.agents/skills/product-capability/SKILL.md`
- `.agents/skills/security-review/SKILL.md`
- `.agents/skills/strategic-compact/SKILL.md`
- `.agents/skills/tdd-workflow/SKILL.md`
- `.agents/skills/verification-loop/SKILL.md`
- `.agents/skills/video-editing/SKILL.md`
- `.agents/skills/x-api/SKILL.md`

## Multitask プロンプトテンプレート (Prompt Template)

各 Multitask タブに以下を貼り付け、対象ファイル一覧をバッチ定義から差し替える。

```
TRANSLATION-RULES.md の規則に従い、以下のファイルの説明文を日本語化してください。
完了したら変更したファイルの一覧を出力して終了してください。新しいファイルは作成しないでください。

対象ファイル:
- agents/xxx.md
- skills/yyy/SKILL.md
```

## 進捗確認 (Progress Check)

全バッチ完了後、以下で未翻訳の見出しを検出する:

```powershell
# 英語のみの見出しが残っていないか検出（簡易チェック）
rg "^## [A-Za-z]" agents/ skills/ .agents/skills/ --glob "*.md"
```

手動チェックは [チェックリスト](#チェックリスト) を使用する。


---
# 翻訳ルール

`SKILL.md`やエージェントを日本語化するときのルール。2026-06-07 に確定。

## 基本方針

- 説明文・ガイドライン・手順は **日本語** に翻訳する
- キーワード、ファイルパス、他ファイルへのリンク、URL は **原文のまま** 残す

## 翻訳しないもの

| 種別 | 例 |
|------|-----|
| キーワード・技術用語 | `feat`, `fix`, `docs`, `camelCase`, `Conventional Commits`, `CLAUDE_PLUGIN_ROOT` |
| ファイルパス・リンク | `rules/{language}/`, `skills/*/SKILL.md`, `AGENTS.md` |
| URL | `https://github.com/...`, `https://ecc.tools` |
| YAML の識別子 | `name: everything-claude-code-conventions`, `description: ...` |
| メタラベル（斜体・太字） | `*Commit message example*`, `**Example commit sequence**:` |
| コード内のパス・識別子 | `import { Button } from '../components/Button'` |

## 見出し（`#` で始まる H1〜H6）

**形式:** `日本語訳 (Original English)`

```markdown
## 概要 (Overview)
## コミット規約 (Commit Conventions)
### データベースマイグレーション (Database Migration)
### コミットスタイル: Conventional Commits (Commit Style: Conventional Commits)
### 言語: JavaScript (Language: JavaScript)
```

- 見出し内の技術用語（`Skill`, `Agent`, `JavaScript`, `Conventional Commits` など）は必要に応じて日本語側にも残す
- 括弧内には **元の英語見出し全文** を入れる

## コミットメッセージの例

- ラベル `*Commit message example*` は **英語のまま**（翻訳しない）
- コードブロック内のメッセージ本文は **日本語化** する
- プレフィックス、スコープ、PR 番号、固有名詞は **原文のまま**

```markdown
*Commit message example*

```text
feat(rules): C# 言語サポートを追加
```
```

```markdown
*Commit message example*

```text
fix: CLAUDE_PLUGIN_ROOT 未設定時に plugin cache から ECC root を自動検出 (#547) (#691)
```
```

## コミットシーケンスの例

- ラベル `**Example commit sequence**:` は **英語のまま**（翻訳しない）
- シーケンス本文（コードフェンス内）は **日本語化** する

```markdown
**Example commit sequence**:
```
feat: --with/--without 選択的インストールフラグを実装 (#679)
fix: カタログ件数を filesystem と同期 (27 agents, 113 skills, 58 commands) (#693)
```
```

手順説明形式のシーケンスも同様に本文のみ日本語化:

```markdown
**Example commit sequence**:
```
rules/{language}/ 配下に新しいディレクトリを作成
言語固有の内容で coding-style.md、hooks.md、patterns.md、security.md、testing.md を追加
```
```

## Frontmatter

- `name` と `description` は識別子・メタデータのため **原文のまま**（翻訳しない）

```yaml
---
name: everything-claude-code-conventions
description: Development conventions and patterns for everything-claude-code. JavaScript project using Conventional Commits.
---
```

## チェックリスト

翻訳完了時に確認:

- [ ] すべての `#` 見出しが `日本語 (English)` 形式か
- [ ] frontmatter の `name` と `description` が原文のままか
- [ ] `*Commit message example*` が英語のままか
- [ ] `**Example commit sequence**:` が英語のままか
- [ ] ファイルパス・リンク・URL が改変されていないか
- [ ] コミット例・シーケンスの本文が日本語化されているか
