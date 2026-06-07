---
name: everything-claude-code
description: Development conventions and patterns for everything-claude-code. JavaScript project with conventional commits.
---

# Everything Claude Code 規約 (Everything Claude Code Conventions)

> 2026-03-20 に [affaan-m/everything-claude-code](https://github.com/affaan-m/everything-claude-code) から生成

## 概要 (Overview)

このスキルは everything-claude-code で使われる開発パターンと規約を Claude に教える。

## 技術スタック (Tech Stack)

- **Primary Language**: JavaScript
- **Architecture**: hybrid module organization
- **Test Location**: separate

## このスキルを使うタイミング (When to Use This Skill)

次の場合にこのスキルを有効化:
- このリポジトリに変更を加えるとき
- 確立されたパターンに従って新機能を追加するとき
- プロジェクト規約に合ったテストを書くとき
- 適切なメッセージ形式でコミットを作成するとき

## コミット規約 (Commit Conventions)

500件の分析済みコミットに基づくコミットメッセージ規約に従う。

### コミットスタイル: Conventional Commits (Commit Style: Conventional Commits)

### 使用するプレフィックス (Prefixes Used)

- `fix`
- `test`
- `feat`
- `docs`

### メッセージガイドライン (Message Guidelines)

- 平均メッセージ長: 約65文字
- 1行目は簡潔で説明的に
- 命令形を使用（"Added feature" ではなく "Add feature"）


*Commit message example*

```text
feat(rules): C# 言語サポートを追加
```

*Commit message example*

```text
chore(deps-dev): flatted をバンプ (#675)
```

*Commit message example*

```text
fix: CLAUDE_PLUGIN_ROOT 未設定時に plugin cache から ECC root を自動検出 (#547) (#691)
```

*Commit message example*

```text
docs: Antigravity セットアップ・利用ガイドを追加 (#552)
```

*Commit message example*

```text
merge: PR #529 — feat(skills): documentation-lookup, bun-runtime, nextjs-turbopack を追加; feat(agents): rust-reviewer を追加
```

*Commit message example*

```text
Revert "Kiro IDE サポート (.kiro/) を追加 (#548)"
```

*Commit message example*

```text
Kiro IDE サポート (.kiro/) を追加 (#548)
```

*Commit message example*

```text
feat: Claude Code と Cursor 向け block-no-verify hook を追加 (#649)
```

## アーキテクチャ (Architecture)

### プロジェクト構造: シングルパッケージ (Project Structure: Single Package)

このプロジェクトは **hybrid** モジュール構成を使用。

### 設定ファイル (Configuration Files)

- `.github/workflows/ci.yml`
- `.github/workflows/maintenance.yml`
- `.github/workflows/monthly-metrics.yml`
- `.github/workflows/release.yml`
- `.github/workflows/reusable-release.yml`
- `.github/workflows/reusable-test.yml`
- `.github/workflows/reusable-validate.yml`
- `.opencode/package.json`
- `.opencode/tsconfig.json`
- `.prettierrc`
- `eslint.config.js`
- `package.json`

### ガイドライン (Guidelines)

- このプロジェクトはハイブリッド構成を使用
- 新しいコードを追加するときは既存パターンに従う

## コードスタイル (Code Style)

### 言語: JavaScript (Language: JavaScript)

### 命名規約 (Naming Conventions)

| Element | Convention |
|---------|------------|
| Files | camelCase |
| Functions | camelCase |
| Classes | PascalCase |
| Constants | SCREAMING_SNAKE_CASE |

### インポートスタイル: 相対インポート (Import Style: Relative Imports)

### エクスポートスタイル: 混合スタイル (Export Style: Mixed Style)


*Preferred import style*

```typescript
// Use relative imports
import { Button } from '../components/Button'
import { useAuth } from './hooks/useAuth'
```

## テスト (Testing)

### テストフレームワーク (Test Framework)

特定のテストフレームワークは検出されず — リポジトリの既存テストパターンを使用。

### ファイルパターン: `*.test.js` (File Pattern: `*.test.js`)

### テスト種別 (Test Types)

- **Unit tests**: 個別の関数とコンポーネントを分離してテスト
- **Integration tests**: 複数コンポーネント/サービス間の相互作用をテスト

### カバレッジ (Coverage)

このプロジェクトはカバレッジレポートが設定済み。80%以上のカバレッジを目標とする。


## エラーハンドリング (Error Handling)

### エラーハンドリングスタイル: Try-Catch ブロック (Error Handling Style: Try-Catch Blocks)


*Standard error handling pattern*

```typescript
try {
  const result = await riskyOperation()
  return result
} catch (error) {
  console.error('Operation failed:', error)
  throw new Error('User-friendly message')
}
```

## よくあるワークフロー (Common Workflows)

コミットパターンの分析から検出されたワークフロー。

### データベースマイグレーション (Database Migration)

マイグレーションファイルによるデータベーススキーマ変更

**頻度**: 月約2回

**手順**:
1. マイグレーションファイルを作成
2. スキーマ定義を更新
3. 型を生成/更新

**通常関与するファイル**:
- `**/schema.*`
- `migrations/*`

**Example commit sequence**:
```
feat: --with/--without 選択的インストールフラグを実装 (#679)
fix: カタログ件数を filesystem と同期 (27 agents, 113 skills, 58 commands) (#693)
feat(rules): Rust 言語ルールを追加 (rebased #660) (#686)
```

### 機能開発 (Feature Development)

標準的な機能実装ワークフロー

**頻度**: 月約22回

**手順**:
1. 機能実装を追加
2. 機能のテストを追加
3. ドキュメントを更新

**通常関与するファイル**:
- `manifests/*`
- `schemas/*`
- `**/*.test.*`
- `**/api/**`

**Example commit sequence**:
```
feat(skills): documentation-lookup, bun-runtime, nextjs-turbopack を追加; feat(agents): rust-reviewer を追加
docs(skills): documentation-lookup を CONTRIBUTING テンプレートに合わせる; クロスハーネス (Codex/Cursor) スキルコピーを追加
fix: PR レビュー対応 — スキルテンプレート (When to use, How it works, Examples)、bun.lock、next build メモ、rust-reviewer CI メモ、doc-lookup プライバシー/不確実性
```

### 言語ルールの追加 (Add Language Rules)

rules システムに新しいプログラミング言語を追加。コーディングスタイル、hooks、パターン、セキュリティ、テストガイドラインを含む。

**頻度**: 月約2回

**手順**:
1. `rules/{language}/` 配下に新しいディレクトリを作成
2. 言語固有の内容で coding-style.md、hooks.md、patterns.md、security.md、testing.md を追加
3. 必要に応じて関連スキルを参照またはリンク

**通常関与するファイル**:
- `rules/*/coding-style.md`
- `rules/*/hooks.md`
- `rules/*/patterns.md`
- `rules/*/security.md`
- `rules/*/testing.md`

**Example commit sequence**:
```
rules/{language}/ 配下に新しいディレクトリを作成
言語固有の内容で coding-style.md、hooks.md、patterns.md、security.md、testing.md を追加
必要に応じて関連スキルを参照またはリンク
```

### 新規スキルの追加 (Add New Skill)

ワークフロー、トリガー、利用方法を文書化した新しいスキルをシステムに追加。多くの場合スクリプトを伴う。

**頻度**: 月約4回

**手順**:
1. `skills/{skill-name}/` 配下に新しいディレクトリを作成
2. SKILL.md にドキュメントを追加（When to Use, How It Works, Examples など）
3. 必要に応じて `skills/{skill-name}/scripts/` 配下にスクリプトや補助ファイルを追加
4. レビューフィードバックに対応しドキュメントを反復改善

**通常関与するファイル**:
- `skills/*/SKILL.md`
- `skills/*/scripts/*.sh`
- `skills/*/scripts/*.js`

**Example commit sequence**:
```
skills/{skill-name}/ 配下に新しいディレクトリを作成
SKILL.md にドキュメントを追加（When to Use, How It Works, Examples など）
必要に応じて skills/{skill-name}/scripts/ 配下にスクリプトや補助ファイルを追加
レビューフィードバックに対応しドキュメントを反復改善
```

### 新規エージェントの追加 (Add New Agent)

コードレビュー、ビルド解決、その他の自動化タスク向けの新しいエージェントをシステムに追加。

**頻度**: 月約2回

**手順**:
1. `agents/{agent-name}.md` 配下に新しいエージェント markdown ファイルを作成
2. AGENTS.md にエージェントを登録
3. 必要に応じて README.md と docs/COMMAND-AGENT-MAP.md を更新

**通常関与するファイル**:
- `agents/*.md`
- `AGENTS.md`
- `README.md`
- `docs/COMMAND-AGENT-MAP.md`

**Example commit sequence**:
```
agents/{agent-name}.md 配下に新しいエージェント markdown ファイルを作成
AGENTS.md にエージェントを登録
必要に応じて README.md と docs/COMMAND-AGENT-MAP.md を更新
```

### 新規ワークフローサーフェスの追加 (Add New Workflow Surface)

ワークフローエントリポイントを追加または更新。デフォルトは skills-first。レガシースラッシュ互換がまだ必要な場合のみ command shim を追加。

**頻度**: 月約1回

**手順**:
1. `skills/{skill-name}/SKILL.md` 配下に正規ワークフローを作成または更新
2. 必要な場合のみ、互換性 shim として `commands/{command-name}.md` を追加または更新

**通常関与するファイル**:
- `skills/*/SKILL.md`
- `commands/*.md` (only when a legacy shim is intentionally retained)

**Example commit sequence**:
```
skills/{skill-name}/SKILL.md 配下に正規スキルを作成または更新
必要な場合のみ、互換性 shim として commands/{command-name}.md を追加または更新
```

### カタログ件数の同期 (Sync Catalog Counts)

AGENTS.md と README.md の agents、skills、commands の文書化件数を実際のリポジトリ状態と同期。

**頻度**: 月約3回

**手順**:
1. AGENTS.md の agent、skill、command 件数を更新
2. README.md（クイックスタート、比較表など）の同じ件数を更新
3. 必要に応じて他のドキュメントファイルを更新

**通常関与するファイル**:
- `AGENTS.md`
- `README.md`

**Example commit sequence**:
```
AGENTS.md の agent、skill、command 件数を更新
README.md（クイックスタート、比較表など）の同じ件数を更新
必要に応じて他のドキュメントファイルを更新
```

### クロスハーネススキルコピーの追加 (Add Cross Harness Skill Copies)

異なるエージェントハーネス（Codex、Cursor、Antigravity など）向けにスキルコピーを追加し、プラットフォーム横断の互換性を確保。

**頻度**: 月約2回

**手順**:
1. SKILL.md を `.agents/skills/{skill}/SKILL.md` および/または `.cursor/skills/{skill}/SKILL.md` にコピーまたは適応
2. 必要に応じてハーネス固有の openai.yaml や設定ファイルを追加
3. CONTRIBUTING テンプレートに合わせるレビューフィードバックに対応

**通常関与するファイル**:
- `.agents/skills/*/SKILL.md`
- `.cursor/skills/*/SKILL.md`
- `.agents/skills/*/agents/openai.yaml`

**Example commit sequence**:
```
SKILL.md を .agents/skills/{skill}/SKILL.md および/または .cursor/skills/{skill}/SKILL.md にコピーまたは適応
必要に応じてハーネス固有の openai.yaml や設定ファイルを追加
CONTRIBUTING テンプレートに合わせるレビューフィードバックに対応
```

### Hook の追加または更新 (Add Or Update Hook)

ワークフロー、品質、セキュリティポリシーを強制する git または bash hooks を追加または更新。

**頻度**: 月約1回

**手順**:
1. `hooks/` または `scripts/hooks/` に hook スクリプトを追加または更新
2. `hooks/hooks.json` または類似設定に hook を登録
3. 必要に応じて `tests/hooks/` にテストを追加または更新

**通常関与するファイル**:
- `hooks/*.hook`
- `hooks/hooks.json`
- `scripts/hooks/*.js`
- `tests/hooks/*.test.js`
- `.cursor/hooks.json`

**Example commit sequence**:
```
hooks/ または scripts/hooks/ に hook スクリプトを追加または更新
hooks/hooks.json または類似設定に hook を登録
必要に応じて tests/hooks/ にテストを追加または更新
```

### レビューフィードバックへの対応 (Address Review Feedback)

明確性、正確性、規約整合のため、ドキュメント、スクリプト、設定を更新してコードレビューフィードバックに対応。

**頻度**: 月約4回

**手順**:
1. レビュアーコメントに対応するため SKILL.md、agent、command ファイルを編集
2. 依頼に応じて例、見出し、設定を更新
3. すべてのレビューフィードバックが解決するまで反復

**通常関与するファイル**:
- `skills/*/SKILL.md`
- `agents/*.md`
- `commands/*.md`
- `.agents/skills/*/SKILL.md`
- `.cursor/skills/*/SKILL.md`

**Example commit sequence**:
```
レビュアーコメントに対応するため SKILL.md、agent、command ファイルを編集
依頼に応じて例、見出し、設定を更新
すべてのレビューフィードバックが解決するまで反復
```


## ベストプラクティス (Best Practices)

コードベース分析に基づき、次のプラクティスに従う:

### 推奨 (Do)

- conventional commit 形式を使用（feat:, fix: など）
- `*.test.js` 命名パターンに従う
- ファイル名に camelCase を使用
- 混合エクスポートを優先

### 非推奨 (Don't)

- 曖昧なコミットメッセージを書かない
- 新機能のテストをスキップしない
- 議論なく確立されたパターンから逸脱しない

---

*このスキルは [ECC Tools](https://ecc.tools) により自動生成されました。チーム向けにレビューし必要に応じてカスタマイズしてください。*
