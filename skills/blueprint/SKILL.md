---
name: blueprint
description: >-
  1 行の目的を、マルチセッション・マルチエージェントのエンジニアリングプロジェクト向けのステップバイステップ構築計画に変換する。各ステップには新規エージェントがコールドスタートで実行できる自己完結型コンテキストブリーフを含む。敵対的レビューゲート、依存グラフ、並列ステップ検出、アンチパターンカタログ、計画変更プロトコルを含む。
  TRIGGER when: user requests a plan, blueprint, or roadmap for a
  complex multi-PR task, or describes work that needs multiple sessions.
  DO NOT TRIGGER when: task is completable in a single PR or fewer
  than 3 tool calls, or user says "just do it".
origin: community
---

# Blueprint — 構築計画ジェネレーター (Blueprint — Construction Plan Generator)

1 行の目的を、任意のコーディングエージェントがコールドスタートで実行できるステップバイステップ構築計画に変換する。

## 使用タイミング (When to Use)

- 大規模機能を依存順序の明確な複数 PR に分割するとき
- 複数セッションにまたがるリファクタやマイグレーションを計画するとき
- サブエージェント間の並列ワークストリームを調整するとき
- セッション間のコンテキスト喪失が手戻りを招くタスク全般

**使用しない**: 単一 PR で完了できるタスク、ツール呼び出し 3 回未満、ユーザーが「just do it」と言った場合。

## 仕組み (How It Works)

Blueprint は 5 フェーズパイプラインを実行する:

1. **リサーチ** — プリフライト（git、gh auth、remote、default branch）、プロジェクト構造、既存計画、メモリファイルの読み取りでコンテキスト収集。
2. **設計** — 目的を 1 PR サイズのステップ（通常 3–12）に分解。依存エッジ、並列/直列順序、モデルティア（strongest vs default）、ステップごとのロールバック戦略を割り当て。
3. **草案** — `plans/` に自己完結 Markdown 計画を書く。各ステップにコンテキストブリーフ、タスクリスト、検証コマンド、終了基準 — 前ステップを読まずに任意ステップを実行可能。
4. **レビュー** — チェックリストとアンチパターンカタログに対し最強モデルサブエージェント（例: Opus）へ敵対的レビューを委譲。確定前に重大所見をすべて修正。
5. **登録** — 計画を保存、メモリインデックス更新、ステップ数と並列性サマリーをユーザーに提示。

Blueprint は git/gh の有無を自動検出。git + GitHub CLI ありでフル branch/PR/CI ワークフロー計画。なしで direct モード（その場編集、ブランチなし）に切り替え。

## 例 (Examples)

### 基本用法 (Basic usage)

```
/blueprint myapp "migrate database to PostgreSQL"
```

`plans/myapp-migrate-database-to-postgresql.md` を生成。ステップ例:
- Step 1: Add PostgreSQL driver and connection config
- Step 2: Create migration scripts for each table
- Step 3: Update repository layer to use new driver
- Step 4: Add integration tests against PostgreSQL
- Step 5: Remove old database code and config

### マルチエージェントプロジェクト (Multi-agent project)

```
/blueprint chatbot "extract LLM providers into a plugin system"
```

可能なら並列ステップ（例: プラグインインターフェース完了後に「Anthropic プラグイン実装」と「OpenAI プラグイン実装」が並列）、モデルティア割り当て（インターフェース設計は strongest、実装は default）、毎ステップ後に検証する不変条件（例: 「既存テストすべてパス」「core にプロバイダー import なし」）を含む計画を生成。

## 主な機能 (Key Features)

- **コールドスタート実行** — 各ステップに自己完結コンテキストブリーフ。事前コンテキスト不要。
- **敵対的レビューゲート** — 完全性、依存正確性、アンチパターン検出をカバーするチェックリストで最強モデルサブエージェントが全計画をレビュー。
- **Branch/PR/CI ワークフロー** — 全ステップに組み込み。git/gh 不在時は direct モードへ優雅に劣化。
- **並列ステップ検出** — 依存グラフが共有ファイルや出力依存のないステップを特定。
- **計画変更プロトコル** — ステップの分割、挿入、スキップ、並べ替え、放棄を正式プロトコルと監査証跡付きで可能。
- **ゼロランタイムリスク** — 純 Markdown スキル。リポジトリ全体は `.md` のみ — hooks、shell スクリプト、実行可能コード、`package.json`、ビルドステップなし。インストール/呼び出し時に Claude Code のネイティブ Markdown スキルローダー以外は何も動かない。

## インストール (Installation)

このスキルは Everything Claude Code に同梱。ECC インストール時は別途インストール不要。

### フル ECC インストール (Full ECC install)

ECC リポジトリチェックアウトから作業する場合、次でスキル存在を確認:

```bash
test -f skills/blueprint/SKILL.md
```

後で更新する場合、更新前に ECC diff をレビュー:

```bash
cd /path/to/everything-claude-code
git fetch origin main
git log --oneline HEAD..origin/main       # review new commits before updating
git checkout <reviewed-full-sha>          # pin to a specific reviewed commit
```

### ベンダー単体インストール (Vendored standalone install)

フル ECC 以外でこのスキルのみベンダーする場合、レビュー済みファイルを ECC リポジトリから `~/.claude/skills/blueprint/SKILL.md` にコピー。ベンダーコピーに git remote はないため、`git pull` ではなくレビュー済み ECC コミットからファイルを再コピーして更新する。

## 要件 (Requirements)

- Claude Code（`/blueprint` スラッシュコマンド用）
- Git + GitHub CLI（任意 — フル branch/PR/CI ワークフロー有効化。不在時 Blueprint が検出し direct モードへ自動切替）

## ソース (Source)

antbotlab/blueprint に着想 — アップストリームプロジェクトと参照設計。
