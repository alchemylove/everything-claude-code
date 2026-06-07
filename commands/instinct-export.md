---
name: instinct-export
description: project/global スコープの instincts をファイルにエクスポートする
command: /instinct-export
---

# Instinct エクスポートコマンド (Instinct Export Command)

instincts を共有可能な形式でエクスポートします。以下の用途に最適です:
- チームメイトとの共有
- 新しいマシンへの転送
- プロジェクト規約への貢献

## 使い方 (Usage)

```
/instinct-export                           # すべての個人 instincts をエクスポート
/instinct-export --domain testing          # testing instincts のみをエクスポート
/instinct-export --min-confidence 0.7      # 高信頼度の instincts のみをエクスポート
/instinct-export --output team-instincts.yaml
/instinct-export --scope project --output project-instincts.yaml
```

## 実行内容 (What to Do)

1. 現在のプロジェクトコンテキストを検出する
2. 選択したスコープで instincts を読み込む:
   - `project`: 現在のプロジェクトのみ
   - `global`: global のみ
   - `all`: project + global をマージ（デフォルト）
3. フィルターを適用する（`--domain`、`--min-confidence`）
4. YAML 形式のエクスポートをファイルに書き込む（出力パス未指定時は stdout）

## 出力形式 (Output Format)

YAML ファイルを作成します:

```yaml
# Instincts Export
# Generated: 2025-01-22
# Source: personal
# Count: 12 instincts

---
id: prefer-functional-style
trigger: "when writing new functions"
confidence: 0.8
domain: code-style
source: session-observation
scope: project
project_id: a1b2c3d4e5f6
project_name: my-app
---

# Prefer Functional Style

## Action
Use functional patterns over classes.
```

## フラグ (Flags)

- `--domain <name>`: 指定ドメインのみをエクスポート
- `--min-confidence <n>`: 最小信頼度閾値
- `--output <file>`: 出力ファイルパス（省略時は stdout に出力）
- `--scope <project|global|all>`: エクスポートスコープ（デフォルト: `all`）
