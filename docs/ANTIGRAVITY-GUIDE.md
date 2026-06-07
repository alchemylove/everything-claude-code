# Antigravity セットアップと利用ガイド (Antigravity Setup and Usage Guide)

Google の [Antigravity](https://antigravity.dev) は、設定に `.agent/` ディレクトリ規約を使う AI コーディング IDE です。ECC は selective install システムを通じて Antigravity を first-class でサポートしています。

## クイックスタート (Quick Start)

```bash
# Antigravity target で ECC をインストール
./install.sh --target antigravity typescript

# 複数の言語モジュール付きでも可
./install.sh --target antigravity typescript python go
```

これにより ECC コンポーネントがプロジェクトの `.agent/` ディレクトリにインストールされ、Antigravity が読み込める状態になります。

## インストールマッピングの仕組み (How the Install Mapping Works)

ECC はコンポーネント構造を Antigravity の期待レイアウトに合わせてリマップします:

| ECC Source | Antigravity Destination | 内容 (What It Contains) |
|------------|------------------------|------------------|
| `rules/` | `.agent/rules/` | 言語 rule とコーディング標準（フラット化） |
| `commands/` | `.agent/workflows/` | slash command は Antigravity workflow になる |
| `agents/` | `.agent/skills/` | agent 定義は Antigravity skill になる |

> **`.agents/` vs `.agent/` vs `agents/` について**: installer が明示的に扱う source path は3つのみです: `rules` → `.agent/rules/`、`commands` → `.agent/workflows/`、`agents`（ドットなし）→ `.agent/skills/`。ECC リポジトリ内のドット付き `.agents/` ディレクトリは Codex/Antigravity 向け skill 定義と `openai.yaml` 設定の**静的レイアウト**であり、installer では直接マップされません。`.agents/` パスはデフォルトの scaffold 操作にフォールスルーします。`.agents/skills/` の内容を Antigravity ランタイムで使いたい場合は、手動で `.agent/skills/` にコピーする必要があります。

### Claude Code との主な違い (Key Differences from Claude Code)

- **Rules はフラット化**: Claude Code は `rules/common/`、`rules/typescript/` のように rules をサブディレクトリにネストします。Antigravity はフラットな `rules/` を期待します — installer が自動で処理します。
- **Commands は workflows になる**: ECC の `/command` ファイルは `.agent/workflows/` に配置され、これが Antigravity における slash command 相当です。
- **Agents は skills になる**: ECC の agent 定義は `.agent/skills/` にマップされ、Antigravity が skill 設定を参照する場所です。

## インストール後のディレクトリ構造 (Directory Structure After Install)

```
your-project/
├── .agent/
│   ├── rules/
│   │   ├── coding-standards.md
│   │   ├── testing.md
│   │   ├── security.md
│   │   └── typescript.md          # language-specific rules
│   ├── workflows/
│   │   ├── plan.md
│   │   ├── code-review.md
│   │   ├── tdd.md
│   │   └── ...
│   ├── skills/
│   │   ├── planner.md
│   │   ├── code-reviewer.md
│   │   ├── tdd-guide.md
│   │   └── ...
│   └── ecc-install-state.json     # tracks what ECC installed
```

## `openai.yaml` Agent 設定 (The `openai.yaml` Agent Config)

`.agents/skills/` 配下の各 skill ディレクトリには、`.agents/skills/<skill-name>/agents/openai.yaml` に Antigravity 向け skill 設定を含む `agents/openai.yaml` があります:

```yaml
interface:
  display_name: "API Design"
  short_description: "REST API design patterns and best practices"
  brand_color: "#F97316"
  default_prompt: "Design REST API: resources, status codes, pagination"
policy:
  allow_implicit_invocation: true
```

| フィールド (Field) | 目的 (Purpose) |
|-------|---------|
| `display_name` | Antigravity UI に表示される人間可読名 |
| `short_description` | skill の簡潔な説明 |
| `brand_color` | skill の視覚バッジ用の hex 色 |
| `default_prompt` | skill を手動起動したときの推奨 prompt |
| `allow_implicit_invocation` | `true` のとき、Antigravity が context に基づいて skill を自動有効化できる |

## インストールの管理 (Managing Your Installation)

### インストール内容の確認 (Check What's Installed)

```bash
node scripts/list-installed.js --target antigravity
```

### 壊れたインストールの修復 (Repair a Broken Install)

```bash
# まず診断
node scripts/doctor.js --target antigravity

# 欠落または drift したファイルを復元
node scripts/repair.js --target antigravity
```

### アンインストール (Uninstall)

```bash
node scripts/uninstall.js --target antigravity
```

### Install State（インストール状態）

installer は `.agent/ecc-install-state.json` を書き込み、ECC が所有するファイルを追跡します。これにより安全な uninstall と repair が可能になり — ECC は自分が作っていないファイルには触れません。

## Antigravity 向けカスタム Skill の追加 (Adding Custom Skills for Antigravity)

新しい skill を貢献し Antigravity で使えるようにする場合:

1. 通常どおり `skills/your-skill-name/SKILL.md` に skill を作成
2. `agents/your-skill-name.md` に agent 定義を追加 — これが installer により `.agent/skills/` にマップされ、Antigravity harness で skill が使えるようになります
3. `.agents/skills/your-skill-name/agents/openai.yaml` に Antigravity agent 設定を追加 — Codex の implicit invocation メタデータ用の静的リポジトリレイアウトです
4. `SKILL.md` の内容を `.agents/skills/your-skill-name/SKILL.md` にミラー — Codex が使う静的コピーで、Antigravity の参照にもなります
5. PR で Antigravity サポートを追加したことを明記

> **重要な区別**: installer は `agents/`（ドットなし）→ `.agent/skills/` をデプロイします — これがランタイムで skill を使えるようにする部分です。`.agents/`（ドット付き）は Codex `openai.yaml` 用の別の静的レイアウトで、installer では自動デプロイされません。

詳細は [CONTRIBUTING.md](../CONTRIBUTING.md) を参照してください。

## 他 Target との比較 (Comparison with Other Targets)

| 機能 (Feature) | Claude Code | Cursor | Codex | Antigravity |
|---------|-------------|--------|-------|-------------|
| Install target | `claude-home` | `cursor-project` | `codex-home` | `antigravity` |
| Config root | `~/.claude/` | `.cursor/` | `~/.codex/` | `.agent/` |
| スコープ (Scope) | User-level | Project-level | User-level | Project-level |
| Rules format | Nested dirs | Flat | Flat | Flat |
| Commands | `commands/` | N/A | N/A | `workflows/` |
| Agents/Skills | `agents/` | N/A | N/A | `skills/` |
| Install state | `ecc-install-state.json` | `ecc-install-state.json` | `ecc-install-state.json` | `ecc-install-state.json` |

## トラブルシューティング (Troubleshooting)

### Antigravity で skill が読み込まれない (Skills not loading in Antigravity)

- プロジェクトルート（ホームディレクトリではない）に `.agent/` があることを確認
- `ecc-install-state.json` が作成されているか確認 — なければ installer を再実行
- ファイルが `.md` 拡張子で有効な frontmatter であることを確認

### Rule が適用されない (Rules not applying)

- Rule はサブディレクトリではなく `.agent/rules/` にある必要がある
- `node scripts/doctor.js --target antigravity` でインストールを検証

### Workflow が使えない (Workflows not available)

- Antigravity は `commands/` ではなく `.agent/workflows/` を参照する
- ECC command を手動コピーした場合はディレクトリ名を変更する

## 関連リソース (Related Resources)

- [Selective Install Architecture](./SELECTIVE-INSTALL-ARCHITECTURE.md) — install システムの内部動作
- [Selective Install Design](./SELECTIVE-INSTALL-DESIGN.md) — 設計判断と target adapter 契約
- [CONTRIBUTING.md](../CONTRIBUTING.md) — skill、agent、command の貢献方法
