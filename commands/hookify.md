---
description: 会話分析または明示的な指示から、望ましくない動作を防ぐ hook を作成します。
---

会話パターンの分析またはユーザーの明示的な指示により、望ましくない Claude Code の動作を防ぐ hook rule を作成します。

## 使い方 (Usage)

`/hookify [description of behavior to prevent]`

引数が提供されない場合、現在の会話を分析して防止すべき動作を検出します。

## ワークフロー (Workflow)

### ステップ 1: 動作情報の収集 (Step 1: Gather Behavior Info)

- 引数あり: ユーザーの unwanted behavior の説明を parse
- 引数なし: `conversation-analyzer` エージェントを使用して以下を検出:
  - explicit correction
  - 繰り返されるミスへの frustrated reaction
  - reverted change
  - 繰り返される類似 issue

### ステップ 2: 所見の提示 (Step 2: Present Findings)

ユーザーに以下を表示:

- behavior description
- proposed event type
- proposed pattern or matcher
- proposed action

### ステップ 3: ルールファイルの生成 (Step 3: Generate Rule Files)

承認された各 rule に対して、`.claude/hookify.{name}.local.md` に file を作成:

```yaml
---
name: rule-name
enabled: true
event: bash|file|stop|prompt|all
action: block|warn
pattern: "regex pattern"
---
Message shown when rule triggers.
```

### ステップ 4: 確認 (Step 4: Confirm)

作成された rule と、`/hookify-list` および `/hookify-configure` での管理方法を報告します。
