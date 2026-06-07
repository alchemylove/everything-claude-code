---
name: codebase-onboarding
description: 不慣れなコードベースを分析し、構造化オンボーディングガイド（アーキテクチャマップ、主要エントリポイント、規約、スターター CLAUDE.md）を生成。新規プロジェクト参加時やリポで Claude Code 初セットアップ時。codebase onboarding, CLAUDE.md.
origin: ECC
---

# コードベースオンボーディング (Codebase Onboarding)

体系的に不慣れなコードベースを分析し、構造化オンボーディングガイドを作成。新しいプロジェクトに参加するか、既存リポでClaude Codeを初めてセットアップする開発者向けに設計。

## 使用時期 (When to Use)

- Claude Codeでプロジェクトを初めて開く
- 新しいチームまたはリポに参加
- ユーザーが「このコードベースを理解する手助けをしてください」と求める
- ユーザーがプロジェクトのCLAUDE.mdを生成するよう要求
- ユーザーが「オンボード」または「このリポを説明」と言う

## 動作方法 (How It Works)

### フェーズ1：偵察 (Phase 1: Reconnaissance)

すべてのファイルを読まずにプロジェクトについての生の信号を集めます。これらのチェックを並行して実行：

```
1. パッケージマニフェスト検出
   → package.json, go.mod, Cargo.toml, pyproject.toml, pom.xml

2. フレームワークフィンガープリント
   → next.config、nuxt.config、angular.json、vite.config

3. エントリポイント識別
   → main.*、index.*、app.*、server.*

4. ディレクトリ構造スナップショット
   → ディレクトリツリーの最上位2レベル

5. コンフィグとツール検出
   → .eslintrc、.prettierrc、tsconfig.json、Dockerfile

6. テスト構造検出
   → tests/、__tests__/、*.spec.ts、jest.config.*
```

### フェーズ2：アーキテクチャマップ (Phase 2: Architecture Mapping)

主要なモジュールとそれらの関係を特定します。

### フェーズ3：規約とスタイル (Phase 3: Convention Detection)

コード規約、命名パターン、プロジェクト固有のパターンを特定。

### 出力 (Phase 4: Generate Onboarding Artifacts)

- アーキテクチャマップ
- 主要なエントリポイントと流れ
- 規約とスタイルガイド
- スターターCLAUDE.md
