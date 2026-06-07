# Command → Agent / Skill マップ (Command → Agent / Skill Map)

このドキュメントは各 slash command と、それが呼び出す主要 agent(s) または skills、および notable な直接呼び出し agent を一覧します。どの command がどの agent を使うかを発見し、リファクタリングの一貫性を保つために使ってください。

| Command | Primary agent(s) | Notes |
|---------|------------------|--------|
| `/plan` | planner | Implementation planning before code |
| `/tdd` | tdd-guide | Test-driven development |
| `/code-review` | code-reviewer | Quality and security review |
| `/build-fix` | build-error-resolver | Fix build/type errors |
| `/e2e` | e2e-runner | Playwright E2E tests |
| `/refactor-clean` | refactor-cleaner | Dead code removal |
| `/update-docs` | doc-updater | Documentation sync |
| `/update-codemaps` | doc-updater | Codemaps / architecture docs |
| `/go-review` | go-reviewer | Go code review |
| `/go-test` | tdd-guide | Go TDD workflow |
| `/go-build` | go-build-resolver | Fix Go build errors |
| `/python-review` | python-reviewer | Python code review |
| `/harness-audit` | — | Harness scorecard (no single agent) |
| `/loop-start` | loop-operator | Start autonomous loop |
| `/loop-status` | loop-operator | Inspect loop status |
| `/quality-gate` | — | Quality pipeline (hook-like) |
| `/model-route` | — | Model recommendation (no agent) |
| `/orchestrate` | planner, tdd-guide, code-reviewer, security-reviewer, architect | Multi-agent handoff |
| `/multi-plan` | architect (Codex/Gemini prompts) | Multi-model planning |
| `/multi-execute` | architect / frontend prompts | Multi-model execution |
| `/multi-backend` | architect | Backend multi-service |
| `/multi-frontend` | architect | Frontend multi-service |
| `/multi-workflow` | architect | General multi-service |
| `/learn` | — | continuous-learning skill, instincts |
| `/learn-eval` | — | continuous-learning-v2, evaluate then save |
| `/instinct-status` | — | continuous-learning-v2 |
| `/instinct-import` | — | continuous-learning-v2 |
| `/instinct-export` | — | continuous-learning-v2 |
| `/evolve` | — | continuous-learning-v2, cluster instincts |
| `/promote` | — | continuous-learning-v2 |
| `/projects` | — | continuous-learning-v2 |
| `/skill-create` | — | skill-create-output script, git history |
| `/checkpoint` | — | verification-loop skill |
| `/verify` | — | verification-loop skill |
| `/eval` | — | eval-harness skill |
| `/test-coverage` | — | Coverage analysis |
| `/sessions` | — | Session history |
| `/setup-pm` | — | Package manager setup script |
| `/claw` | — | NanoClaw CLI (scripts/claw.js) |
| `/pm2` | — | PM2 service lifecycle |
| `/security-scan` | security-reviewer (skill) | AgentShield via security-scan skill |

## 直接使用 Agent (Direct-Use Agents)

| Direct agent | Purpose | Scope | Notes |
|--------------|---------|-------|-------|
| `typescript-reviewer` | TypeScript/JavaScript code review | TypeScript/JavaScript projects | Invoke the agent directly when a review needs TS/JS-specific findings and there is no dedicated slash command yet. |

## command が参照する Skills (Skills referenced by commands)

- **continuous-learning**, **continuous-learning-v2**: `/learn`, `/learn-eval`, `/instinct-*`, `/evolve`, `/promote`, `/projects`
- **verification-loop**: `/checkpoint`, `/verify`
- **eval-harness**: `/eval`
- **security-scan**: `/security-scan` (runs AgentShield)
- **strategic-compact**: suggested at compaction points (hooks)

## このマップの使い方 (How to use this map)

- **発見性:** どの command がどの agent を起動するかを見つける（例：「code-reviewer には `/code-review` を使う」）。
- **リファクタリング:** agent の名前変更や削除時に、この doc と command ファイルの参照を検索する。
- **CI/docs:** catalog script（`node scripts/ci/catalog.js`）は agent/command/skill 件数を出力する。このマップは command–agent 関係を補完する。
