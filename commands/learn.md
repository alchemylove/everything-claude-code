---
description: 現在のセッションから再利用可能なパターンを抽出し、候補 skill または guidance として保存する。
---

# /learn - 再利用可能なパターンの抽出 (Extract Reusable Patterns)

現在のセッションを分析し、skill として保存する価値のあるパターンを抽出します。

## トリガー (Trigger)

非自明な問題を解決したときに、セッション中の任意の時点で `/learn` を実行します。

## 抽出対象 (What to Extract)

以下を探します:

1. **エラー解決パターン (Error Resolution Patterns)**
   - どのようなエラーが発生したか
   - 根本原因は何か
   - 何が修正したか
   - 類似エラーに再利用可能か

2. **デバッグ技術 (Debugging Techniques)**
   - 自明でないデバッグ手順
   - うまく機能したツールの組み合わせ
   - 診断パターン

3. **ワークアラウンド (Workarounds)**
   - ライブラリの癖
   - API の制限
   - バージョン固有の修正

4. **プロジェクト固有のパターン (Project-Specific Patterns)**
   - 発見されたコードベースの規約
   - 行われたアーキテクチャの決定
   - 統合パターン

## 出力形式 (Output Format)

`~/.claude/skills/learned/[pattern-name].md` に skill ファイルを作成します:

```markdown
# [Descriptive Pattern Name]

**Extracted:** [Date]
**Context:** [Brief description of when this applies]

## Problem
[What problem this solves - be specific]

## Solution
[The pattern/technique/workaround]

## Example
[Code example if applicable]

## When to Use
[Trigger conditions - what should activate this skill]
```

## プロセス (Process)

1. セッションで抽出可能なパターンをレビューする
2. 最も価値がある/再利用可能な洞察を特定する
3. skill ファイルを下書きする
4. 保存前にユーザーに確認を求める
5. `~/.claude/skills/learned/` に保存する

## 注意事項 (Notes)

- 些細な修正（タイプミス、単純な構文エラー）は抽出しない
- 一度限りの問題（特定の API 障害など）は抽出しない
- 将来のセッションで時間を節約できるパターンに焦点を当てる
- skill は集中させる — 1 skill に 1 パターン
