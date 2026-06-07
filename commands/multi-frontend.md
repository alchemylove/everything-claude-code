---
description: コンポーネント、レイアウト、アニメーション、UI仕上げ向けのフロントエンド中心マルチモデルワークフローを実行します。
---

# Frontend - フロントエンド中心の開発 (Frontend-Focused Development)

フロントエンド中心のワークフロー（Research → Ideation → Plan → Execute → Optimize → Review）、Gemini主導。

## 使い方 (Usage)

```bash
/frontend <UI task description>
```

## コンテキスト (Context)

- フロントエンドタスク: $ARGUMENTS
- Gemini主導、Codexは補助的な参照用
- 適用範囲: コンポーネント設計、レスポンシブレイアウト、UIアニメーション、スタイル最適化

## 役割 (Your Role)

あなたは**フロントエンドオーケストレーター**として、UI/UXタスクのためのマルチモデル連携を調整します（Research → Ideation → Plan → Execute → Optimize → Review）。

**連携モデル (Collaborative Models)**:
- **Gemini** – フロントエンドUI/UX（**フロントエンドの権威、信頼できる**）
- **Codex** – バックエンドの視点（**フロントエンドの意見は参考のみ**）
- **Claude (self)** – オーケストレーション、計画、実装、配信

---

## マルチモデル呼び出し仕様 (Multi-Model Call Specification)

**呼び出し構文 (Call Syntax)**:

```
# New session call
Bash({
  command: "~/.claude/bin/codeagent-wrapper {{LITE_MODE_FLAG}}--backend gemini --gemini-model gemini-3-pro-preview - \"$PWD\" <<'EOF'
ROLE_FILE: <role prompt path>
<TASK>
Requirement: <enhanced requirement (or $ARGUMENTS if not enhanced)>
Context: <project context and analysis from previous phases>
</TASK>
OUTPUT: Expected output format
EOF",
  run_in_background: false,
  timeout: 3600000,
  description: "Brief description"
})

# Resume session call
Bash({
  command: "~/.claude/bin/codeagent-wrapper {{LITE_MODE_FLAG}}--backend gemini --gemini-model gemini-3-pro-preview resume <SESSION_ID> - \"$PWD\" <<'EOF'
ROLE_FILE: <role prompt path>
<TASK>
Requirement: <enhanced requirement (or $ARGUMENTS if not enhanced)>
Context: <project context and analysis from previous phases>
</TASK>
OUTPUT: Expected output format
EOF",
  run_in_background: false,
  timeout: 3600000,
  description: "Brief description"
})
```

**ロールプロンプト (Role Prompts)**:

| フェーズ | Gemini |
|-------|--------|
| 分析 | `~/.claude/.ccg/prompts/gemini/analyzer.md` |
| 計画 | `~/.claude/.ccg/prompts/gemini/architect.md` |
| レビュー | `~/.claude/.ccg/prompts/gemini/reviewer.md` |

**セッション再利用 (Session Reuse)**: 各呼び出しは`SESSION_ID: xxx`を返します。後続のフェーズでは`resume xxx`を使用してください。フェーズ2で`GEMINI_SESSION`を保存し、フェーズ3と5で`resume`を使用します。

---

## コミュニケーションガイドライン (Communication Guidelines)

1. レスポンスの開始時にモードラベル`[Mode: X]`を付ける、初期は`[Mode: Research]`
2. 厳格な順序に従う: `Research → Ideation → Plan → Execute → Optimize → Review`
3. 必要に応じて`AskUserQuestion`ツールを使用してユーザーとやり取りする（例: 確認/選択/承認）

---

## コアワークフロー (Core Workflow)

### フェーズ 0: プロンプト強化 (Prompt Enhancement (Optional))

`[Mode: Prepare]` - ace-tool MCPが利用可能な場合、`mcp__ace-tool__enhance_prompt`を呼び出し、**後続のGemini呼び出しのために元の$ARGUMENTSを強化結果で置き換える**。利用できない場合は`$ARGUMENTS`をそのまま使用。

### フェーズ 1: 調査 (Research)

`[Mode: Research]` - 要件の理解とコンテキストの収集

1. **コード取得 (Code Retrieval)**（ace-tool MCPが利用可能な場合）: `mcp__ace-tool__search_context`を呼び出して既存のコンポーネント、スタイル、デザインシステムを取得。利用できない場合は組み込みツールを使用: `Glob`でファイル検索、`Grep`でコンポーネント/スタイル検索、`Read`でコンテキスト収集、`Task`（Explore agent）でより深い探索。
2. 要件の完全性スコア（0-10）: >=7で継続、<7で停止して補足

### フェーズ 2: アイデア創出 (Ideation)

`[Mode: Ideation]` - Gemini主導の分析

**Geminiを呼び出す必要があります**（上記の呼び出し仕様に従う）:
- ROLE_FILE: `~/.claude/.ccg/prompts/gemini/analyzer.md`
- Requirement: 強化された要件（または強化されていない場合は$ARGUMENTS）
- Context: フェーズ1からのプロジェクトコンテキスト
- OUTPUT: UIの実現可能性分析、推奨ソリューション（少なくとも2つ）、UX評価

**SESSION_ID**（`GEMINI_SESSION`）を保存して後続のフェーズで再利用します。

ソリューション（少なくとも2つ）を出力し、ユーザーの選択を待ちます。

### フェーズ 3: 計画 (Planning)

`[Mode: Plan]` - Gemini主導の計画

**Geminiを呼び出す必要があります**（`resume <GEMINI_SESSION>`を使用してセッションを再利用）:
- ROLE_FILE: `~/.claude/.ccg/prompts/gemini/architect.md`
- Requirement: ユーザーが選択したソリューション
- Context: フェーズ2からの分析結果
- OUTPUT: コンポーネント構造、UIフロー、スタイリングアプローチ

Claudeが計画を統合し、ユーザーの承認後に`.claude/plan/task-name.md`に保存します。

### フェーズ 4: 実装 (Implementation)

`[Mode: Execute]` - コード開発

- 承認された計画に厳密に従う
- 既存プロジェクトのデザインシステムとコード標準に従う
- レスポンシブ性、アクセシビリティを保証

### フェーズ 5: 最適化 (Optimization)

`[Mode: Optimize]` - Gemini主導のレビュー

**Geminiを呼び出す必要があります**（上記の呼び出し仕様に従う）:
- ROLE_FILE: `~/.claude/.ccg/prompts/gemini/reviewer.md`
- Requirement: Review the following frontend code changes
- Context: git diff or code content
- OUTPUT: Accessibility, responsiveness, performance, design consistency issues list

レビューフィードバックを統合し、ユーザー確認後に最適化を実行します。

### フェーズ 6: 品質レビュー (Quality Review)

`[Mode: Review]` - 最終評価

- 計画に対する完成度をチェック
- レスポンシブ性とアクセシビリティを検証
- 問題と推奨事項を報告

---

## 重要なルール (Key Rules)

1. **Geminiのフロントエンド意見は信頼できる**
2. **Codexのフロントエンド意見は参考のみ**
3. 外部モデルは**ファイルシステムへの書き込みアクセスがゼロ**
4. Claudeがすべてのコード書き込みとファイル操作を処理
