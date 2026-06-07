---
description: 決定論的なリポジトリ harness audit を実行し、優先順位付き scorecard を返します。
---

# Harness 監査コマンド (Harness Audit Command)

決定論的なリポジトリ harness audit を実行し、優先順位付き scorecard を返します。

## 使い方 (Usage)

`/harness-audit [scope] [--format text|json] [--root path]`

- `scope`（オプション）: `repo`（デフォルト）、`hooks`、`skills`、`commands`、`agents`
- `--format`: output style（`text` がデフォルト、automation には `json`）
- `--root`: 現在の working directory の代わりに特定の path を audit

## 決定論的エンジン (Deterministic Engine)

常に実行:

```bash
node scripts/harness-audit.js <scope> --format <text|json> [--root <path>]
```

この script が scoring と check の source of truth です。追加の dimension や ad-hoc な point を作り出さないでください。

Rubric version: `2026-05-19`.

script は最大 12 の固定 category（各 `0-10` normalized）を計算します。最初の 7 つは常に applicable、GitHub Integration は常に applicable、deploy-target category は matching marker が検出された場合のみ applicable です。

1. Tool Coverage
2. Context Efficiency
3. Quality Gates
4. Memory Persistence
5. Eval Coverage
6. Security Guardrails
7. Cost Efficiency
8. GitHub Integration
9. Vercel Integration *(when `vercel.json` or `.vercel/` is present)*
10. Netlify Integration *(when `netlify.toml` or `.netlify/` is present)*
11. Cloudflare Integration *(when `wrangler.toml` or `wrangler.jsonc` is present)*
12. Fly Integration *(when `fly.toml` is present)*

score は explicit file/rule check から導出され、同じ commit に対して reproducible です。
script はデフォルトで現在の working directory を audit し、target が ECC repo 自体か、ECC を使用する consumer project かを auto-detect します。

## 出力契約 (Output Contract)

返却内容:

1. `overall_score` out of `max_score`。`max_score` は target に applicable な category によって異なります。fixed total を assume しないでください。
2. `applicable_categories[]` と `category_count` — どの category が寄与したかを記述
3. category score と concrete findings
4. exact file path 付きの failed check
5. deterministic output からの Top 3 action（`top_actions`）
6. 次に適用すべき suggested ECC skill

## チェックリスト (Checklist)

- script output を直接使用。手動で rescore しない。
- `--format json` が要求された場合、script JSON をそのまま返す。
- text が要求された場合、failing check と top action を summarize。
- `checks[]` と `top_actions[]` からの exact file path を含める。

## 結果の例 (Example Result)

```text
Harness Audit (repo, repo): 71/80
- Tool Coverage: 10/10 (10/10 pts)
- Context Efficiency: 9/10 (9/10 pts)
- Quality Gates: 10/10 (10/10 pts)
- GitHub Integration: 2/10 (2/10 pts)

Top 3 Actions:
1) [GitHub Integration] Add at least one workflow under .github/workflows/. (.github/workflows/)
2) [Security Guardrails] Add prompt/tool preflight security guards in hooks/hooks.json. (hooks/hooks.json)
3) [Eval Coverage] Increase automated test coverage across scripts/hooks/lib. (tests/)
```

## 引数 (Arguments)

$ARGUMENTS:
- `repo|hooks|skills|commands|agents`（オプション scope）
- `--format text|json`（オプション output format）
