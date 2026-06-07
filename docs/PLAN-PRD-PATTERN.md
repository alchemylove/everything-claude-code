# Plan-PRD パターン: Markdown 段階的プランニングフロー (Plan-PRD Pattern: Markdown-Staged Planning Flow)

SDLC に沿った軽量プランニングワークフロー。ライフサイクルの各フェーズが、次のコマンドが消費するコミット可能な markdown **staging file** を生成する。

> 短く言うと：`/plan-prd` が PRD を書き、`/plan` が plan を書き、`tdd-workflow` skill が実装し、`/pr` が出荷する。各矢印はメモリ内の会話ではなくディスク上のファイル。

## 機能: Markdown Staging Files (Feature: Markdown Staging Files)

すべてのプランニング artifact は `.claude/` 配下のプレーン `.md` ファイル：

```
.claude/
  prds/      # Product Requirements Documents from /plan-prd
  plans/     # Implementation plans from /plan
  reviews/   # Code review artifacts from /code-review
```

これらのファイルは：

- **プレーン markdown** — 人間が読める、PR で diff 可能、CLI で grep 可能。
- **コミット可能** — 意図が実装と一緒に旅するようコードと併せてチェックイン。
- **合成可能** — 各コマンドは前段のファイルを `$ARGUMENTS` として受け取り、ツールチェーンは in-context 状態ではなくパスで合成。
- **再開可能** — セッションを閉じ、翌日新しいセッションを開き、ファイルパスを渡し直す。

## フロー (Flow)

```
┌───────────────────────────┐
│ /plan-prd "<idea>"        │  Requirements phase
│  → .claude/prds/X.prd.md  │   Problem · Users · Hypothesis · Scope
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────┐
│ /plan <prd-path>          │  Design phase
│  → .claude/plans/X.plan.md│   Patterns · Files · Tasks · Validation
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────┐
│ tdd-workflow skill         │  Implementation phase
│  → code + tests           │   Test-first, minimal diff
└─────────────┬─────────────┘
              │
              ▼
┌───────────────────────────┐
│ /pr                        │  Delivery phase
│  → GitHub PR               │   Links back to PRD + plan
└───────────────────────────┘
```

各ボックスは **ゲート**。次ができる：

- ゲート間で停止 — artifact は永続。
- artifact パスで任意のゲートから再開。
- 小さな作業ではゲートをスキップ — `/plan` に自由形式テキストを渡し `/plan-prd` を無視。
- ゲートを単独実行 — `/plan "refactor X"` は artifact なしの会話型 plan を生成。

## なぜ `/plan-prd` は `/plan` に追加されるか (Why `/plan-prd` Is Additional to `/plan`)

異なる問いに答える。混ぜるとスコープ creep が起きる。

| Command | Answers | SDLC Phase | Artifact |
|---|---|---|---|
| `/plan-prd` | *What problem? For whom? How do we know we're done?* | Requirements | `.claude/prds/{name}.prd.md` |
| `/plan` | *What files, patterns, and tasks satisfy the requirement?* | Design + Implementation strategy | `.claude/plans/{name}.plan.md` (PRD mode) or inline (text mode) |

### なぜ統合しないか？ (Why not combine them?)

- **関心の分離。** PRD は *why* を問い；plan は *how* を問う。束ねると要件に実装フェーズ表が混ざった8フェーズ尋問の旧 `/prp-prd` → `/prp-plan` ペアのように、両方 poorly な1つの巨大コマンドになる。
- **異なる読者。** PRD をレビューするステークホルダーはファイルパスや type-check コマンドを気にしない。plan を読むエンジニアは市場調査フェーズを必要としない。
- **異なる寿命。** PRD は実装前提が変わって plan が何度も書き直されても安定しうる。
- **オプションステップ。** 多くの変更（bug fix、小リファクタ、単一ファイル追加）は PRD 不要。`/plan` だけで十分。すべての変更に PRD を強制するのは官僚主義。

### いつどちらを使うか (When to use each)

`/plan-prd` を使うとき：

- スコープが不明または争点がある。
- 複数ステークホルダーがソリューション前に問題で合意する必要がある。
- 変更が大きく、仮説を書き留める方が実装途中のスコープ再論争より安い。

`/plan` を直接使うとき：

- 要件がすでに明確（bug 報告、スコープ済みリファクタ、既知の migration）。
- 会話型 plan + 確認ゲートで十分なほど小さい作業。
- すでに PRD がある — `/plan` に渡し `/plan-prd` をスキップ。

## 使い方 (Usage)

### フルフロー（スコープ不明な feature）(Full flow (feature with unclear scope))

```bash
# 1. Draft the PRD
/plan-prd "Per-user rate limits on the public API"

# → .claude/prds/per-user-rate-limits.prd.md created
# Answer the framing questions, provide evidence, define hypothesis and scope.

# 2. Pick the next pending milestone and produce a plan
/plan .claude/prds/per-user-rate-limits.prd.md

# → .claude/plans/per-user-rate-limits.plan.md created
# The plan includes patterns to mirror, files to change, and validation commands.
# PRD's Delivery Milestones table updates the selected row to `in-progress`.

# 3. Implement test-first
Use the tdd-workflow skill

# 4. Open the PR
/pr
# → PR body auto-references .claude/prds/... and .claude/plans/...
```

### クイックフロー（スコープ明確）(Quick flow (scope already clear))

```bash
/plan "Add retry with exponential backoff to the notifier"
# Conversational planning, no artifact.
# Confirm, then use the tdd-workflow skill.
```

### 他处の既存 PRD を参照 (Reference an existing PRD from elsewhere)

```bash
# PRD was written by someone else, lives in your repo
/plan docs/rfcs/0042-rate-limiting.prd.md
```

`/plan` は任意の `.prd.md` パスを検出し artifact モードに切り替え、Delivery Milestones 表をパースする。

## なぜ staging file が in-context 状態に勝るか (Why staging files beat in-context state)

- **転送可能**: 新セッションに PRD パスを渡せば追いつける — 長い会話の再生不要。
- **監査可能**: PR レビュアーは *意図したこと* を *構築したこと* の隣で見られる。
- **バージョン管理**: staging file はコードと同様 git 履歴で進化。
- **機械パース可能**: `/plan` は次の pending milestone をプログラム的に選び；`/pr` は PR body に artifact をプログラム的にリンク。プロンプトエンジニアリング不要。

## 関連コマンド (Related commands)

- `/plan-prd` — requirements（このパターンのエントリポイント）。
- `/plan` — planning（PRD または自由形式テキストを消費）。
- `tdd-workflow` skill — test-first 実装。
- `/pr` — PRD と plan を参照する PR を開く。
- `/code-review` — ローカル diff または PR をレビュー；`.claude/prds/` と `.claude/plans/` をコンテキストとして自動検出。

## 互換性 (Compatibility)

このパターンは既存 `prp-*` コマンドセットと並ぶ ECC-native staging-file コマンドを追加する。レガシー PRP コマンドはより深い PRP ワークフローと `.claude/PRPs/` artifact をすでに持つユーザー向けに残る。

- `/plan-prd` は `.claude/prds/` 向けの lean requirements エントリポイント。
- `/plan` は `.prd.md` を消費し、レガシー PRP ディレクトリレイアウトを要求せず `.claude/plans/` artifact を生成できる。
- `/pr` は ECC-native PR 作成コマンドで `.claude/prds/` と `.claude/plans/` を参照できる。
- `/prp-prd`、`/prp-plan`、`/prp-implement`、`/prp-commit`、`/prp-pr` は有効なレガシー/深いワークフローコマンドのまま。
