---
description: プロジェクトのスタックを検出し、リポジトリの install manifest と stack mapping を使って dry-run の ECC オンボーディング計画を生成する。
---

# /project-init

現在のプロジェクト向けに、安全でレビュー可能な ECC オンボーディング計画を作成する。このコマンドは dry-run モードで開始し、ユーザーが明示的に承認した後にのみファイルを書き込む。

## 使い方 (Usage)

```text
/project-init
/project-init --dry-run
/project-init --target claude
/project-init --target cursor
/project-init --skills continuous-learning-v2,security-review
/project-init --config ecc-install.json
```

## 安全ルール (Safety Rules)

1. デフォルトは dry-run。ユーザーが具体的な計画を承認するまで、`CLAUDE.md`、settings ファイル、rules、skills、install state を変更しない。
2. 既存のプロジェクトガイダンスを保持する。`CLAUDE.md`、`.claude/settings.local.json`、`.cursor/`、`.codex/`、`.gemini/`、`.opencode/`、`.codebuddy/`、`.joycode/`、`.qwen/` が既に存在する場合は調査し、上書きではなく merge/append 計画を提案する。
3. ECC の installer と manifest ツールを使用する。install のショートカットとしてファイルを手動コピーしたり、任意の remote を clone したりしない。
4. 権限は狭く保つ。生成される settings は検出された build/test/lint ツールに合わせ、広範な shell アクセスは避ける。
5. 適用前に、何が変更されるかを正確に報告する。

## 検出入力 (Detection Inputs)

現在のプロジェクトルートを読み、以下から stack シグナルを検出する:

- package manager ファイル: `package.json`, `package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`, `bun.lockb`
- 言語 manifest: `pyproject.toml`, `requirements.txt`, `go.mod`, `Cargo.toml`, `pom.xml`, `build.gradle`, `build.gradle.kts`
- framework ファイル: `next.config.*`, `vite.config.*`, `tailwind.config.*`, `Dockerfile`, `docker-compose.yml`
- ECC config: `ecc-install.json`
- 任意の stack map: ECC リポジトリ内の `config/project-stack-mappings.json`

ECC checkout が利用可能な場合、`config/project-stack-mappings.json` を stack-to-rules/skills の参照として使用する。ファイルが利用できない場合は、インストール済み ECC manifest とユーザーの明示的な選択にフォールバックする。

## 計画フロー (Planning Flow)

1. 対象 harness を特定する。ユーザーが `cursor`, `codex`, `gemini`, `opencode`, `codebuddy`, `joycode`, `qwen` を指定しない限り、デフォルトは `claude`。
2. プロジェクトファイルから stack を検出し、各マッチの根拠を表示する。
3. 最小限で有用な ECC 計画を解決する:
   - プロジェクトに `ecc-install.json` がある: `node scripts/install-plan.js --config ecc-install.json --json`
   - ユーザーが profile を指定: `node scripts/install-plan.js --profile <profile> --target <target> --json`
   - ユーザーが skills を指定: `node scripts/install-plan.js --skills <skill-ids> --target <target> --json`
   - 言語 stack のみ検出: それらの言語名で legacy language install dry-run を使用
4. 書き込み前に dry-run apply コマンドを実行する:

```bash
node scripts/install-apply.js --target <target> --dry-run --json <language-or-profile-args>
```

5. 検出された stack、選択された modules/components/skills、対象パス、スキップされた unsupported modules、変更されるファイルを要約する。
6. non-dry-run コマンドを適用する前に承認を求める。

## 出力契約 (Output Contract)

以下を返す:

1. 検出された stack の根拠
2. 提案される対象 harness
3. 使用した dry-run コマンドの正確な内容
4. 承認後に実行する apply コマンドの正確な内容
5. 作成または変更されるファイル/ディレクトリ
6. 既存ファイル、広範な権限、欠落スクリプト、unsupported target に関する警告

## CLAUDE.md ガイダンス (CLAUDE.md Guidance)

ユーザーが `CLAUDE.md` のスターターを望む場合、installer 計画とは別に生成し、最小限に保つ:

- 検出された場合の build コマンド
- 検出された場合の test コマンド
- 検出された場合の lint/typecheck コマンド
- 検出された場合の dev server コマンド
- 既存の package scripts または manifest からのリポジトリ固有のメモ

diff を表示し承認を得ることなく、既存の `CLAUDE.md` を置き換えない。

## 関連 (Related)

- stack-to-surface のヒント: `config/project-stack-mappings.json`
- 決定的な plan 解決: `scripts/install-plan.js`
- dry-run と apply 操作: `scripts/install-apply.js`
- インストール前の対話的な機能探索: `/ecc-guide`
