# リポジトリとフォーク評価 + セットアップ推奨 (Repo & Fork Assessment + Setup Recommendations)

**日付:** 2026-03-21

---

## 利用可能なもの (What's Available)

### リポジトリ: `Infiniteyieldai/everything-claude-code`

これは **`affaan-m/everything-claude-code` のフォーク**（50K+ スター、6K+ フォークの上流プロジェクト）です。

| 属性 | 値 |
|-----------|-------|
| バージョン | 1.9.0（現行） |
| 状態 | クリーンなフォーク — 上流 `main` より 1 コミット先行（このセッションで追加した EVALUATION.md） |
| リモートブランチ | `main`、`claude/evaluate-repo-comparison-ASZ9Y` |
| 上流同期 | 完全同期 — 最後にマージした上流コミットは zh-CN ドキュメント PR (#728) |
| ライセンス | MIT |

**作業にはこのリポジトリが適切です。** 最新の上流バージョンで、分岐やマージコンフリクトはありません。

---

### 現在の `~/.claude/` インストール

| コンポーネント | インストール済み | リポジトリで利用可能 |
|-----------|-----------|-------------------|
| エージェント | 0 | 28 |
| スキル | 0 | 116 |
| コマンド | 0 | 59 |
| ルール | 0 | 60+ ファイル（12 言語） |
| フック | 1（git Stop チェック） | 完全な PreToolUse/PostToolUse マトリクス |
| MCP 設定 | 0 | 1（Context7） |

既存の Stop フック（`stop-hook-git-check.sh`）は堅牢です — 未コミット/未プッシュの作業があるとセッション終了をブロックします。維持してください。

---

## インストールプロファイル推奨 (Install Profile Recommendations)

リポジトリは 5 つのインストールプロファイルを同梱しています。主な用途に応じて選択してください：

### プロファイル: `core`（最小限の実用セットアップ）(Profile: `core` (Minimum viable setup))
> 最速インストール。コマンド、コアエージェント、フック runtime、品質ワークフローを取得。

**最適な用途:** ECC を試す、最小フットプリント、制約のある環境。

```bash
node scripts/install-plan.js --profile core
node scripts/install-apply.js
```

**インストール内容:** rules-core、agents-core、commands-core、hooks-runtime、platform-configs、workflow-quality

---

### プロファイル: `developer`（日常開発向け推奨）(Profile: `developer` (Recommended for daily dev work))
> 多くの ECC ユーザー向けのデフォルトエンジニアリングプロファイル。

**最適な用途:** アプリコードベース横断の一般的なソフトウェア開発。

```bash
node scripts/install-plan.js --profile developer
node scripts/install-apply.js
```

**core からの追加:** framework-language スキル、database パターン、orchestration コマンド

---

### プロファイル: `security` (Profile: `security`)
> ベースライン runtime + セキュリティ特化のエージェントとルール。

**最適な用途:** セキュリティ重視のワークフロー、コード監査、脆弱性レビュー。

---

### プロファイル: `research` (Profile: `research`)
> 調査、統合、公開ワークフロー。

**最適な用途:** コンテンツ作成、投資家向け資料、市場調査、クロスポスト。

---

### プロファイル: `full` (Profile: `full`)
> すべて — 全 18 モジュール。

**最適な用途:** 完全なツールキットを求めるパワーユーザー。

```bash
node scripts/install-plan.js --profile full
node scripts/install-apply.js
```

---

## 優先追加（高価値・低リスク）(Priority Additions (High Value, Low Risk))

プロファイルに関わらず、これらのコンポーネントは即効性があります：

### 1. コアエージェント（最高 ROI）(1. Core Agents (highest ROI))

| エージェント | 重要な理由 |
|-------|----------------|
| `planner.md` | 複雑なタスクを実装計画に分解 |
| `code-reviewer.md` | 品質と保守性のレビュー |
| `tdd-guide.md` | TDD ワークフロー（RED→GREEN→IMPROVE） |
| `security-reviewer.md` | 脆弱性検出 |
| `architect.md` | システム設計とスケーラビリティ判断 |

### 2. 主要コマンド (2. Key Commands)

| コマンド | 重要な理由 |
|---------|----------------|
| `/plan` | コーディング前の実装計画 |
| `/tdd` | テスト駆動ワークフロー |
| `/code-review` | オンデマンドレビュー |
| `/build-fix` | ビルドエラーの自動解決 |
| `/learn` | 現在のセッションからパターンを抽出 |

### 3. フックアップグレード（`hooks/hooks.json` から）(3. Hook Upgrades (from `hooks/hooks.json`))
リポジトリのフックシステムは、現在の単一 Stop フックに以下を追加します：

| フック | トリガー | 価値 |
|------|---------|-------|
| `block-no-verify` | PreToolUse: Bash | `--no-verify` git フラグの悪用をブロック |
| `pre-bash-git-push-reminder` | PreToolUse: Bash | プッシュ前レビューリマインダー |
| `doc-file-warning` | PreToolUse: Write | 非標準ドキュメントファイルへの警告 |
| `suggest-compact` | PreToolUse: Edit/Write | 論理的な間隔でのコンパクション提案 |
| Continuous learning observer | PreToolUse: * | スキル改善のためのツール利用パターンを捕捉 |

### 4. ルール（常時ガイドライン）(4. Rules (Always-on guidelines))
`rules/common/` は毎セッション適用されるベースラインガイドラインを提供します：
- `security.md` — セキュリティガードレール
- `testing.md` — 80%+ カバレッジ要件
- `git-workflow.md` — Conventional Commits、ブランチ戦略
- `coding-style.md` — 言語横断のスタイル標準

---

## フォークの扱い (What to Do With the Fork)

### オプション A: 上流トラッカーとして使用（現状）(Option A: Use as upstream tracker (current state))
フォークを `affaan-m/everything-claude-code` 上流と同期したまま維持。定期的に上流変更をマージ：
```bash
git fetch upstream
git merge upstream/main
```
ローカルクローンからインストール。クリーンで保守しやすい。

### オプション B: フォークをカスタマイズ (Option B: Customize the fork)
個人のスキル、エージェント、コマンドをフォークに追加。以下に適しています：
- ビジネス固有のドメインスキル（自社の垂直領域）
- チーム固有のコーディング規約
- 自スタック向けカスタムフック

フォークには既に EVALUATION.md と REPO-ASSESSMENT.md があります — 作業用フォークとして問題ありません。

### オプション C: npm からインストール（新規マシンで最も簡単）(Option C: Install from npm (simplest for fresh machines))
```bash
npx ecc-universal install --profile developer
```
リポジトリのクローンは不要。多くのユーザーには推奨インストール方法です。

---

## 推奨セットアップ手順 (Recommended Setup Steps)

1. **既存の Stop フックを維持** — 役割を果たしている
2. **ローカルフォークから developer プロファイルをインストール**:
   ```bash
   cd /path/to/everything-claude-code
   node scripts/install-plan.js --profile developer
   node scripts/install-apply.js
   ```
3. **主スタック向けに言語ルールを追加**（TypeScript、Python、Go など）:
   ```bash
   node scripts/install-plan.js --add rules/typescript
   node scripts/install-apply.js
   ```
4. **ライブドキュメント参照のため MCP Context7 を有効化**:
   - `mcp-configs/mcp-servers.json` をプロジェクトの `.claude/` にコピー
5. **フックをレビュー** — `block-no-verify` と `pre-bash-git-push-reminder` から始めて `hooks/hooks.json` の追加を選択的に有効化

---

## サマリー (Summary)

| 質問 | 回答 |
|----------|--------|
| フォークは健全か？ | はい — 上流 v1.9.0 と完全同期 |
| 検討すべき他のフォークは？ | この環境では見当たらない。上流 `affaan-m/everything-claude-code` が正 |
| 最適なインストールプロファイルは？ | 日常開発には `developer` |
| 現在のセットアップの最大ギャップは？ | エージェント 0 件 — 最低限 planner、code-reviewer、tdd-guide、security-reviewer を追加 |
| 最速の勝ちは？ | `node scripts/install-plan.js --profile core && node scripts/install-apply.js` を実行 |
