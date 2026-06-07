---
description: "セッションから再利用可能なパターンを抽出し、保存前に品質を自己評価し、適切な保存場所（Global vs Project）を決定する。"
---

# /learn-eval - 抽出、評価、保存 (Extract, Evaluate, then Save)

`/learn` を拡張し、skill ファイルを書く前に品質ゲート、保存場所の決定、ナレッジ配置の認識を追加します。

## 抽出対象 (What to Extract)

以下を探します:

1. **エラー解決パターン (Error Resolution Patterns)** — 根本原因 + 修正 + 再利用性
2. **デバッグ技術 (Debugging Techniques)** — 自明でない手順、ツールの組み合わせ
3. **ワークアラウンド (Workarounds)** — ライブラリの癖、API の制限、バージョン固有の修正
4. **プロジェクト固有のパターン (Project-Specific Patterns)** — 規約、アーキテクチャの決定、統合パターン

## プロセス (Process)

1. セッションで抽出可能なパターンをレビューする
2. 最も価値がある/再利用可能な洞察を特定する

3. **保存場所を決定する (Determine save location):**
   - 質問: 「このパターンは別のプロジェクトでも役立つか？」
   - **Global** (`~/.claude/skills/learned/`): 2 つ以上のプロジェクトで使える汎用パターン（bash 互換性、LLM API の動作、デバッグ技術など）
   - **Project**（現在のプロジェクトの `.claude/skills/learned/`）: プロジェクト固有のナレッジ（特定の設定ファイルの癖、プロジェクト固有のアーキテクチャ決定など）
   - 迷ったら Global を選択する（Global → Project への移動はその逆より容易）

4. 以下の形式で skill ファイルを下書きする:

```markdown
---
name: pattern-name
description: "Under 130 characters"
user-invocable: false
origin: auto-extracted
---

# [Descriptive Pattern Name]

**Extracted:** [Date]
**Context:** [Brief description of when this applies]

## Problem
[What problem this solves - be specific]

## Solution
[The pattern/technique/workaround - with code examples]

## When to Use
[Trigger conditions]
```

5. **品質ゲート — チェックリスト + 総合判定 (Quality gate — Checklist + Holistic verdict)**

   ### 5a. 必須チェックリスト（実際にファイルを読んで検証）(Required checklist (verify by actually reading files))

   ドラフトを評価する前に、以下の**すべて**を実行する:

   - [ ] `~/.claude/skills/` および関連プロジェクトの `.claude/skills/` ファイルをキーワードで grep し、内容の重複を確認する
   - [ ] MEMORY.md（プロジェクトと global 両方）の重複を確認する
   - [ ] 既存 skill への追記で十分かを検討する
   - [ ] これが再利用可能なパターンであり、一度限りの修正でないことを確認する

   ### 5b. 総合判定 (Holistic verdict)

   チェックリスト結果とドラフト品質を統合し、以下の**いずれか 1 つ**を選択する:

   | Verdict | Meaning | Next Action |
   |---------|---------|-------------|
   | **Save** | Unique, specific, well-scoped | Proceed to Step 6 |
   | **Improve then Save** | Valuable but needs refinement | List improvements → revise → re-evaluate (once) |
   | **Absorb into [X]** | Should be appended to an existing skill | Show target skill and additions → Step 6 |
   | **Drop** | Trivial, redundant, or too abstract | Explain reasoning and stop |

**ガイドライン指標 (Guideline dimensions)**（判定を通知するが、スコアリングはしない）:

- **Specificity & Actionability**: すぐに使えるコード例やコマンドが含まれている
- **Scope Fit**: 名前、トリガー条件、内容が整合し、単一のパターンに焦点を当てている
- **Uniqueness**: 既存 skill でカバーされていない価値を提供する（チェックリスト結果から判断）
- **Reusability**: 将来のセッションで現実的なトリガーシナリオが存在する

6. **判定別の確認フロー (Verdict-specific confirmation flow)**

- **Improve then Save**: 必要な改善 + 修正ドラフト + 更新されたチェックリスト/判定を 1 回の再評価後に提示する。修正後の判定が **Save** ならユーザー確認後に保存し、それ以外は新しい判定に従う
- **Save**: 保存パス + チェックリスト結果 + 1 行の判定理由 + 完全なドラフトを提示 → ユーザー確認後に保存する
- **Absorb into [X]**: 対象パス + 追加内容（diff 形式）+ チェックリスト結果 + 判定理由を提示 → ユーザー確認後に追記する
- **Drop**: チェックリスト結果 + 理由のみを表示する（確認不要）

7. 決定された場所に保存/追記する

## ステップ 5 の出力形式 (Output Format for Step 5)

```
### Checklist
- [x] skills/ grep: no overlap (or: overlap found → details)
- [x] MEMORY.md: no overlap (or: overlap found → details)
- [x] Existing skill append: new file appropriate (or: should append to [X])
- [x] Reusability: confirmed (or: one-off → Drop)

### Verdict: Save / Improve then Save / Absorb into [X] / Drop

**Rationale:** (1-2 sentences explaining the verdict)
```

## 設計の根拠 (Design Rationale)

このバージョンは、以前の 5 次元数値スコアリング rubric（Specificity、Actionability、Scope Fit、Non-redundancy、Coverage を 1-5 でスコアリング）をチェックリストベースの総合判定システムに置き換えています。最新の frontier model（Opus 4.6+）は強力なコンテキスト判断能力を持っており、豊かな定性的シグナルを数値スコアに強制するとニュアンスが失われ、誤解を招く合計を生み出す可能性があります。総合的なアプローチにより、モデルがすべての要因を自然に重み付けし、明示的なチェックリストが重要なチェックのスキップを防ぎながら、より正確な save/drop 決定を生み出します。

## 注意事項 (Notes)

- 些細な修正（タイプミス、単純な構文エラー）は抽出しない
- 一度限りの問題（特定の API 障害など）は抽出しない
- 将来のセッションで時間を節約できるパターンに焦点を当てる
- skill は集中させる — 1 skill に 1 パターン
- 判定が Absorb の場合、新しいファイルを作成せず既存 skill に追記する
