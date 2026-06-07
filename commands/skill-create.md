---
name: skill-create
description: ローカルの git 履歴を分析してコーディングパターンを抽出し、SKILL.md ファイルを生成します。Skill Creator GitHub App のローカル版です。
allowed_tools: ["Bash", "Read", "Write", "Grep", "Glob"]
---

# /skill-create - ローカルスキル生成 (/skill-create - Local Skill Generation)

リポジトリの git 履歴を分析してコーディングパターンを抽出し、Claude にチームのプラクティスを教える SKILL.md ファイルを生成します。

## 使い方 (Usage)

```bash
/skill-create                    # 現在のリポジトリを分析
/skill-create --commits 100      # 最後の100コミットを分析
/skill-create --output ./skills  # カスタム出力ディレクトリ
/skill-create --instincts        # continuous-learning-v2 用の instincts も生成
```

## 実行内容 (What It Does)

1. **Git 履歴の解析** - コミット、ファイル変更、パターンを分析
2. **パターンの検出** - 繰り返されるワークフローと慣習を特定
3. **SKILL.md の生成** - 有効な Claude Code スキルファイルを作成
4. **オプションで Instincts を作成** - continuous-learning-v2 システム用

## 分析ステップ (Analysis Steps)

### ステップ 1: Git データの収集 (Step 1: Gather Git Data)

```bash
# ファイル変更を含む最近のコミットを取得
git log --oneline -n ${COMMITS:-200} --name-only --pretty=format:"%H|%s|%ad" --date=short

# ファイル別のコミット頻度を取得
git log --oneline -n 200 --name-only | grep -v "^$" | grep -v "^[a-f0-9]" | sort | uniq -c | sort -rn | head -20

# コミットメッセージのパターンを取得
git log --oneline -n 200 | cut -d' ' -f2- | head -50
```

### ステップ 2: パターンの検出 (Step 2: Detect Patterns)

以下のパターンタイプを探します：

| パターン | 検出方法 |
|---------|-----------------|
| **Commit conventions** | コミットメッセージの正規表現（feat:, fix:, chore:） |
| **File co-changes** | 常に一緒に変更されるファイル |
| **Workflow sequences** | 繰り返されるファイル変更パターン |
| **Architecture** | フォルダ構造と命名規則 |
| **Testing patterns** | テストファイルの場所、命名、カバレッジ |

### ステップ 3: SKILL.md の生成 (Step 3: Generate SKILL.md)

出力フォーマット：

```markdown
---
name: {repo-name}-patterns
description: {repo-name} から抽出されたコーディングパターン
version: 1.0.0
source: local-git-analysis
analyzed_commits: {count}
---

# {Repo Name} Patterns

## Commit Conventions
{detected commit message patterns}

## Code Architecture
{detected folder structure and organization}

## Workflows
{detected repeating file change patterns}

## Testing Patterns
{detected test conventions}
```

### ステップ 4: Instincts の生成（--instincts の場合） (Step 4: Generate Instincts (if --instincts))

continuous-learning-v2 統合用：

```yaml
---
id: {repo}-commit-convention
trigger: "when writing a commit message"
confidence: 0.8
domain: git
source: local-repo-analysis
---

# Use Conventional Commits

## Action
Prefix commits with: feat:, fix:, chore:, docs:, test:, refactor:

## Evidence
- {n} 件のコミットを分析
- {percentage}% が conventional commit フォーマットに従う
```

## 出力例 (Example Output)

TypeScript プロジェクトで `/skill-create` を実行すると、以下のような出力が生成される可能性があります：

```markdown
---
name: my-app-patterns
description: my-app リポジトリからのコーディングパターン
version: 1.0.0
source: local-git-analysis
analyzed_commits: 150
---

# My App Patterns

## Commit Conventions

このプロジェクトは **conventional commits** を使用します：
- `feat:` - 新機能
- `fix:` - バグ修正
- `chore:` - メンテナンスタスク
- `docs:` - ドキュメント更新

## Code Architecture

```
src/
├── components/     # React コンポーネント（PascalCase.tsx）
├── hooks/          # カスタムフック（use*.ts）
├── utils/          # ユーティリティ関数
├── types/          # TypeScript 型定義
└── services/       # API と外部サービス
```

## Workflows

### Adding a New Component
1. `src/components/ComponentName.tsx` を作成
2. `src/components/__tests__/ComponentName.test.tsx` にテストを追加
3. `src/components/index.ts` からエクスポート

### Database Migration
1. `src/db/schema.ts` を変更
2. `pnpm db:generate` を実行
3. `pnpm db:migrate` を実行

## Testing Patterns

- テストファイル: `__tests__/` ディレクトリまたは `.test.ts` サフィックス
- カバレッジ目標: 80%+
- フレームワーク: Vitest
```

## GitHub App 統合 (GitHub App Integration)

高度な機能（10k+ コミット、チーム共有、自動 PR）については、[Skill Creator GitHub App](https://github.com/apps/skill-creator) を使用してください：

- インストール: [github.com/apps/skill-creator](https://github.com/apps/skill-creator)
- 任意の issue で `/skill-creator analyze` とコメント
- 生成されたスキルを含む PR を受け取る

## 関連コマンド (Related Commands)

- `/instinct-import` - 生成された instincts をインポート
- `/instinct-status` - 学習した instincts を表示
- `/evolve` - instincts をスキル/エージェントにクラスター化

---

*[Everything Claude Code](https://github.com/affaan-m/everything-claude-code) の一部*
