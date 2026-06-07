---
name: everything-claude-code-conventions
description: everything-claude-code の開発規約とパターン。Conventional Commits を採用した JavaScript プロジェクト。
---

# Everything Claude Code 規約 (Everything Claude Code Conventions)

> [affaan-m/everything-claude-code](https://github.com/affaan-m/everything-claude-code) より 2026-03-20 に生成

## 概要 (Overview)

この skill は、everything-claude-code で使われている開発パターンと規約を Claude に教えます。

## 技術スタック (Tech Stack)

- **Primary Language**: JavaScript
- **Architecture**: hybrid module organization
- **Test Location**: separate

## この Skill の使用タイミング (When to Use This Skill)

次の場合にこの skill を有効化してください:
- このリポジトリに変更を加えるとき
- 確立されたパターンに従って新機能を追加するとき
- プロジェクトの規約に合ったテストを書くとき
- 適切なメッセージ形式でコミットを作成するとき

## コミット規約 (Commit Conventions)

500 件のコミットを分析して得られた、コミットメッセージの規約に従ってください。

### コミットスタイル: Conventional Commits (Commit Style: Conventional Commits)

### 使用するプレフィックス (Prefixes Used)

- `fix`
- `test`
- `feat`
- `docs`

### メッセージのガイドライン (Message Guidelines)

- 平均メッセージ長: 約 65 文字
- 1 行目は簡潔かつ説明的に
- 命令形を使う（「Added feature」ではなく「Add feature」）


*Commit message example*

```text
feat(rules): C# 言語サポートを追加
```

*Commit message example*

```text
chore(deps-dev): flatted をバージョンアップ (#675)
```

*Commit message example*

```text
fix: CLAUDE_PLUGIN_ROOT 未設定時に plugin cache から ECC root を自動検出 (#547) (#691)
```

*Commit message example*

```text
docs: Antigravity のセットアップと利用ガイドを追加 (#552)
```

*Commit message example*

```text
merge: PR #529 — feat(skills): documentation-lookup、bun-runtime、nextjs-turbopack を追加; feat(agents): rust-reviewer を追加
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

### プロジェクト構成: 単一パッケージ (Project Structure: Single Package)

このプロジェクトは **hybrid** なモジュール構成を採用しています。

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

- このプロジェクトは hybrid な構成を採用しています
- 新しいコードを追加するときは既存のパターンに従ってください

## コードスタイル (Code Style)

### 言語: JavaScript (Language: JavaScript)

### 命名規則 (Naming Conventions)

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

特定のテストフレームワークは検出されませんでした — リポジトリの既存のテストパターンを使用してください。

### ファイルパターン: `*.test.js` (File Pattern: `*.test.js`)

### テストの種類 (Test Types)

- **Unit tests**: 個別の関数やコンポーネントを分離してテストする
- **Integration tests**: 複数のコンポーネントやサービス間の連携をテストする

### カバレッジ (Coverage)

このプロジェクトにはカバレッジレポートの設定があります。80% 以上のカバレッジを目指してください。


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

## 一般的なワークフロー (Common Workflows)

コミットパターンの分析から検出されたワークフローです。

### データベースマイグレーション (Database Migration)

マイグレーションファイルを伴うデータベーススキーマの変更

**Frequency**: 月約 2 回

**Steps**:
1. マイグレーションファイルを作成する
2. スキーマ定義を更新する
3. 型を生成または更新する

**Files typically involved**:
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

**Frequency**: 月約 22 回

**Steps**:
1. 機能実装を追加する
2. 機能のテストを追加する
3. ドキュメントを更新する

**Files typically involved**:
- `manifests/*`
- `schemas/*`
- `**/*.test.*`
- `**/api/**`

**Example commit sequence**:
```
feat(skills): documentation-lookup、bun-runtime、nextjs-turbopack を追加; feat(agents): rust-reviewer を追加
docs(skills): documentation-lookup を CONTRIBUTING テンプレートに合わせる; cross-harness (Codex/Cursor) skill コピーを追加
fix: PR レビューに対応 — skill テンプレート (When to use, How it works, Examples)、bun.lock、next build 注記、rust-reviewer CI 注記、doc-lookup のプライバシー/不確実性
```

### 言語ルールの追加 (Add Language Rules)

rules システムに新しいプログラミング言語を追加する。coding style、hooks、patterns、security、testing のガイドラインを含みます。

**Frequency**: 月約 2 回

**Steps**:
1. `rules/{language}/` 配下に新しいディレクトリを作成する
2. 言語固有の内容で `coding-style.md`、`hooks.md`、`patterns.md`、`security.md`、`testing.md` を追加する
3. 必要に応じて関連 skill を参照またはリンクする

**Files typically involved**:
- `rules/*/coding-style.md`
- `rules/*/hooks.md`
- `rules/*/patterns.md`
- `rules/*/security.md`
- `rules/*/testing.md`

**Example commit sequence**:
```
rules/{language}/ 配下に新しいディレクトリを作成
言語固有の内容で coding-style.md、hooks.md、patterns.md、security.md、testing.md を追加
必要に応じて関連 skill を参照またはリンク
```

### 新規 Skill の追加 (Add New Skill)

新しい skill をシステムに追加する。ワークフロー、トリガー、使い方を文書化し、必要に応じてスクリプトを含みます。

**Frequency**: 月約 4 回

**Steps**:
1. `skills/{skill-name}/` 配下に新しいディレクトリを作成する
2. `SKILL.md` にドキュメントを追加する（When to Use、How It Works、Examples など）
3. 必要に応じて `skills/{skill-name}/scripts/` 配下にスクリプトや補助ファイルを追加する
4. レビューフィードバックに対応し、ドキュメントを反復改善する

**Files typically involved**:
- `skills/*/SKILL.md`
- `skills/*/scripts/*.sh`
- `skills/*/scripts/*.js`

**Example commit sequence**:
```
skills/{skill-name}/ 配下に新しいディレクトリを作成
SKILL.md にドキュメントを追加 (When to Use、How It Works、Examples など)
必要に応じて skills/{skill-name}/scripts/ 配下にスクリプトや補助ファイルを追加
レビューフィードバックに対応し、ドキュメントを反復改善
```

### 新規 Agent の追加 (Add New Agent)

コードレビュー、ビルド解決、その他の自動化タスク用に新しい agent をシステムに追加する。

**Frequency**: 月約 2 回

**Steps**:
1. `agents/{agent-name}.md` 配下に新しい agent の markdown ファイルを作成する
2. `AGENTS.md` に agent を登録する
3. 必要に応じて `README.md` と `docs/COMMAND-AGENT-MAP.md` を更新する

**Files typically involved**:
- `agents/*.md`
- `AGENTS.md`
- `README.md`
- `docs/COMMAND-AGENT-MAP.md`

**Example commit sequence**:
```
agents/{agent-name}.md 配下に新しい agent の markdown ファイルを作成
AGENTS.md に agent を登録
必要に応じて README.md と docs/COMMAND-AGENT-MAP.md を更新
```

### 新規 Command の追加 (Add New Command)

新しい command をシステムに追加する。多くの場合、対応する skill とセットで追加します。

**Frequency**: 月約 1 回

**Steps**:
1. `commands/{command-name}.md` 配下に新しい markdown ファイルを作成する
2. 必要に応じて `skills/{skill-name}/SKILL.md` 配下の対応 skill を追加または更新する

**Files typically involved**:
- `commands/*.md`
- `skills/*/SKILL.md`

**Example commit sequence**:
```
commands/{command-name}.md 配下に新しい markdown ファイルを作成
必要に応じて skills/{skill-name}/SKILL.md 配下の対応 skill を追加または更新
```

### カタログ件数の同期 (Sync Catalog Counts)

`AGENTS.md` と `README.md` に記載されている agent、skill、command の件数を、リポジトリの実際の状態と同期する。

**Frequency**: 月約 3 回

**Steps**:
1. `AGENTS.md` の agent、skill、command の件数を更新する
2. `README.md`（quick-start、比較表など）の同じ件数を更新する
3. 必要に応じて他のドキュメントファイルも更新する

**Files typically involved**:
- `AGENTS.md`
- `README.md`

**Example commit sequence**:
```
AGENTS.md の agent、skill、command の件数を更新
README.md (quick-start、比較表など) の同じ件数を更新
必要に応じて他のドキュメントファイルも更新
```

### Cross Harness Skill コピーの追加 (Add Cross Harness Skill Copies)

異なる agent harness（例: Codex、Cursor、Antigravity）向けに skill のコピーを追加し、プラットフォーム間の互換性を確保する。

**Frequency**: 月約 2 回

**Steps**:
1. `SKILL.md` を `.agents/skills/{skill}/SKILL.md` および/または `.cursor/skills/{skill}/SKILL.md` にコピーまたは適応する
2. 必要に応じて harness 固有の `openai.yaml` や設定ファイルを追加する
3. `CONTRIBUTING` テンプレートに合わせるようレビューフィードバックに対応する

**Files typically involved**:
- `.agents/skills/*/SKILL.md`
- `.cursor/skills/*/SKILL.md`
- `.agents/skills/*/agents/openai.yaml`

**Example commit sequence**:
```
SKILL.md を .agents/skills/{skill}/SKILL.md および/または .cursor/skills/{skill}/SKILL.md にコピーまたは適応
必要に応じて harness 固有の openai.yaml や設定ファイルを追加
CONTRIBUTING テンプレートに合わせるようレビューフィードバックに対応
```

### Hook の追加または更新 (Add Or Update Hook)

ワークフロー、品質、セキュリティポリシーを強制する git または bash hook を追加または更新する。

**Frequency**: 月約 1 回

**Steps**:
1. `hooks/` または `scripts/hooks/` に hook スクリプトを追加または更新する
2. `hooks/hooks.json` などの設定に hook を登録する
3. 必要に応じて `tests/hooks/` にテストを追加または更新する

**Files typically involved**:
- `hooks/*.hook`
- `hooks/hooks.json`
- `scripts/hooks/*.js`
- `tests/hooks/*.test.js`
- `.cursor/hooks.json`

**Example commit sequence**:
```
hooks/ または scripts/hooks/ に hook スクリプトを追加または更新
hooks/hooks.json などの設定に hook を登録
必要に応じて tests/hooks/ にテストを追加または更新
```

### レビューフィードバックへの対応 (Address Review Feedback)

ドキュメント、スクリプト、設定を更新してコードレビューのフィードバックに対応する。明確性、正確性、規約への整合性を高めます。

**Frequency**: 月約 4 回

**Steps**:
1. レビュアーのコメントに対応するため `SKILL.md`、agent、command ファイルを編集する
2. 依頼に応じて例、見出し、設定を更新する
3. すべてのレビューフィードバックが解消されるまで反復する

**Files typically involved**:
- `skills/*/SKILL.md`
- `agents/*.md`
- `commands/*.md`
- `.agents/skills/*/SKILL.md`
- `.cursor/skills/*/SKILL.md`

**Example commit sequence**:
```
レビュアーのコメントに対応するため SKILL.md、agent、command ファイルを編集
依頼に応じて例、見出し、設定を更新
すべてのレビューフィードバックが解消されるまで反復
```


## ベストプラクティス (Best Practices)

コードベースの分析に基づき、次のプラクティスに従ってください:

### 推奨事項 (Do)

- Conventional Commits 形式を使う（`feat:`、`fix:` など）
- `*.test.js` の命名パターンに従う
- ファイル名に camelCase を使う
- mixed exports を優先する

### 非推奨事項 (Don't)

- 曖昧なコミットメッセージを書かない
- 新機能のテストを省略しない
- 議論なしに確立されたパターンから逸脱しない

---

*この skill は [ECC Tools](https://ecc.tools) により自動生成されました。チームのニーズに合わせてレビューし、カスタマイズしてください。*
