# アーキテクチャ改善の推奨事項 (Architecture Improvement Recommendations)

このドキュメントは Everything Claude Code (ECC) プロジェクト向けのアーキテクトレベルの改善を記録します。保守性、一貫性、長期的な品質向上を目指す Claude Code コーディングアーキテクトの視点で書かれています。

---

## 1. ドキュメントと Single Source of Truth (Documentation and Single Source of Truth)

### 1.1 Agent / Command / Skill 件数の同期 (Agent / Command / Skill Count Sync)

**課題:** AGENTS.md は「13 specialized agents, 50+ skills, 33 commands」と記載している一方、リポジトリには **16 agents**、**65+ skills**、**40 commands** がある。README や他ドキュメントでも数がばらつき、コントリビューターとユーザーに混乱を与える。

**推奨:**

- **Single source of truth:** 件数（必要なら表も）を filesystem または小さな manifest から導出する。選択肢:
  - **Option A:** `agents/*.md`、`commands/*.md`、`skills/*/SKILL.md` を走査して JSON/Markdown を出力するスクリプト（例: `scripts/ci/catalog.js`）を追加。CI と docs がこれを消費する。
  - **Option B:** agent、command、skill とメタデータを列挙する `docs/catalog.json`（または YAML）を1つ維持。スクリプトと docs がそこから読む。追加/削除時の更新規律が必要。
- **短期:** AGENTS.md、README.md、CLAUDE.md を実際の件数に手動同期し、新規 agent（chief-of-staff、loop-operator、harness-optimizer など）を agent 表に追加する。

**影響:** 高 — 第一印象とコントリビューターの信頼に影響。

---

### 1.2 Command → Agent / Skill マップ (Command → Agent / Skill Map)

**課題:** 「どの command がどの agent(s) または skill(s) を使うか」の単一の機械可読/人間可読マップがない。README の表と個別 command `.md` に散在し、drift しうる。

**推奨:**

- 各 command について name、description、primary agent(s)、参照 skill(s) を列挙する**command registry**（`docs/` 内、または command ファイルの frontmatter）を追加。command ファイル内容から生成するか手動維持する。
- docs（例: `docs/COMMAND-AGENT-MAP.md`）または生成 catalog で「map」を公開し、発見性とツール利用（例:「tdd-guide を使う command は？」）を高める。

**影響:** 中 — 発見性とリファクタリングの安全性が向上。

---

## 2. テストと品質 (Testing and Quality)

### 2.1 テスト発見とハードコードリスト (Test Discovery vs Hardcoded List)

**課題:** `tests/run-all.js` はテストファイルの**ハードコードリスト**を使う。新規テストは `run-all.js` を更新しないと実行されず、カバレッジが漏れる可能性がある。

**推奨:**

- **Glob ベースの発見:** パターン（例: `tests/` 配下の `**/*.test.js`）でテストファイルを発見して実行。特殊ケース用に allowlist/denylist を任意で保持。
- 単一エントリポイント（`tests/run-all.js`）で発見したテストを実行し結果を集約。

**影響:** 高 — 新規テストが存在するが実行されない回帰を防ぐ。

---

### 2.2 テストカバレッジ指標 (Test Coverage Metrics)

**課題:** カバレッジツール（nyc/c8/istanbul など）がない。プロジェクト自身のスクリプトで「80%+ coverage」を断言できず、カバレッジは暗黙的。

**推奨:**

- Node スクリプト向けにカバレッジツール（`c8` または `nyc`）を導入し CI で実行。ベースライン（例: 60%）から段階的に引き上げるか、少なくとも CI で失敗させずにレポートしてトレンドを可視化。
- 主対象は `scripts/`（lib + hooks + ci）。必要なら one-off スクリプトは除外。

**影響:** 中 — AGENTS.md の指針（80%+ coverage）と整合し、未テストパスを可視化。

---

## 3. スキーマと検証 (Schema and Validation)

### 3.1 CI で Hooks JSON Schema を使用 (Use Hooks JSON Schema in CI)

**課題:** `schemas/hooks.schema.json` は hook 設定の形を定義するが、`scripts/ci/validate-hooks.js` は**使っていない**。検証が重複（VALID_EVENTS、構造）し、schema と drift しうる。

**推奨:**

- JSON Schema バリデータ（例: `ajv`）で `hooks/hooks.json` を `schemas/hooks.schema.json` に対して検証。構造の single source of truth はバリデータに置き、hook 固有チェック（インライン JS 構文など）だけスクリプトに残す。
- schema とバリデータの同期を保ち、hooks.json の `$schema` 経由で IDE/エディタ検証を可能にする。

**影響:** 中 — drift 低減と hook 編集時のコントリビューター体験向上。

---

## 4. Cross-Harness と i18n (Cross-Harness and i18n)

### 4.1 Skill/Agent サブセット同期 (.agents/skills, .cursor/skills)

**課題:** `.agents/skills/`（Codex）と `.cursor/skills/` は `skills/` のサブセット。メインリポジトリで skill を追加/削除するとき、これらのサブセットを手動更新する必要があり、忘れやすい。

**推奨:**

- CONTRIBUTING.md に skill 追加時に `.agents/skills` と `.cursor/skills` の更新が必要な場合があることと手順を記載。
- 任意: `skills/` とサブセットを比較し、あるべき skill が片方にだけある場合に失敗または警告する CI チェック/スクリプト（規約または小さな manifest による）。

**影響:** 低〜中 — cross-harness の drift 低減。

---

### 4.2 翻訳の Drift (docs/ zh-CN, zh-TW, ja-JP)

**課題:** `docs/` の翻訳は agent、command、skill を重複。英語ソースが進化すると、明確なプロセスやツールなしに翻訳が古くなる。

**推奨:**

- **翻訳プロセス**を文書化: いつ更新するか（リリース時など）、各ロケールのオーナー、古いコンテンツの検出方法（ファイルリストの diff や主要セクションなど）。
- 検討: 翻訳ステータスファイル（`docs/i18n-status.md`）や、英語の更新が翻訳より新しい場合に警告する CI。
- 長期: 英語ソースと同じ構造を参照する extraction/placeholder 形式（i18n キーなど）。

**影響:** 中 — 非英語ユーザーの体験向上と古い翻訳による混乱の低減。

---

## 5. Hook とスクリプト (Hooks and Scripts)

### 5.1 Hook ランタイムの一貫性 (Hook Runtime Consistency)

**課題:** Hook は一貫した Node モード dispatch surface を保つべき。continuous-learning の観測は `run-with-flags.js` と `observe-runner.js` 経由で dispatch し、shell モード hook エントリを公開せず既存 `observe.sh` に委譲する。

**推奨:**

- 可能なら新規 hook は Node を優先（クロスプラットフォーム、単一ランタイム）。shell が必要なら理由を文書化し surface を小さく保つ。
- `ECC_HOOK_PROFILE` と `ECC_DISABLED_HOOKS` がすべてのコードパス（shell 含む）で尊重されるようにする。

**影響:** 低 — 現行設計を維持。より多くの hook が Node に移行すれば改善。

---

## 6. サマリー表 (Summary Table)

| 領域 (Area) | 改善 (Improvement) | 優先度 (Priority) | 工数 (Effort) |
| --- | --- | --- | --- |
| Doc sync | AGENTS.md/README の件数と表を同期 | 高 (High) | 低 (Low) |
| Single source | カタログ script または manifest | 高 (High) | 中 (Medium) |
| Test discovery | Glob ベース test runner | 高 (High) | 低 (Low) |
| Coverage | c8/nyc と CI coverage を追加 | 中 (Medium) | 中 (Medium) |
| Hook schema in CI | schema 経由で hooks.json を検証 | 中 (Medium) | 低 (Low) |
| Command map | Command → agent/skill registry | 中 (Medium) | 中 (Medium) |
| Subset sync | `.agents`/`.cursor` の文書化/CI | 低〜中 (Low–Med) | 低〜中 (Low–Med) |
| Translations | プロセスと stale 検出 | 中 (Medium) | 中 (Medium) |
| Hook runtime | Node を優先; shell 使用を文書化 | 低 (Low) | 低 (Low) |

---

## 7. クイックウィン（即時） (Quick Wins (Immediate))

1. **AGENTS.md を更新:** agent 数を 16 に。chief-of-staff、loop-operator、harness-optimizer を agent 表に追加。skill/command 件数をリポジトリと整合。
2. **テスト発見:** `run-all.js` を `tests/` 配下の `**/*.test.js` 発見（任意 allowlist 付き）に変更し、新規テストを常に実行。
3. **Hooks schema を接続:** `validate-hooks.js` で ajv 等を使い `hooks/hooks.json` を `schemas/hooks.schema.json` で検証。hook 固有チェックだけスクリプトに残す。

これら3つは1〜2セッションで実施でき、一貫性と信頼性を大きく改善できる。
