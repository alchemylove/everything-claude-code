# レガシー Artifact インベントリ (Legacy Artifact Inventory)

このインベントリは、レガシーと stale 作業のクリーンアップが暗黙にならないようにします。各
artifact は、GA または rc publication pass が queue を clean とみなす前に
landed、milestone-tracked、salvage branch、または archive/no-action に分類されるべきです。

## 分類状態 (Classification States)

| State | Meaning |
| --- | --- |
| Landed | Useful work has already been ported to current `main` and verified. |
| Milestone-tracked | Useful work remains, but belongs to a named roadmap milestone. |
| Salvage branch | Useful work should be ported through a fresh maintainer branch with attribution. |
| Translator/manual review | Content may be useful, but cannot be safely imported automatically. |
| Archive/no-action | Artifact is intentionally retained or skipped; no active port is planned. |

## 現在のリポジトリスキャン (Current Repository Scan)

2026-05-12 時点で、追跡リポジトリに `_legacy-documents-*` ディレクトリはありません。

新鮮なチェック：

```sh
find . -type d -name '_legacy-documents-*' -print
```

期待結果：出力なし。

ファイル名スキャンで現在見つかった唯一の追跡レガシーディレクトリは
`legacy-command-shims/` です。

umbrella ECC ワークスペースには、追跡 checkout 外に sibling レガシー git リポジトリも含まれます。
これらは意図的に別途インベントリされます。生 operator context、ローカル設定、プライベート draft、
公開リポジトリに wholesale コピーすべきでない untracked ファイルを含みうるためです。

ECC umbrella ディレクトリからの新鮮なワークスペースレベルチェック：

```sh
find .. -maxdepth 1 -type d -name '_legacy-documents-*' -print | sort
```

期待結果：

```text
../_legacy-documents-ecc-context-2026-04-30
../_legacy-documents-ecc-everything-claude-code-2026-04-30
```

## インベントリ (Inventory)

| Artifact | State | Evidence | Action |
| --- | --- | --- | --- |
| `_legacy-documents-*` directories | Archive/no-action | No matching directories exist in the tracked checkout as of 2026-05-12. | Re-run the scan before release. If any appear, add each directory to this table before publishing. |
| `legacy-command-shims/` | Archive/no-action | `legacy-command-shims/README.md` states these retired short-name shims are opt-in and no longer loaded by the default plugin command surface. | Keep as an explicit compatibility archive. Do not move these back into the default plugin surface without a migration decision. |
| Closed-stale PR salvage ledger | Landed | `docs/stale-pr-salvage-ledger.md` records useful stale work recovered through maintainer PRs. | Continue using the ledger pattern for future stale closures. |
| #1687 zh-CN localization tail | Translator/manual review | Large safe subsets landed in #1746-#1752; remaining pieces are attached to Linear ITO-55 for language-owner review. | Do not blindly cherry-pick. Split by docs, commands, agents, and skills if a translator review lane opens; no automatic import remains release-blocking. |
| #1609 Persian README translation | Translator/manual review | Recorded in the stale salvage ledger and attached to Linear ITO-55 for language-owner review. | Do not import stale README/version/count text without a Persian reviewer and a current catalog refresh. |
| #1563 zh-TW README sync | Translator/manual review | Recorded in the stale salvage ledger and attached to Linear ITO-55 for language-owner review. | Do not import stale README/version/count text without a zh-TW reviewer and a current catalog refresh. |
| #1564 Turkish README sync | Translator/manual review | Recorded in the stale salvage ledger and attached to Linear ITO-55 for language-owner review. | Do not import stale README/version/count text without a Turkish reviewer and a current catalog refresh. |
| #1565 pt-BR README sync | Translator/manual review | Recorded in the stale salvage ledger and attached to Linear ITO-55 for language-owner review. | Do not import stale README/version/count text without a pt-BR reviewer and a current catalog refresh. |

## ワークスペースレベルレガシーリポ (Workspace-Level Legacy Repos)

これらの sibling リポジトリは追跡 `everything-claude-code`
checkout の外にあります。将来の salvage pass のソース素材であり、インストール可能な
リリース asset ではありません。

| Artifact | State | Evidence | Action |
| --- | --- | --- | --- |
| `../_legacy-documents-ecc-everything-claude-code-2026-04-30` | Archive/no-action | Separate legacy checkout on `fix/configure-ecc-skill-copy-paths-1483` at `b78ddbd0`; useful configure-ecc and install-path concepts have been superseded by current install docs and tests. The checkout also has untracked localized project-guidelines examples and a Finder duplicate `skills/social-graph-ranker/SKILL 2.md`. | Do not import wholesale. If configure-ecc copy-root regressions reappear, use this branch only as source-attributed archaeology and port through a fresh maintainer branch. Leave Finder duplicates out of source control. |
| `../_legacy-documents-ecc-context-2026-04-30` | Milestone-tracked | Archived `ECC-context` repo is four commits ahead of its origin and contains context, gameplan, knowledge, marketing, AgentShield, and ECC Tools planning material. It also contains local/private surfaces such as `.env` and local settings. | Keep as a sanitized extraction source for roadmap, launch, AgentShield, and ECC Tools work. Never copy raw context, secrets, personal paths, private settings, or unpublished drafts into this repo. Port only focused, public-safe content with attribution. |

## ワークスペースレガシー Import ルール (Workspace Legacy Import Rules)

ワークスペースレベルレガシーリポを採掘するとき：

1. `.env` ファイル、token、OAuth secret、
   ローカル設定、個人 path、プライベート operator context を読み、表示、stage、コピーしない。
2. 生マーケティング draft、gameplan、chat/context dump を import しない。
3. 焦点を当てた公開安全なアイデアのみを現在の docs または code に抽出。
4. 新 PR でソースレガシーリポ、branch、commit、または stale PR を帰属。
5. ネイティブ作業と同じテストとリリースチェックで結果を検証。

## レガシー Command Shim 内容 (Legacy Command Shim Contents)

互換性アーカイブには現在12の retired command shim があります：

| Shim | Preferred current direction |
| --- | --- |
| `agent-sort.md` | Use maintained command or skill routing where available. |
| `claw.md` | Use maintained `scripts/claw.js` / `npm run claw` surfaces. |
| `context-budget.md` | Use maintained token/context budgeting skills. |
| `devfleet.md` | Use maintained agent/harness orchestration docs and skills. |
| `docs.md` | Use current documentation and release checklist workflows. |
| `e2e.md` | Use maintained E2E testing skills and test scripts. |
| `eval.md` | Use eval-harness and verification-loop skills. |
| `orchestrate.md` | Use maintained orchestration status and worktree scripts. |
| `prompt-optimize.md` | Use prompt-optimizer skill. |
| `rules-distill.md` | Use current rules and skill extraction workflows. |
| `tdd.md` | Use tdd-workflow and language-specific testing skills. |
| `verify.md` | Use verification-loop and package-specific verification skills. |

## リリースルール (Release Rule)

GA または rc publication pass の前に：

1. `_legacy-documents-*` スキャンを再実行。
2. closed-stale salvage ledger チェックを再実行。
3. 新しく発見されたレガシー artifact がこのファイルにすべて表れていることを確認。
4. ソース帰属付きの新鮮な maintainer PR 経由で有用作業を port。
5. archive/no-action artifact をデフォルト install と plugin loading から除外。
