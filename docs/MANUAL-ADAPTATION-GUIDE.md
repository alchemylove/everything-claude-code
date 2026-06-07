# 非ネイティブ Harness 向け手動適応ガイド (Manual Adaptation Guide for Non-Native Harnesses)

`.claude/`、`.codex/`、`.opencode/`、`.cursor/`、`.agent/` レイアウトをネイティブに読み込まない harness 内で ECC の振る舞いを使いたいときに、このガイドを使用してください。

これは、system prompt、アップロードファイル、貼り付け指示は受け付けるが、リポジトリのネイティブ install surface を直接実行できない、Grok などのチャット型インターフェース向けのフォールバックパスです。

## 使用するタイミング (When to Use This)

次の条件に当てはまる target harness では手動適応を使用します:

- repo フォルダを自動読み込みしない
- カスタム slash command をサポートしない
- hook をサポートしない
- repo ローカルの skill 有効化をサポートしない
- filesystem/tool アクセスが部分的またはない

可能なら first-class ECC target を優先してください:

- Claude Code
- Codex
- Cursor
- OpenCode
- CodeBuddy
- Antigravity

非ネイティブ harness で ECC の振る舞いが必要な場合にのみ、このガイドを使用してください。

## 再現するもの (What You Are Reproducing)

ECC を手動適応するとき、次の4つを保持しようとします:

1. リポジトリ全体をダンプするのではなく、絞った context。
2. モデルに workflow を推測させるのではなく、skill 有効化の手がかり。
3. harness に slash-command システムがなくても、command の意図。
4. harness にネイティブ自動化がなくても、hook の規律。

リポジトリ内のすべてのファイルをミラーする必要はありません。最小限の context bundle で有用な振る舞いを再現することが目的です。

## ECC ネイティブのフォールバック (The ECC-Native Fallback)

リポジトリ自体からの手動選択をデフォルトにしてください。

実際に必要なファイルだけから始めます:

- 言語または framework skill を1つ
- workflow skill を1つ
- タスクが専門的なら domain skill を1つ
- harness が明示的な orchestration の恩恵を受ける場合のみ agent または command を1つ

良い最小例:

- Python feature 作業:
  - `skills/python-patterns/SKILL.md`
  - `skills/tdd-workflow/SKILL.md`
  - `skills/verification-loop/SKILL.md`
- TypeScript API 作業:
  - `skills/backend-patterns/SKILL.md`
  - `skills/security-review/SKILL.md`
  - `skills/tdd-workflow/SKILL.md`
- Content/outbound 作業:
  - `skills/brand-voice/SKILL.md`
  - `skills/content-engine/SKILL.md`
  - `skills/crosspost/SKILL.md`

harness がファイルアップロードをサポートする場合は、それらのファイルだけをアップロードしてください。

貼り付け context のみサポートする場合は、生の全ファイルではなく、関連セクションを抽出して圧縮 bundle を貼り付けてください。

## 手動 Context パッキング (Manual Context Packing)

これに追加ツールは不要です。

リポジトリを直接使用します:

```bash
cd /path/to/everything-claude-code

sed -n '1,220p' skills/tdd-workflow/SKILL.md > /tmp/ecc-context.md
printf '\n\n---\n\n' >> /tmp/ecc-context.md
sed -n '1,220p' skills/backend-patterns/SKILL.md >> /tmp/ecc-context.md
printf '\n\n---\n\n' >> /tmp/ecc-context.md
sed -n '1,220p' skills/security-review/SKILL.md >> /tmp/ecc-context.md
```

パッキング前に `rg` で適切な skill を特定することもできます:

```bash
rg -n "When to use|Use when|Trigger" skills -g 'SKILL.md'
```

任意: すでに `repomix` などの repo packer を使っている場合、選択ファイルを1つの handoff ドキュメントに圧縮するのに役立ちます。便利ツールであり、canonical ECC パスではありません。

## 圧縮ルール (Compression Rules)

他の harness 向けに ECC を手動パッキングするとき:

- タスクの枠組みを残す
- 有効化条件を残す
- workflow 手順を残す
- 重要な例を残す
- まず繰り返しの散文を削除する
- 次に無関係なバリアントを削除する
- skill が1〜2個で足りるときはディレクトリ全体を貼り付けない

よりタイトな prompt 形式が必要なら、本質部分をコンパクトな構造化ブロックに変換します:

```xml
<skill name="tdd-workflow">
  <when>New feature, bug fix, or refactor that should be test-first.</when>
  <steps>
    <step>Write a failing test.</step>
    <step>Make it pass with the smallest change.</step>
    <step>Refactor and rerun validation.</step>
  </steps>
</skill>
```

## Command の再現 (Reproducing Commands)

harness に slash-command システムがない場合、system prompt またはセッション preamble に小さな command registry を定義します。

例:

```text
Command registry:
- /plan -> use planner-style reasoning, produce a short execution plan, then act
- /tdd -> follow the tdd-workflow skill
- /review -> switch into code-review mode and enumerate findings first
- /verify -> run a verification loop before claiming completion
```

実際の command を実装しているわけではありません。harness に ECC の振る舞いへマップする明示的な呼び出しハンドルを与えています。

## Hook の再現 (Reproducing Hooks)

harness にネイティブ hook がない場合、hook の意図を standing instructions に移します。

例:

```text
Before writing code:
1. Check whether a relevant skill should be activated.
2. Check for security-sensitive changes.
3. Prefer tests before implementation when feasible.

Before finalizing:
1. Re-read the user request.
2. Verify the main changed paths.
3. State what was actually validated and what was not.
```

真の自動化は再現できませんが、ECC の運用規律は捉えられます。

## Harness 能力マトリクス (Harness Capability Matrix)

| Capability | First-Class ECC Targets | Manual-Adaptation Targets |
| --- | --- | --- |
| Folder-based install | Native | No |
| Slash commands | Native | Simulated in prompt |
| Hooks | Native | Simulated in prompt |
| Skill activation | Native | Manual |
| Repo-local tooling | Native | Depends on harness |
| Context packing | Optional | Required |

## 実践的な Grok スタイルセットアップ (Practical Grok-Style Setup)

1. 最小で有用な bundle を選ぶ。
2. 選択した ECC skill ファイルを1つのアップロードまたは貼り付けブロックにパックする。
3. 短い command registry を追加する。
4. standing「hook intent」指示を追加する。
5. 1つのタスクから始め、harness が workflow に従うことを確認してから拡大する。

スターター preamble の例:

```text
You are operating with a manually adapted ECC bundle.

Active skills:
- backend-patterns
- tdd-workflow
- security-review

Command registry:
- /plan
- /tdd
- /verify

Before writing code, follow the active skill instructions.
Before finalizing, verify what changed and report any remaining gaps.
```

## 制限事項 (Limitations)

手動適応は有用ですが、ネイティブ target と比べて依然として二流です。

失うもの:

- 自動 install と sync
- ネイティブ hook 実行
- 真の command plumbing
- 実行時の信頼できる skill discovery
- 組み込み multi-agent/worktree orchestration

ルールは単純です:

- 非ネイティブ harness に ECC の振る舞いを持ち込むには手動適応を使う
- フルシステムが欲しいときはネイティブ ECC target を使う

## 関連資料 (Related Work)

- [Issue #1186](https://github.com/affaan-m/everything-claude-code/issues/1186)
- [Discussion #1077](https://github.com/affaan-m/everything-claude-code/discussions/1077)
- [Antigravity Guide](./ANTIGRAVITY-GUIDE.md)
- [Troubleshooting](./TROUBLESHOOTING.md)
