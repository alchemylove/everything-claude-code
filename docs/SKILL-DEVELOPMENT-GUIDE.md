# Skill 開発ガイド (Skill Development Guide)

Everything Claude Code (ECC) 向けに効果的な skill を作成するための包括的ガイドです。

## 目次 (Table of Contents)

- [Skill とは?](#what-are-skills)
- [Skill のアーキテクチャ](#skill-architecture)
- [最初の Skill を作成する](#creating-your-first-skill)
- [Skill のカテゴリ](#skill-categories)
- [効果的な Skill コンテンツを書く](#writing-effective-skill-content)
- [ベストプラクティス](#best-practices)
- [共通パターン](#common-patterns)
- [Skill をテストする](#testing-your-skill)
- [Skill を提出する](#submitting-your-skill)
- [サンプルギャラリー](#examples-gallery)

---

<a id="what-are-skills"></a>
## Skill とは? (What Are Skills?)

Skill は、Claude Code がコンテキストに応じて読み込む**知識モジュール**です。skill は次のものを提供します。

- **ドメイン専門知識**: フレームワークのパターン、言語イディオム、ベストプラクティス
- **ワークフロー定義**: 一般的なタスク向けの段階的な手順
- **参考資料**: コードスニペット、チェックリスト、意思決定ツリー
- **コンテキスト注入**: 特定の条件に一致したときに有効化

**agent**（専門特化したサブアシスタント）や **command**（ユーザー起点のアクション）と異なり、skill は Claude Code が関連時に参照する受動的な知識です。

### Skill が有効化されるタイミング (When Skills Activate)

skill は次の場合に有効化されます。

- ユーザーのタスクが skill のドメインに一致する
- Claude Code が関連するコンテキストを検出する
- command が skill を参照する
- agent がドメイン知識を必要とする

### Skill と Agent と Command の違い (Skill vs Agent vs Command)

| コンポーネント | 目的 | 有効化方法 |
|-----------|---------|------------|
| **Skill** | 知識リポジトリ | コンテキストベース（自動） |
| **Agent** | タスク実行者 | 明示的な委譲 |
| **Command** | ユーザーアクション | ユーザー起動（`/command`） |
| **Hook** | 自動化 | イベントトリガー |
| **Rule** | 常時適用ガイドライン | 常に有効 |

---

<a id="skill-architecture"></a>
## Skill のアーキテクチャ (Skill Architecture)

### ファイル構成 (File Structure)

```
skills/
└── your-skill-name/
    ├── SKILL.md           # Required: Main skill definition
    ├── examples/          # Optional: Code examples
    │   ├── basic.ts
    │   └── advanced.ts
    └── references/        # Optional: External references
        └── links.md
```

### SKILL.md の形式 (SKILL.md Format)

```markdown
---
name: skill-name
description: Brief description shown in skill list and used for auto-activation
origin: ECC
---

# Skill Title

Brief overview of what this skill covers.

## When to Activate

Describe scenarios where Claude should use this skill.

## Core Concepts

Main patterns and guidelines.

## Code Examples

\`\`\`typescript
// Practical, tested examples
\`\`\`

## Anti-Patterns

Show what NOT to do with concrete examples.

## Best Practices

- Actionable guidelines
- Do's and don'ts

## Related Skills

Link to complementary skills.
```

### YAML Frontmatter のフィールド (YAML Frontmatter Fields)

| フィールド | 必須 | 説明 |
|-------|----------|-------------|
| `name` | はい | 小文字のハイフン区切り識別子（例: `react-patterns`） |
| `description` | はい | skill 一覧表示と自動有効化に使う 1 行説明 |
| `origin` | いいえ | 出所を示す識別子（例: `ECC`, `community`, project name） |
| `tags` | いいえ | カテゴリ分け用のタグ配列 |
| `version` | いいえ | 更新追跡用の skill バージョン |

---

<a id="creating-your-first-skill"></a>
## 最初の Skill を作成する (Creating Your First Skill)

### ステップ 1: 焦点を選ぶ (Step 1: Choose a Focus)

良い skill は**焦点が明確で、実行可能**です。

| PASS: 良い焦点 | FAIL: 広すぎる |
|---------------|--------------|
| `react-hook-patterns` | `react` |
| `postgresql-indexing` | `databases` |
| `pytest-fixtures` | `python-testing` |
| `nextjs-app-router` | `nextjs` |

### ステップ 2: ディレクトリを作成する (Step 2: Create the Directory)

```bash
mkdir -p skills/your-skill-name
```

### ステップ 3: SKILL.md を書く (Step 3: Write SKILL.md)

最小構成のテンプレートは次のとおりです。

```markdown
---
name: your-skill-name
description: Brief description of when to use this skill
---

# Your Skill Title

Brief overview (1-2 sentences).

## When to Activate

- Scenario 1
- Scenario 2
- Scenario 3

## Core Concepts

### Concept 1

Explanation with examples.

### Concept 2

Another pattern with code.

## Code Examples

\`\`\`typescript
// Practical example
\`\`\`

## Best Practices

- Do this
- Avoid that

## Related Skills

- `related-skill-1`
- `related-skill-2`
```

### ステップ 4: コンテンツを追加する (Step 4: Add Content)

Claude が**すぐに使える**内容を書いてください。

- PASS: そのままコピーして使えるコード例
- PASS: 明確な意思決定ツリー
- PASS: 検証用のチェックリスト
- FAIL: 例のない曖昧な説明
- FAIL: 実行可能なガイダンスのない長文説明

---

<a id="skill-categories"></a>
## Skill のカテゴリ (Skill Categories)

### 言語標準 (Language Standards)

イディオマティックなコード、命名規則、言語固有のパターンに焦点を当てます。

**例:** `python-patterns`, `golang-patterns`, `typescript-standards`

```markdown
---
name: python-patterns
description: Python idioms, best practices, and patterns for clean, idiomatic code.
---

# Python Patterns

## When to Activate

- Writing Python code
- Refactoring Python modules
- Python code review

## Core Concepts

### Context Managers

\`\`\`python
# Always use context managers for resources
with open('file.txt') as f:
    content = f.read()
\`\`\`
```

### フレームワークパターン (Framework Patterns)

フレームワーク固有の規約、一般的なパターン、アンチパターンに焦点を当てます。

**例:** `django-patterns`, `nextjs-patterns`, `springboot-patterns`

```markdown
---
name: django-patterns
description: Django best practices for models, views, URLs, and templates.
---

# Django Patterns

## When to Activate

- Building Django applications
- Creating models and views
- Django URL configuration
```

### ワークフロー Skill (Workflow Skills)

一般的な開発タスク向けの段階的な手順を定義します。

**例:** `tdd-workflow`, `code-review-workflow`, `deployment-checklist`

```markdown
---
name: code-review-workflow
description: Systematic code review process for quality and security.
---

# Code Review Workflow

## Steps

1. **Understand Context** - Read PR description and linked issues
2. **Check Tests** - Verify test coverage and quality
3. **Review Logic** - Analyze implementation for correctness
4. **Check Security** - Look for vulnerabilities
5. **Verify Style** - Ensure code follows conventions
```

### ドメイン知識 (Domain Knowledge)

特定のドメイン向けの専門知識です（security、performance など）。

**例:** `security-review`, `performance-optimization`, `api-design`

```markdown
---
name: api-design
description: REST and GraphQL API design patterns, versioning, and best practices.
---

# API Design Patterns

## RESTful Conventions

| Method | Endpoint | Purpose |
|--------|----------|---------|
| GET | /resources | List all |
| GET | /resources/:id | Get one |
| POST | /resources | Create |
```

### ツール統合 (Tool Integration)

特定のツール、ライブラリ、サービスの利用ガイダンスです。

**例:** `supabase-patterns`, `docker-patterns`, `mcp-server-patterns`

---

<a id="writing-effective-skill-content"></a>
## 効果的な Skill コンテンツを書く (Writing Effective Skill Content)

### 1. "When to Activate" から始める (1. Start with "When to Activate")

このセクションは自動有効化において**極めて重要**です。具体的に書いてください。

```markdown
## When to Activate

- Creating new React components
- Refactoring existing components
- Debugging React state issues
- Reviewing React code for best practices
```

### 2. "Show, Don't Tell" を使う (2. Use "Show, Don't Tell")

悪い例:

```markdown
## Error Handling

Always handle errors properly in async functions.
```

良い例:

```markdown
## Error Handling

\`\`\`typescript
async function fetchData(url: string) {
  try {
    const response = await fetch(url)

    if (!response.ok) {
      throw new Error(\`HTTP \${response.status}: \${response.statusText}\`)
    }

    return await response.json()
  } catch (error) {
    console.error('Fetch failed:', error)
    throw new Error('Failed to fetch data')
  }
}
\`\`\`

### Key Points

- Check \`response.ok\` before parsing
- Log errors for debugging
- Re-throw with user-friendly message
```

### 3. アンチパターンを含める (3. Include Anti-Patterns)

やってはいけないことも示してください。

```markdown
## Anti-Patterns

### FAIL: Direct State Mutation

\`\`\`typescript
// NEVER do this
user.name = 'New Name'
items.push(newItem)
\`\`\`

### PASS: Immutable Updates

\`\`\`typescript
// ALWAYS do this
const updatedUser = { ...user, name: 'New Name' }
const updatedItems = [...items, newItem]
\`\`\`
```

### 4. チェックリストを用意する (4. Provide Checklists)

チェックリストは実行しやすく、追いやすい形式です。

```markdown
## Pre-Deployment Checklist

- [ ] All tests passing
- [ ] No console.log in production code
- [ ] Environment variables documented
- [ ] Secrets not hardcoded
- [ ] Error handling complete
- [ ] Input validation in place
```

### 5. 意思決定ツリーを使う (5. Use Decision Trees)

複雑な判断が必要な場合には次のようにします。

```markdown
## Choosing the Right Approach

\`\`\`
Need to fetch data?
├── Single request → use fetch directly
├── Multiple independent → Promise.all()
├── Multiple dependent → await sequentially
└── With caching → use SWR or React Query
\`\`\`
```

---

<a id="best-practices"></a>
## ベストプラクティス (Best Practices)

### DO (DO)

| プラクティス | 例 |
|----------|---------|
| **具体的に書く** | "Use \`useCallback\` for event handlers passed to child components" |
| **例を見せる** | そのまま使えるコードを含める |
| **WHY を説明する** | "Immutability prevents unexpected side effects in React state" |
| **関連 skill をリンクする** | "See also: \`react-performance\`" |
| **焦点を保つ** | 1 skill = 1 つのドメイン/概念 |
| **セクションを使う** | ひと目で追える明確な見出し |

### DON'T (DON'T)

| プラクティス | 良くない理由 |
|----------|--------------|
| **曖昧に書く** | "Write good code" のように実行可能でない |
| **長文説明に偏る** | 解析しづらく、コードの方が伝わりやすい |
| **盛り込みすぎる** | "Python, Django, and Flask patterns" のように広すぎる |
| **例を省略する** | 実践のない理論は有用性が下がる |
| **アンチパターンを無視する** | やってはいけない例を学ぶことにも価値がある |

### コンテンツガイドライン (Content Guidelines)

1. **長さ**: 通常は 200-500 行、最大 800 行
2. **コードブロック**: 言語識別子を付ける
3. **見出し**: `##` と `###` の階層を使う
4. **リスト**: unordered は `-`、ordered は `1.` を使う
5. **表**: 比較や参照に使う

---

<a id="common-patterns"></a>
## 共通パターン (Common Patterns)

### パターン 1: Standards Skill (Pattern 1: Standards Skill)

```markdown
---
name: language-standards
description: Coding standards and best practices for [language].
---

# [Language] Coding Standards

## When to Activate

- Writing [language] code
- Code review
- Setting up linting

## Naming Conventions

| Element | Convention | Example |
|---------|------------|---------|
| Variables | camelCase | userName |
| Constants | SCREAMING_SNAKE | MAX_RETRY |
| Functions | camelCase | fetchUser |
| Classes | PascalCase | UserService |

## Code Examples

[Include practical examples]

## Linting Setup

[Include configuration]

## Related Skills

- `language-testing`
- `language-security`
```

### パターン 2: Workflow Skill (Pattern 2: Workflow Skill)

```markdown
---
name: task-workflow
description: Step-by-step workflow for [task].
---

# [Task] Workflow

## When to Activate

- [Trigger 1]
- [Trigger 2]

## Prerequisites

- [Requirement 1]
- [Requirement 2]

## Steps

### Step 1: [Name]

[Description]

\`\`\`bash
[Commands]
\`\`\`

### Step 2: [Name]

[Description]

## Verification

- [ ] [Check 1]
- [ ] [Check 2]

## Troubleshooting

| Problem | Solution |
|---------|----------|
| [Issue] | [Fix] |
```

### パターン 3: Reference Skill (Pattern 3: Reference Skill)

```markdown
---
name: api-reference
description: Quick reference for [API/Library].
---

# [API/Library] Reference

## When to Activate

- Using [API/Library]
- Looking up [API/Library] syntax

## Common Operations

### Operation 1

\`\`\`typescript
// Basic usage
\`\`\`

### Operation 2

\`\`\`typescript
// Advanced usage
\`\`\`

## Configuration

[Include config examples]

## Error Handling

[Include error patterns]
```

---

<a id="testing-your-skill"></a>
## Skill をテストする (Testing Your Skill)

### ローカルテスト (Local Testing)

1. **Claude Code の skills ディレクトリへコピーする**:
   ```bash
   cp -r skills/your-skill-name ~/.claude/skills/
   ```

2. **Claude Code でテストする**:
   ```
   You: "I need to [task that should trigger your skill]"

   Claude should reference your skill's patterns.
   ```

3. **有効化を確認する**:
   - Claude に skill 内の概念を説明させる
   - 例やパターンが使われているか確認する
   - ガイドラインに従っていることを確認する

### 検証チェックリスト (Validation Checklist)

- [ ] **YAML frontmatter が有効** - 構文エラーがない
- [ ] **name が規約に従う** - lowercase-with-hyphens
- [ ] **description が明確** - いつ使うかが伝わる
- [ ] **例が動作する** - コードがコンパイル・実行できる
- [ ] **リンクが有効** - 関連 skill が存在する
- [ ] **機微情報がない** - API keys、tokens、paths を含まない

### コード例のテスト (Code Example Testing)

すべてのコード例をテストしてください。

```bash
# From the repo root
npx tsc --noEmit skills/your-skill-name/examples/*.ts

# Or from inside the skill directory
npx tsc --noEmit examples/*.ts

# From the repo root
python -m py_compile skills/your-skill-name/examples/*.py

# Or from inside the skill directory
python -m py_compile examples/*.py

# From the repo root
go build ./skills/your-skill-name/examples/...

# Or from inside the skill directory
go build ./examples/...
```

---

<a id="submitting-your-skill"></a>
## Skill を提出する (Submitting Your Skill)

### 1. Fork と Clone (1. Fork and Clone)

```bash
gh repo fork affaan-m/everything-claude-code --clone
cd everything-claude-code
```

### 2. ブランチを作成する (2. Create Branch)

```bash
git checkout -b feat/skill-your-skill-name
```

### 3. Skill を追加する (3. Add Your Skill)

```bash
mkdir -p skills/your-skill-name
# Create SKILL.md
```

### 4. 検証する (4. Validate)

```bash
# Check YAML frontmatter
head -10 skills/your-skill-name/SKILL.md

# Verify structure
ls -la skills/your-skill-name/

# Run tests if available
npm test
```

### 5. Commit と Push (5. Commit and Push)

```bash
git add skills/your-skill-name/
git commit -m "feat(skills): add your-skill-name skill"
git push -u origin feat/skill-your-skill-name
```

### 6. Pull Request を作成する (6. Create Pull Request)

次の PR テンプレートを使ってください。

```markdown
## 概要 (Summary)

skill の概要と、その価値が分かる簡潔な説明。

## Skill 種別 (Skill Type)

- [ ] 言語標準 (Language standards)
- [ ] フレームワークパターン (Framework patterns)
- [ ] ワークフロー (Workflow)
- [ ] ドメイン知識 (Domain knowledge)
- [ ] ツール統合 (Tool integration)

## テスト (Testing)

この skill をローカルでどう検証したか。

## チェックリスト (Checklist)

- [ ] YAML frontmatter が有効
- [ ] コード例をテスト済み
- [ ] skill ガイドラインに準拠
- [ ] 機微情報を含まない
- [ ] 有効化トリガーが明確
```

---

<a id="examples-gallery"></a>
## サンプルギャラリー (Examples Gallery)

### 例 1: 言語標準 (Example 1: Language Standards)

**ファイル:** `skills/rust-patterns/SKILL.md`

```markdown
---
name: rust-patterns
description: Rust idioms, ownership patterns, and best practices for safe, idiomatic code.
origin: ECC
---

# Rust Patterns

## When to Activate

- Writing Rust code
- Handling ownership and borrowing
- Error handling with Result/Option
- Implementing traits

## Ownership Patterns

### Borrowing Rules

\`\`\`rust
// PASS: CORRECT: Borrow when you don't need ownership
fn process_data(data: &str) -> usize {
    data.len()
}

// PASS: CORRECT: Take ownership when you need to modify or consume
fn consume_data(data: Vec<u8>) -> String {
    String::from_utf8(data).unwrap()
}
\`\`\`

## Error Handling

### Result Pattern

\`\`\`rust
use thiserror::Error;

#[derive(Error, Debug)]
pub enum AppError {
    #[error("IO error: {0}")]
    Io(#[from] std::io::Error),

    #[error("Parse error: {0}")]
    Parse(#[from] std::num::ParseIntError),
}

pub type AppResult<T> = Result<T, AppError>;
\`\`\`

## Related Skills

- `rust-testing`
- `rust-security`
```

### 例 2: フレームワークパターン (Example 2: Framework Patterns)

**ファイル:** `skills/fastapi-patterns/SKILL.md`

```markdown
---
name: fastapi-patterns
description: FastAPI patterns for routing, dependency injection, validation, and async operations.
origin: ECC
---

# FastAPI Patterns

## When to Activate

- Building FastAPI applications
- Creating API endpoints
- Implementing dependency injection
- Handling async database operations

## Project Structure

\`\`\`
app/
├── main.py              # FastAPI app entry point
├── routers/             # Route handlers
│   ├── users.py
│   └── items.py
├── models/              # Pydantic models
│   ├── user.py
│   └── item.py
├── services/            # Business logic
│   └── user_service.py
└── dependencies.py      # Shared dependencies
\`\`\`

## Dependency Injection

\`\`\`python
from fastapi import Depends
from sqlalchemy.ext.asyncio import AsyncSession

async def get_db() -> AsyncSession:
    async with AsyncSessionLocal() as session:
        yield session

@router.get("/users/{user_id}")
async def get_user(
    user_id: int,
    db: AsyncSession = Depends(get_db)
):
    # Use db session
    pass
\`\`\`

## Related Skills

- `python-patterns`
- `pydantic-validation`
```

### 例 3: ワークフロー Skill (Example 3: Workflow Skill)

**ファイル:** `skills/refactoring-workflow/SKILL.md`

```markdown
---
name: refactoring-workflow
description: Systematic refactoring workflow for improving code quality without changing behavior.
origin: ECC
---

# Refactoring Workflow

## When to Activate

- Improving code structure
- Reducing technical debt
- Simplifying complex code
- Extracting reusable components

## Prerequisites

- All tests passing
- Git working directory clean
- Feature branch created

## Workflow Steps

### Step 1: Identify Refactoring Target

- Look for code smells (long methods, duplicate code, large classes)
- Check test coverage for target area
- Document current behavior

### Step 2: Ensure Tests Exist

\`\`\`bash
# Run tests to verify current behavior
npm test

# Check coverage for target files
npm run test:coverage
\`\`\`

### Step 3: Make Small Changes

- One refactoring at a time
- Run tests after each change
- Commit frequently

### Step 4: Verify Behavior Unchanged

\`\`\`bash
# Run full test suite
npm test

# Run E2E tests
npm run test:e2e
\`\`\`

## Common Refactorings

| Smell | Refactoring |
|-------|-------------|
| Long method | Extract method |
| Duplicate code | Extract to shared function |
| Large class | Extract class |
| Long parameter list | Introduce parameter object |

## Checklist

- [ ] Tests exist for target code
- [ ] Made small, focused changes
- [ ] Tests pass after each change
- [ ] Behavior unchanged
- [ ] Committed with clear message
```

---

## 追加リソース (Additional Resources)

- [CONTRIBUTING.md](../CONTRIBUTING.md) - 一般的なコントリビューションガイドライン
- [project-guidelines-template](./examples/project-guidelines-template.md) - プロジェクト固有の skill テンプレート
- [coding-standards](../skills/coding-standards/SKILL.md) - standards skill の例
- [tdd-workflow](../skills/tdd-workflow/SKILL.md) - workflow skill の例
- [security-review](../skills/security-review/SKILL.md) - domain knowledge skill の例

---

**忘れないでください**: 良い skill は、焦点が明確で、実行可能で、すぐに役立つものです。自分自身が使いたくなる skill を書いてください。
