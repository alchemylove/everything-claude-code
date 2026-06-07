# ルール (Rules)

## 構造 (Structure)

ルールは **common** レイヤーと **言語固有** のディレクトリで構成されています:

```
rules/
├── common/          # 言語非依存の原則（常にインストール）
│   ├── coding-style.md
│   ├── git-workflow.md
│   ├── testing.md
│   ├── performance.md
│   ├── patterns.md
│   ├── hooks.md
│   ├── agents.md
│   └── security.md
├── typescript/      # TypeScript/JavaScript 固有
├── angular/         # Angular 固有
├── python/          # Python 固有
├── golang/          # Go 固有
├── web/             # Web およびフロントエンド固有
├── swift/           # Swift 固有
├── php/             # PHP 固有
├── ruby/            # Ruby / Rails 固有
└── arkts/           # HarmonyOS / ArkTS 固有
```

- **common/** には普遍的な原則が含まれます — 言語固有のコード例はありません。
- **言語ディレクトリ** は、フレームワーク固有のパターン、ツール、コード例で common ルールを拡張します。各ファイルは対応する common ファイルを参照します。

## インストール (Installation)

### オプション 1: インストールスクリプト（推奨）(Option 1: Install Script (Recommended))

```bash
# Install common + one or more language-specific rule sets
./install.sh typescript
./install.sh angular
./install.sh python
./install.sh golang
./install.sh web
./install.sh swift
./install.sh php
./install.sh ruby
./install.sh arkts

# Install multiple languages at once
./install.sh typescript python
```

### オプション 2: 手動インストール (Option 2: Manual Installation)

> **重要:** ディレクトリ全体をコピーしてください — `/*` でフラット化しないでください。
> common と言語固有のディレクトリには同名のファイルが含まれています。
> 1 つのディレクトリにフラット化すると、言語固有のファイルが common ルールを上書きし、
> 言語固有ファイルが使用する相対パス `../common/` の参照が壊れます。
>
> ユーザー向け Claude インストールには、以下の ECC 所有の namespace を使用してください。フラットな
> パッケージレベルの配置先は非 ECC ルールパックと衝突し、
> メイン README のガイダンスとも一致しません。

```bash
# Create the ECC rule namespace once.
mkdir -p ~/.claude/rules/ecc

# Install common rules (required for all projects)
cp -r rules/common ~/.claude/rules/ecc/

# Install language-specific rules based on your project's tech stack
cp -r rules/typescript ~/.claude/rules/ecc/
cp -r rules/angular ~/.claude/rules/ecc/
cp -r rules/python ~/.claude/rules/ecc/
cp -r rules/golang ~/.claude/rules/ecc/
cp -r rules/web ~/.claude/rules/ecc/
cp -r rules/swift ~/.claude/rules/ecc/
cp -r rules/php ~/.claude/rules/ecc/
cp -r rules/ruby ~/.claude/rules/ecc/
cp -r rules/arkts ~/.claude/rules/ecc/

# Attention ! ! ! Configure according to your actual project requirements; the configuration here is for reference only.
```

プロジェクトローカルのルールには、プロジェクトルート配下で同じ namespace を使用します:

```bash
mkdir -p .claude/rules/ecc
cp -r rules/common .claude/rules/ecc/
cp -r rules/typescript .claude/rules/ecc/
```

## ルール vs スキル (Rules vs Skills)

- **Rules** は広く適用される標準、規約、チェックリストを定義します（例: 「80% テストカバレッジ」「ハードコードされた secret 禁止」）。
- **Skills**（`skills/` ディレクトリ）は、特定タスク向けの深い実践的リファレンスを提供します（例: `python-patterns`、`golang-testing`）。

言語固有のルールファイルは、適切な箇所で関連スキルを参照します。ルールは *何を* すべきかを示し、スキルは *どう* すべきかを示します。

## 新しい言語の追加 (Adding a New Language)

新しい言語（例: `rust/`）のサポートを追加するには:

1. `rules/rust/` ディレクトリを作成する
2. common ルールを拡張するファイルを追加する:
   - `coding-style.md` — フォーマッタ、イディオム、エラーハンドリングパターン
   - `testing.md` — テストフレームワーク、カバレッジツール、テスト構成
   - `patterns.md` — 言語固有のデザインパターン
   - `hooks.md` — フォーマッタ、リンター、型チェッカー向け PostToolUse hooks
   - `security.md` — secret 管理、セキュリティスキャンツール
3. 各ファイルは次のように始める:
   ```
   > This file extends [common/xxx.md](../common/xxx.md) with <Language> specific content.
   ```
4. 既存のスキルがあれば参照し、なければ `skills/` 配下に新規作成する。

`web/` のような非言語ドメインでも、再利用可能なドメイン固有ガイダンスが十分にある場合は、同じレイヤードパターンに従って standalone ruleset を検討してください。

## ルールの優先順位 (Rule Priority)

言語固有ルールと common ルールが矛盾する場合、**言語固有ルールが優先**されます（具体は一般を上書き）。これは標準的なレイヤード設定パターン（CSS の詳細度や `.gitignore` の優先順位と同様）に従います。

- `rules/common/` はすべてのプロジェクトに適用される普遍的なデフォルトを定義します。
- `rules/golang/`、`rules/python/`、`rules/swift/`、`rules/php/`、`rules/typescript/` などは、言語のイディオムが異なる箇所でそれらのデフォルトを上書きします。

### 例 (Example)

`common/coding-style.md` は不変性をデフォルト原則として推奨します。言語固有の `golang/coding-style.md` はこれを上書きできます:

> Idiomatic Go uses pointer receivers for struct mutation — see [common/coding-style.md](../common/coding-style.md) for the general principle, but Go-idiomatic mutation is preferred here.

### 上書き注記付き common ルール (Common rules with override notes)

言語固有ファイルによって上書きされうる `rules/common/` のルールには、次の注記が付いています:

> **Language note**: This rule may be overridden by language-specific rules for languages where this pattern is not idiomatic.
