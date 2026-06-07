---
name: ralphinho-rfc-pipeline
description: RFC-driven multi-agent DAG execution pattern with quality gates, merge queues, and work unit orchestration.
origin: ECC
---

# Ralphinho RFC Pipeline

[humanplane](https://github.com/humanplane) スタイルのRFC分解パターンと複数ユニットオーケストレーションワークフローにインスパイア。

単一エージェントパスでは大きすぎる機能を独立して検証可能なワークユニットに分割する必要がある場合、このスキルを使用します。

## パイプライン段階 (Pipeline Stages)

1. RFC intake
2. DAG decomposition
3. Unit assignment
4. Unit implementation
5. Unit validation
6. Merge queue and integration
7. Final system verification

## ユニット仕様テンプレート (Unit Spec Template)

各ワークユニットに含める：
- `id`
- `depends_on`
- `scope`
- `acceptance_tests`
- `risk_level`
- `rollback_plan`

## 複雑度ティア (Complexity Tiers)

- Tier 1: isolated file edits, deterministic tests
- Tier 2: multi-file behavior changes, moderate integration risk
- Tier 3: schema/auth/perf/security changes

## ユニットごとの品質パイプライン (Quality Pipeline per Unit)

1. research
2. implementation plan
3. implementation
4. tests
5. review
6. merge-ready report

## マージキュールール (Merge Queue Rules)

- Never merge a unit with unresolved dependency failures.
- Always rebase unit branches on latest integration branch.
- Re-run integration tests after each queued merge.

## リカバリ (Recovery)

ユニットが stall した場合：
- evict from active queue
- snapshot findings
- regenerate narrowed unit scope
- retry with updated constraints

## 出力 (Outputs)

- RFC execution log
- unit scorecards
- dependency graph snapshot
- integration risk summary
