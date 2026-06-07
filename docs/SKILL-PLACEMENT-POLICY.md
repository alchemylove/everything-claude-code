# Skill 配置と来歴ポリシー (Skill Placement and Provenance Policy)

このドキュメントは、生成、import、curated された skills の配置場所、識別方法、出荷対象を定義します。

## Skill タイプと配置 (Skill Types and Placement)

| Type | Root Path | Shipped | Provenance |
|------|-----------|---------|------------|
| Curated | `skills/` (repo) | Yes | Not required |
| Learned | `~/.claude/skills/learned/` | No | Required |
| Imported | `~/.claude/skills/imported/` | No | Required |
| Evolved | `~/.claude/homunculus/evolved/skills/` (global) or `projects/<hash>/evolved/skills/` (per-project) | No | Inherits from instinct source |

Curated skills はリポジトリの `skills/` にあります。Install manifest は curated path のみ参照します。生成・import された skills はユーザーホームディレクトリにあり、出荷されません。

## Curated Skills

配置：`skills/<skill-name>/`、ルートに `SKILL.md`。

- `manifests/install-modules.json` paths に含まれる。
- `scripts/ci/validate-skills.js` で検証。
- 来歴ファイル不要。`SKILL.md` frontmatter の `origin`（ECC、community）で帰属。

## Learned Skills

配置：`~/.claude/skills/learned/<skill-name>/`。

continuous-learning（evaluate-session hook、`/learn` command）で作成。デフォルトパスは `skills/continuous-learning/config.json` → `learned_skills_path` で設定可能。

- リポジトリ外。出荷しない。
- `SKILL.md` の兄弟に `.provenance.json` が必須。
- ディレクトリが存在するとき runtime で読み込まれる。

## Imported Skills

配置：`~/.claude/skills/imported/<skill-name>/`。

外部ソース（URL、ファイルコピーなど）からのユーザーインストール skill。自動 importer はまだない；配置は規約による。

- リポジトリ外。出荷しない。
- `SKILL.md` の兄弟に `.provenance.json` が必須。

## Evolved Skills (Continuous Learning v2)

配置：`~/.claude/homunculus/evolved/skills/`（global）または `~/.claude/homunculus/projects/<hash>/evolved/skills/`（per-project）。

instinct-cli evolve がクラスターされた instincts から生成。learned/imported とは別システム。

- リポジトリ外。出荷しない。
- 来歴はソース instincts から継承；別途 `.provenance.json` は不要。

## 来歴メタデータ (Provenance Metadata)

learned と imported skills に必須。ファイル：skill ディレクトリ内の `.provenance.json`。

必須フィールド：

| Field | Type | Description |
|-------|------|-------------|
| source | string | Origin (URL, path, or identifier) |
| created_at | string | ISO 8601 timestamp |
| confidence | number | 0–1 |
| author | string | Who or what produced the skill |

スキーマ：`schemas/provenance.schema.json`。検証：`scripts/lib/skill-evolution/provenance.js` → `validateProvenance`。

## Validator 動作 (Validator Behavior)

### validate-skills.js

スコープ：curated skills のみ（リポジトリ内 `skills/`）。

- `skills/` が存在しない：exit 0（検証対象なし）。
- 各サブディレクトリ：`SKILL.md` を含み、非空であること。
- learned/imported/evolved ルートには触れない。

### validate-install-manifests.js

スコープ：curated path のみ。modules 内のすべての `paths` はリポジトリに存在すること。

- 生成/import ルートはスコープ外。manifest は参照しない。
- 欠落 path → error。optional-path 処理なし。

### 生成ルートを使うスクリプト (Scripts That Use Generated Roots)

`scripts/skills-health.js`、`scripts/lib/skill-evolution/health.js`、session hooks：`~/.claude/skills/learned` と `~/.claude/skills/imported` をプローブ。欠落ディレクトリは空として扱い；エラーなし。

## 公開可能 vs ローカルのみ (Publishable vs Local-Only)

| Publishable | Local-Only |
|-------------|------------|
| `skills/*` (curated) | `~/.claude/skills/learned/*` |
| | `~/.claude/skills/imported/*` |
| | `~/.claude/homunculus/**/evolved/**` |

install manifest に現れるのは curated skills のみで、install 時にコピーされる。

## 実装ロードマップ (Implementation Roadmap)

1. ポリシードキュメントと来歴スキーマ（この変更）。
2. learned-skill 書き込みパス（evaluate-session、`/learn` 出力）に来歴検証を追加し、新しい learned skills が常に `.provenance.json` を得るようにする。
3. instinct-cli evolve を更新し、evolved skills 生成時に optional 来歴を書く。
4. learned/imported コンテンツを含んではならないリポジトリ path 向けに `scripts/validate-provenance.js` を CI に追加（必要なら）。
5. learned/imported ルートを CONTRIBUTING.md またはユーザードキュメントに記載し、コントリビューターがコミットしないよう知らせる。
