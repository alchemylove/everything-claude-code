---
name: evolve
description: instinct を分析し、進化した構造を提案または生成する
command: true
---

# Evolve コマンド (Evolve Command)

## 実装 (Implementation)

プラグインルートパスを使って instinct CLI を実行:

```bash
python3 "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/scripts/instinct-cli.py" evolve [--generate]
```

`CLAUDE_PLUGIN_ROOT` が未設定の場合（手動インストール）:

```bash
python3 ~/.claude/skills/continuous-learning-v2/scripts/instinct-cli.py evolve [--generate]
```

instinct を分析し、関連するものをクラスタリングして上位構造にまとめる:
- **Commands**: instinct がユーザー起動のアクションを記述している場合
- **Skills**: instinct が自動トリガーの振る舞いを記述している場合
- **Agents**: instinct が複雑な多段階プロセスを記述している場合

## 使い方 (Usage)

```
/evolve                    # Analyze all instincts and suggest evolutions
/evolve --generate         # Also generate files under evolved/{skills,commands,agents}
```

## 進化ルール (Evolution Rules)

### → Command（ユーザー起動）(→ Command (User-Invoked))
instinct がユーザーが明示的に要求するアクションを記述している場合:
- 「ユーザーが X を依頼したとき...」に関する複数の instinct
- 「新しい X を作成するとき」のようなトリガーを持つ instinct
- 繰り返し可能なシーケンスに従う instinct

例:
- `new-table-step1`: "when adding a database table, create migration"
- `new-table-step2`: "when adding a database table, update schema"
- `new-table-step3`: "when adding a database table, regenerate types"

→ 作成: **new-table** command

### → Skill（自動トリガー）(→ Skill (Auto-Triggered))
instinct が自動的に起こるべき振る舞いを記述している場合:
- パターンマッチングトリガー
- エラーハンドリングの応答
- コードスタイルの強制

例:
- `prefer-functional`: "when writing functions, prefer functional style"
- `use-immutable`: "when modifying state, use immutable patterns"
- `avoid-classes`: "when designing modules, avoid class-based design"

→ 作成: `functional-patterns` skill

### → Agent（深度/分離が必要）(→ Agent (Needs Depth/Isolation))
instinct が分離の恩恵を受ける複雑な多段階プロセスを記述している場合:
- デバッグワークフロー
- リファクタリングシーケンス
- リサーチタスク

例:
- `debug-step1`: "when debugging, first check logs"
- `debug-step2`: "when debugging, isolate the failing component"
- `debug-step3`: "when debugging, create minimal reproduction"
- `debug-step4`: "when debugging, verify fix with test"

→ 作成: **debugger** agent

## 実行内容 (What to Do)

1. 現在のプロジェクトコンテキストを検出
2. プロジェクト + グローバル instinct を読む（ID 競合時はプロジェクトが優先）
3. トリガー/ドメインパターンで instinct をグループ化
4. 以下を特定:
   - Skill 候補（2 つ以上の instinct を持つトリガークラスタ）
   - Command 候補（高信頼度のワークフロー instinct）
   - Agent 候補（より大きな高信頼度クラスタ）
5. 該当する場合、昇格候補（project -> global）を表示
6. `--generate` が渡された場合、以下にファイルを書き込む:
   - プロジェクトスコープ: `~/.claude/homunculus/projects/<project-id>/evolved/`
   - グローバルフォールバック: `~/.claude/homunculus/evolved/`

## 出力形式 (Output Format)

```
============================================================
  EVOLVE ANALYSIS - 12 instincts
  Project: my-app (a1b2c3d4e5f6)
  Project-scoped: 8 | Global: 4
============================================================

High confidence instincts (>=80%): 5

## SKILL CANDIDATES
1. Cluster: "adding tests"
   Instincts: 3
   Avg confidence: 82%
   Domains: testing
   Scopes: project

## COMMAND CANDIDATES (2)
  /adding-tests
    From: test-first-workflow [project]
    Confidence: 84%

## AGENT CANDIDATES (1)
  adding-tests-agent
    Covers 3 instincts
    Avg confidence: 82%
```

## フラグ (Flags)

- `--generate`: 分析出力に加えて進化したファイルを生成

## 生成ファイル形式 (Generated File Format)

### Command
```markdown
---
name: new-table
description: Create a new database table with migration, schema update, and type generation
command: /new-table
evolved_from:
  - new-table-migration
  - update-schema
  - regenerate-types
---

# New Table Command

[Generated content based on clustered instincts]

## Steps
1. ...
2. ...
```

### Skill
```markdown
---
name: functional-patterns
description: Enforce functional programming patterns
evolved_from:
  - prefer-functional
  - use-immutable
  - avoid-classes
---

# Functional Patterns Skill

[Generated content based on clustered instincts]
```

### Agent
```markdown
---
name: debugger
description: Systematic debugging agent
model: sonnet
evolved_from:
  - debug-check-logs
  - debug-isolate
  - debug-reproduce
---

# Debugger Agent

[Generated content based on clustered instincts]
```
