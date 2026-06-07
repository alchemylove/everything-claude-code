---
name: gateguard
description: Fact-forcing gate that blocks Edit/Write/Bash (including MultiEdit) and demands concrete investigation (importers, data schemas, user instruction) before allowing the action. Measurably improves output quality by +2.25 points vs ungated agents.
origin: community
---

# GateGuard — 事実強制プリアクションゲート (GateGuard — Fact-Forcing Pre-Action Gate)

編集前に Claude に調査を強制する PreToolUse フック。自己評価（「本当に？」）ではなく具体的事実を要求する。調査行為自体が、自己評価では得られなかった認識を生む。

## 有効化タイミング (When to Activate)

- ファイル編集が複数モジュールに影響するコードベースで作業するとき
- 特定スキーマや日付形式を持つデータファイルがあるプロジェクト
- AI 生成コードが既存パターンに一致しなければならないチーム
- Claude が調査せず推測しがちなワークフロー全般

## コアコンセプト (Core Concept)

LLM の自己評価は機能しない。「ポリシー違反は？」と聞けば答えは常に「いいえ」。実験的に検証済み。

しかし「このモジュールを import する全ファイルを列挙せよ」と求めれば LLM は Grep と Read を実行せざるを得ない。調査自体が出力を変えるコンテキストを作る。

**3 段階ゲート:**

```
1. DENY  — block the first Edit/Write/Bash attempt
2. FORCE — tell the model exactly which facts to gather
3. ALLOW — permit retry after facts are presented
```

競合は 3 つすべてを行わない。多くは deny で止まる。

## エビデンス (Evidence)

同一エージェント、同一タスクの 2 つの独立 A/B テスト:

| Task | Gated | Ungated | Gap |
| --- | --- | --- | --- |
| Analytics module | 8.0/10 | 6.5/10 | +1.5 |
| Webhook validator | 10.0/10 | 7.0/10 | +3.0 |
| **Average** | **9.0** | **6.75** | **+2.25** |

両エージェントとも動作しテストを通すコードを生成。差は設計の深さ。

## ゲートタイプ (Gate Types)

### Edit / MultiEdit ゲート (Edit / MultiEdit Gate)

MultiEdit も同様 — バッチ内の各ファイルを個別にゲート。

```
Before editing {file_path}, present these facts:

1. List ALL files that import/require this file (use Grep)
2. List the public functions/classes affected by this change
3. If this file reads/writes data files, show field names, structure,
   and date format (use redacted or synthetic values, not raw production data)
4. Quote the user's current instruction verbatim
```

### Write ゲート (Write Gate)

```
Before creating {file_path}, present these facts:

1. Name the file(s) and line(s) that will call this new file
2. Confirm no existing file serves the same purpose (use Glob)
3. If this file reads/writes data files, show field names, structure,
   and date format (use redacted or synthetic values, not raw production data)
4. Quote the user's current instruction verbatim
```

### 破壊的 Bash ゲート (Destructive Bash Gate)

トリガー: `rm -rf`, `git reset --hard`, `git push --force`, `drop table` など。

```
1. List all files/data this command will modify or delete
2. Write a one-line rollback procedure
3. Quote the user's current instruction verbatim
```

### ルーティン Bash ゲート (Routine Bash Gate)

```
1. The current user request in one sentence
2. What this specific command verifies or produces
```

## クイックスタート (Quick Start)

### オプション A: ECC フックを使用 (Option A: Use the ECC hook)

`scripts/hooks/gateguard-fact-force.js` のフックがこのプラグインに含まれる。hooks.json で有効化。

GateGuard がセットアップや修復作業をブロックする場合、セッション開始時に `ECC_GATEGUARD=off`。フックレベル制御は GateGuard フック ID で `ECC_DISABLED_HOOKS` を継続使用。

### オプション B: 設定付きフルパッケージ (Option B: Full package with config)

```bash
pip install gateguard-ai
gateguard init
```

`.gateguard.yml` が追加され、プロジェクトごとにカスタムメッセージ、無視パス、ゲート切替を設定可能。

## アンチパターン (Anti-Patterns)

- **自己評価で代替しない。** 「本当に？」は常に「はい」。実験的に検証済み。
- **データスキーマチェックをスキップしない。** 両 A/B テストエージェントが実データが `%Y/%m/%d %H:%M` なのに ISO-8601 を仮定。データ構造確認（redacted 値）でこのクラスのバグ全体を防ぐ。
- **すべての Bash をゲートしない。** ルーティン bash はセッション 1 回。破壊的 bash は毎回。バランスで遅延を避けつつ実リスクを捕捉。

## ベストプラクティス (Best Practices)

- ゲートを自然に発火させる。ゲート質問に事前回答しようとしない — 調査自体が品質を上げる。
- ドメイン向けにゲートメッセージをカスタマイズ。プロジェクト固有規約があればゲートプロンプトに追加。
- `.gateguard.yml` で `.venv/`、`node_modules/`、`.git/` などを無視。

## 関連スキル (Related Skills)

- `safety-guard` — ランタイム安全チェック（補完、重複なし）
- `code-reviewer` — 編集後レビュー（GateGuard は編集前調査）
