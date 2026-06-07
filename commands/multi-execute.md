---
description: Claudeを唯一のファイルシステムライターとして維持しながら、マルチモデル実装計画を実行します。
---

# Execute - マルチモデル協調実装 (Multi-Model Collaborative Execution)

マルチモデル協調実装 - 計画からプロトタイプを取得 → Claudeがリファクタリングして実装 → マルチモデル監査と配信。

$ARGUMENTS

---

## コアプロトコル (Core Protocols)

- **言語プロトコル (Language Protocol)**: ツール/モデルとやり取りする際は**英語**を使用し、ユーザーとはユーザーの言語でコミュニケーション
- **コード主権 (Code Sovereignty)**: 外部モデルは**ファイルシステムへの書き込みアクセスがゼロ**、すべての変更はClaudeが実行
- **ダーティプロトタイプのリファクタリング (Dirty Prototype Refactoring)**: Codex/Gemini Unified Diffを「ダーティプロトタイプ」として扱い、本番グレードのコードにリファクタリングする必要がある
- **損失制限メカニズム (Stop-Loss Mechanism)**: 現在のフェーズの出力が検証されるまで次のフェーズに進まない
- **前提条件 (Prerequisite)**: `/ccg:plan`の出力に対してユーザーが明示的に「Y」と返信した後のみ実行（欠落している場合は最初に確認が必要）

---

## マルチモデル呼び出し仕様 (Multi-Model Call Specification)

**呼び出し構文 (Call Syntax)**（並列: `run_in_background: true`を使用）:

```
# Resume session call (recommended) - Implementation Prototype
Bash({
  command: "~/.claude/bin/codeagent-wrapper {{LITE_MODE_FLAG}}--backend <codex|gemini> {{GEMINI_MODEL_FLAG}}resume <SESSION_ID> - \"$PWD\" <<'EOF'
ROLE_FILE: <role prompt path>
<TASK>
Requirement: <task description>
Context: <plan content + target files>
</TASK>
OUTPUT: Unified Diff Patch ONLY. Strictly prohibit any actual modifications.
EOF",
  run_in_background: true,
  timeout: 3600000,
  description: "Brief description"
})

# New session call - Implementation Prototype
Bash({
  command: "~/.claude/bin/codeagent-wrapper {{LITE_MODE_FLAG}}--backend <codex|gemini> {{GEMINI_MODEL_FLAG}}- \"$PWD\" <<'EOF'
ROLE_FILE: <role prompt path>
<TASK>
Requirement: <task description>
Context: <plan content + target files>
</TASK>
OUTPUT: Unified Diff Patch ONLY. Strictly prohibit any actual modifications.
EOF",
  run_in_background: true,
  timeout: 3600000,
  description: "Brief description"
})
```

**監査呼び出し構文 (Audit Call Syntax)**（Code Review / Audit）:

```
Bash({
  command: "~/.claude/bin/codeagent-wrapper {{LITE_MODE_FLAG}}--backend <codex|gemini> {{GEMINI_MODEL_FLAG}}resume <SESSION_ID> - \"$PWD\" <<'EOF'
ROLE_FILE: <role prompt path>
<TASK>
Scope: Audit the final code changes.
Inputs:
- The applied patch (git diff / final unified diff)
- The touched files (relevant excerpts if needed)
Constraints:
- Do NOT modify any files.
- Do NOT output tool commands that assume filesystem access.
</TASK>
OUTPUT:
1) A prioritized list of issues (severity, file, rationale)
2) Concrete fixes; if code changes are needed, include a Unified Diff Patch in a fenced code block.
EOF",
  run_in_background: true,
  timeout: 3600000,
  description: "Brief description"
})
```

**モデルパラメータの注意事項 (Model Parameter Notes)**:
- `{{GEMINI_MODEL_FLAG}}`: `--backend gemini`を使用する場合、`--gemini-model gemini-3-pro-preview`で置き換える（末尾のスペースに注意）; codexの場合は空文字列を使用

**ロールプロンプト (Role Prompts)**:

| フェーズ | Codex | Gemini |
|-------|-------|--------|
| 実装 | `~/.claude/.ccg/prompts/codex/architect.md` | `~/.claude/.ccg/prompts/gemini/frontend.md` |
| レビュー | `~/.claude/.ccg/prompts/codex/reviewer.md` | `~/.claude/.ccg/prompts/gemini/reviewer.md` |

**セッション再利用 (Session Reuse)**: `/ccg:plan`がSESSION_IDを提供した場合、`resume <SESSION_ID>`を使用してコンテキストを再利用します。

**バックグラウンドタスクの待機 (Wait for Background Tasks)**（最大タイムアウト600000ms = 10分）:

```
TaskOutput({ task_id: "<task_id>", block: true, timeout: 600000 })
```

**重要 (IMPORTANT)**:
- `timeout: 600000`を指定する必要があります。指定しないとデフォルトの30秒で早期タイムアウトが発生します
- 10分後もまだ完了していない場合、`TaskOutput`でポーリングを継続し、**プロセスを強制終了しない**
- タイムアウトにより待機がスキップされた場合、**`AskUserQuestion`を呼び出してユーザーに待機を継続するか、タスクを強制終了するかを尋ねる必要があります**

---

## 実行ワークフロー (Execution Workflow)

**実行タスク (Execute Task)**: $ARGUMENTS

### フェーズ 0: 計画の読み取り (Read Plan)

`[Mode: Prepare]`

1. **入力タイプの識別 (Identify Input Type)**:
   - 計画ファイルパス（例: `.claude/plan/xxx.md`）
   - 直接的なタスク説明

2. **計画内容の読み取り (Read Plan Content)**:
   - 計画ファイルパスが提供された場合、読み取りと解析
   - 抽出: タスクタイプ、実装ステップ、キーファイル、SESSION_ID

3. **実行前の確認 (Pre-Execution Confirmation)**:
   - 入力が「直接的なタスク説明」または計画に`SESSION_ID` / キーファイルが欠落している場合: 最初にユーザーに確認
   - ユーザーが計画に「Y」と返信したことを確認できない場合: 進む前に再度確認する必要がある

4. **タスクタイプのルーティング (Task Type Routing)**:

   | タスクタイプ | 検出 | ルート |
   |-----------|-----------|-------|
   | **フロントエンド** | ページ、コンポーネント、UI、スタイル、レイアウト | Gemini |
   | **バックエンド** | API、インターフェース、データベース、ロジック、アルゴリズム | Codex |
   | **フルスタック** | フロントエンドとバックエンドの両方を含む | Codex ∥ Gemini 並列 |

---

### フェーズ 1: クイックコンテキスト取得 (Quick Context Retrieval)

`[Mode: Retrieval]`

**ace-tool MCPが利用可能な場合**、クイックコンテキスト取得に使用:

計画の「Key Files」リストに基づいて、`mcp__ace-tool__search_context`を呼び出します:

```
mcp__ace-tool__search_context({
  query: "<semantic query based on plan content, including key files, modules, function names>",
  project_root_path: "$PWD"
})
```

**取得戦略 (Retrieval Strategy)**:
- 計画の「Key Files」テーブルから対象パスを抽出
- カバー範囲のセマンティッククエリを構築: エントリファイル、依存モジュール、関連する型定義
- 結果が不十分な場合、1-2回の再帰的取得を追加

**ace-tool MCPが利用できない場合**、Claude Code組み込みツールでフォールバック:
1. **Glob**: 計画の「Key Files」テーブルから対象ファイルを検索（例: `Glob("src/components/**/*.tsx")`）
2. **Grep**: キーシンボル、関数名、型定義をコードベース全体で検索
3. **Read**: 発見したファイルを読み取り、完全なコンテキストを収集
4. **Task (Explore agent)**: より広範な探索が必要な場合、`Task`を`subagent_type: "Explore"`で使用

**取得後 (After Retrieval)**:
- 取得したコードスニペットを整理
- 実装のための完全なコンテキストを確認
- フェーズ3に進む

---

### フェーズ 3: プロトタイプの取得 (Prototype Acquisition)

`[Mode: Prototype]`

**タスクタイプに基づいてルーティング (Route Based on Task Type)**:

#### ルート A: フロントエンド/UI/スタイル → Gemini (Route A: Frontend/UI/Styles → Gemini)

**制限**: コンテキスト < 32kトークン

1. Geminiを呼び出す（`~/.claude/.ccg/prompts/gemini/frontend.md`を使用）
2. 入力: 計画内容 + 取得したコンテキスト + 対象ファイル
3. OUTPUT: `Unified Diff Patch ONLY. Strictly prohibit any actual modifications.`
4. **Geminiはフロントエンドデザインの権威であり、そのCSS/React/Vueプロトタイプは最終的なビジュアルベースライン**
5. **警告**: Geminiのバックエンドロジック提案を無視
6. 計画に`GEMINI_SESSION`が含まれている場合: `resume <GEMINI_SESSION>`を優先

#### ルート B: バックエンド/ロジック/アルゴリズム → Codex (Route B: Backend/Logic/Algorithms → Codex)

1. Codexを呼び出す（`~/.claude/.ccg/prompts/codex/architect.md`を使用）
2. 入力: 計画内容 + 取得したコンテキスト + 対象ファイル
3. OUTPUT: `Unified Diff Patch ONLY. Strictly prohibit any actual modifications.`
4. **Codexはバックエンドロジックの権威であり、その論理的推論とデバッグ機能を活用**
5. 計画に`CODEX_SESSION`が含まれている場合: `resume <CODEX_SESSION>`を優先

#### ルート C: フルスタック → 並列呼び出し (Route C: Fullstack → Parallel Calls)

1. **並列呼び出し**（`run_in_background: true`）:
   - Gemini: フロントエンド部分を処理
   - Codex: バックエンド部分を処理
2. `TaskOutput`で両方のモデルの完全な結果を待つ
3. それぞれ計画から対応する`SESSION_ID`を使用して`resume`（欠落している場合は新しいセッションを作成）

**上記の`マルチモデル呼び出し仕様`の`重要`指示に従ってください**

---

### フェーズ 4: コード実装 (Code Implementation)

`[Mode: Implement]`

**コード主権者としてのClaudeが以下のステップを実行 (Claude as Code Sovereign executes the following steps)**:

1. **差分の読み取り (Read Diff)**: Codex/Geminiが返したUnified Diff Patchを解析

2. **メンタルサンドボックス (Mental Sandbox)**:
   - 対象ファイルへの差分の適用をシミュレート
   - 論理的一貫性をチェック
   - 潜在的な競合や副作用を特定

3. **リファクタリングとクリーンアップ (Refactor and Clean)**:
   - 「ダーティプロトタイプ」を**高い可読性、保守性、エンタープライズグレードのコード**にリファクタリング
   - 冗長なコードを削除
   - プロジェクトの既存コード標準への準拠を保証
   - **必要でない限りコメント/ドキュメントを生成しない**、コードは自己説明的であるべき

4. **最小限のスコープ (Minimal Scope)**:
   - 変更は要件の範囲内のみに限定
   - 副作用の**必須レビュー**
   - 対象を絞った修正を実施

5. **変更の適用 (Apply Changes)**:
   - Edit/Writeツールを使用して実際の変更を実行
   - **必要なコードのみを変更**、ユーザーの他の既存機能に影響を与えない

6. **自己検証 (Self-Verification)**（強く推奨）:
   - プロジェクトの既存のlint / typecheck / testsを実行（最小限の関連スコープを優先）
   - 失敗した場合: 最初にリグレッションを修正し、その後フェーズ5に進む

---

### フェーズ 5: 監査と配信 (Audit and Delivery)

`[Mode: Audit]`

#### 5.1 自動監査 (Automatic Audit)

**変更が有効になった後、すぐにCodexとGeminiを並列呼び出ししてCode Reviewを実施する必要があります**:

1. **Codexレビュー (Codex Review)**（`run_in_background: true`）:
   - ROLE_FILE: `~/.claude/.ccg/prompts/codex/reviewer.md`
   - 入力: 変更されたDiff + 対象ファイル
   - フォーカス: セキュリティ、パフォーマンス、エラーハンドリング、ロジックの正確性

2. **Geminiレビュー (Gemini Review)**（`run_in_background: true`）:
   - ROLE_FILE: `~/.claude/.ccg/prompts/gemini/reviewer.md`
   - 入力: 変更されたDiff + 対象ファイル
   - フォーカス: アクセシビリティ、デザインの一貫性、ユーザーエクスペリエンス

`TaskOutput`で両方のモデルの完全なレビュー結果を待ちます。コンテキストの一貫性のため、フェーズ3のセッション（`resume <SESSION_ID>`）の再利用を優先します。

#### 5.2 統合と修正 (Integrate and Fix)

1. Codex + Geminiレビューフィードバックを統合
2. 信頼ルールに基づいて重み付け: バックエンドはCodexに従い、フロントエンドはGeminiに従う
3. 必要な修正を実行
4. 必要に応じてフェーズ5.1を繰り返す（リスクが許容可能になるまで）

#### 5.3 配信確認 (Delivery Confirmation)

監査が通過した後、ユーザーに報告:

```markdown
## Execution Complete

### Change Summary
| File | Operation | Description |
|------|-----------|-------------|
| path/to/file.ts | Modified | Description |

### Audit Results
- Codex: <Passed/Found N issues>
- Gemini: <Passed/Found N issues>

### Recommendations
1. [ ] <Suggested test steps>
2. [ ] <Suggested verification steps>
```

---

## 重要なルール (Key Rules)

1. **コード主権 (Code Sovereignty)** – すべてのファイル変更はClaudeが実行、外部モデルは書き込みアクセスがゼロ
2. **ダーティプロトタイプのリファクタリング (Dirty Prototype Refactoring)** – Codex/Geminiの出力はドラフトとして扱い、リファクタリングする必要がある
3. **信頼ルール (Trust Rules)** – バックエンドはCodexに従い、フロントエンドはGeminiに従う
4. **最小限の変更 (Minimal Changes)** – 必要なコードのみを変更、副作用なし
5. **必須監査 (Mandatory Audit)** – 変更後にマルチモデルCode Reviewを実施する必要がある

---

## 使い方 (Usage)

```bash
# Execute plan file
/ccg:execute .claude/plan/feature-name.md

# Execute task directly (for plans already discussed in context)
/ccg:execute implement user authentication based on previous plan
```

---

## /ccg:planとの関係 (Relationship with /ccg:plan)

1. `/ccg:plan`が計画 + SESSION_IDを生成
2. ユーザーが「Y」で確認
3. `/ccg:execute`が計画を読み取り、SESSION_IDを再利用し、実装を実行
