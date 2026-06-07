---
description: 敵対的デュアルレビュー収束ループ — 2つの独立したモデルレビュアーがコード出荷前に両方とも承認する必要があります。
---

# Santa Loop (Santa Loop)

santa-method スキルを用いた敵対的デュアルレビュー収束ループ。2つの独立したレビュアー（異なるモデル、共有コンテキストなし）が、コード出荷前に両方とも NICE を返す必要があります。

## 目的 (Purpose)

現在のタスク出力に対して2つの独立したレビュアー（Claude Opus + 外部モデル）を実行します。コードがプッシュされる前に、両方が NICE を返す必要があります。どちらかが NAUGHTY を返した場合、フラグが立てられたすべての問題を修正し、コミットして、新しいレビュアーで再実行します（最大3ラウンド）。

## 使い方 (Usage)

```
/santa-loop [file-or-glob | description]
```

## ワークフロー (Workflow)

### ステップ 1: レビュー対象の特定 (Step 1: Identify What to Review)

`$ARGUMENTS` からスコープを判定するか、コミットされていない変更にフォールバックします：

```bash
git diff --name-only HEAD
```

すべての変更ファイルを読み取り、完全なレビューコンテキストを構築します。`$ARGUMENTS` がパス、ファイル、または説明を指定している場合は、それをスコープとして使用します。

### ステップ 2: ルーブリックの構築 (Step 2: Build the Rubric)

レビュー対象のファイルタイプに適したルーブリックを構築します。すべての基準には客観的な PASS/FAIL 条件が必要です。最低限以下を含めてください：

| 基準 | 合格条件 |
|------|----------|
| Correctness | ロジックが正しく、バグがなく、エッジケースを処理している |
| Security | シークレット、インジェクション、XSS、OWASP Top 10 の問題がない |
| Error handling | エラーが明示的に処理され、暗黙的な無視がない |
| Completeness | すべての要件が対処され、欠落ケースがない |
| Internal consistency | ファイル間またはセクション間の矛盾がない |
| No regressions | 変更が既存の動作を壊さない |

ファイルタイプに基づいてドメイン固有の基準を追加します（例：TS の型安全性、Rust のメモリ安全性、SQL のマイグレーション安全性）。

### ステップ 3: デュアル独立レビュー (Step 3: Dual Independent Review)

Agent ツールを使用して2つのレビュアーを**並列で**起動します（同時実行のために両方を1つのメッセージで起動）。判定ゲートに進む前に、両方が完了する必要があります。

各レビュアーはすべてのルーブリック基準を PASS または FAIL として評価し、構造化された JSON を返します：

```json
{
  "verdict": "PASS" | "FAIL",
  "checks": [
    {"criterion": "...", "result": "PASS|FAIL", "detail": "..."}
  ],
  "critical_issues": ["..."],
  "suggestions": ["..."]
}
```

判定ゲート（ステップ 4）はこれらを NICE/NAUGHTY にマッピングします：両方 PASS → NICE、どちらか FAIL → NAUGHTY。

#### レビュアー A: Claude Agent（常に実行） (Reviewer A: Claude Agent (always runs))

完全なルーブリック + レビュー対象のすべてのファイルを持つ Agent（subagent_type: `code-reviewer`、model: `opus`）を起動します。プロンプトには以下を含める必要があります：
- 完全なルーブリック
- レビュー対象のすべてのファイル内容
- "You are an independent quality reviewer. You have NOT seen any other review. Your job is to find problems, not to approve."
- 上記の構造化された JSON 判定を返すこと

#### レビュアー B: 外部モデル（外部 CLI がインストールされていない場合のみ Claude フォールバック） (Reviewer B: External Model (Claude fallback only if no external CLI installed))

まず、利用可能な CLI を検出します：
```bash
command -v codex >/dev/null 2>&1 && echo "codex" || true
command -v gemini >/dev/null 2>&1 && echo "gemini" || true
```

レビュアープロンプト（レビュアー A と同一のルーブリック + 指示）を構築し、一意の一時ファイルに書き込みます：
```bash
PROMPT_FILE=$(mktemp /tmp/santa-reviewer-b-XXXXXX.txt)
cat > "$PROMPT_FILE" << 'EOF'
... full rubric + file contents + reviewer instructions ...
EOF
```

最初に利用可能な CLI を使用します：

**Codex CLI**（インストールされている場合）
```bash
codex exec --sandbox read-only -m gpt-5.4 -C "$(pwd)" - < "$PROMPT_FILE"
rm -f "$PROMPT_FILE"
```

**Gemini CLI**（インストールされていて codex がない場合）
```bash
gemini -p "$(cat "$PROMPT_FILE")" -m gemini-2.5-pro
rm -f "$PROMPT_FILE"
```

**Claude Agent フォールバック**（`codex` も `gemini` もインストールされていない場合のみ）
2番目の Claude Agent（subagent_type: `code-reviewer`、model: `opus`）を起動します。両方のレビュアーが同じモデルファミリーを共有しているため、真のモデル多様性は達成されなかったが、コンテキスト分離は引き続き適用される旨の警告をログに記録します。

すべての場合において、レビュアーはレビュアー A と同じ構造化された JSON 判定を返す必要があります。

### ステップ 4: 判定ゲート (Step 4: Verdict Gate)

- **両方 PASS** → **NICE** — ステップ 6（プッシュ）に進む
- **どちらか FAIL** → **NAUGHTY** — 両方のレビュアーからのすべてのクリティカルな問題をマージし、重複を排除し、ステップ 5 に進む

### ステップ 5: 修正サイクル（NAUGHTY パス） (Step 5: Fix Cycle (NAUGHTY path))

1. 両方のレビュアーからのすべてのクリティカルな問題を表示する
2. フラグが立てられたすべての問題を修正する — フラグが立てられたものだけを変更し、ついでのリファクタリングはしない
3. すべての修正を1つのコミットにまとめる：
   ```
   fix: santa-loop レビュー所見に対応 (round N)
   ```
4. **新しいレビュアー**でステップ 3 を再実行する（前のラウンドの記憶なし）
5. 両方が PASS を返すまで繰り返す

**最大3イテレーション。** 3ラウンド後も NAUGHTY の場合、停止して残りの問題を提示します：

```
SANTA LOOP ESCALATION (exceeded 3 iterations)

3ラウンド後の残存問題：
- [両方のレビュアーからの未解決のクリティカルな問題をすべてリスト]

続行する前に手動レビューが必要です。
```

プッシュしないでください。

### ステップ 6: プッシュ（NICE パス） (Step 6: Push (NICE path))

両方のレビュアーが PASS を返した場合：

```bash
git push -u origin HEAD
```

### ステップ 7: 最終レポート (Step 7: Final Report)

出力レポートを表示します（下記の出力セクションを参照）。

## 出力 (Output)

```
SANTA VERDICT: [NICE / NAUGHTY (escalated)]

Reviewer A (Claude Opus):   [PASS/FAIL]
Reviewer B ([model used]):  [PASS/FAIL]

Agreement:
  Both flagged:      [両方が検出した問題]
  Reviewer A only:   [A のみが検出した問題]
  Reviewer B only:   [B のみが検出した問題]

Iterations: [N]/3
Result:     [PUSHED / ESCALATED TO USER]
```

## 注意事項 (Notes)

- レビュアー A（Claude Opus）は常に実行されます — ツール環境に関係なく、少なくとも1つの強力なレビュアーが保証されます。
- レビュアー B の目標はモデル多様性です。GPT-5.4 または Gemini 2.5 Pro は真の独立性を提供します — 異なる学習データ、異なるバイアス、異なる死角。Claude のみのフォールバックでもコンテキスト分離による価値はありますが、モデル多様性は失われます。
- 利用可能な最も強力なモデルが使用されます：レビュアー A には Opus、レビュアー B には GPT-5.4 または Gemini 2.5 Pro。
- 外部レビュアーはレビュー中のリポジトリ変更を防ぐため、`--sandbox read-only`（Codex）で実行されます。
- 各ラウンドで新しいレビュアーを使用することで、以前の発見に対するアンカリングバイアスを防ぎます。
- ルーブリックは最も重要な入力です。レビュアーがゴム印承認をしたり、主観的なスタイルの問題をフラグに立てる場合は、ルーブリックを厳格化してください。
- NAUGHTY ラウンドでコミットが行われるため、ループが中断されても修正は保持されます。
- プッシュは NICE 後にのみ発生します — ループ中には決して行われません。
