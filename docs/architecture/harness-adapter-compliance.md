# Harness Adapter 準拠マトリクス (Harness Adapter Compliance Matrix)

このマトリクスは、複数のコーディング harness で ECC を使いたいチーム向けの公開オンプラムです。cross-harness アーキテクチャを
実用的 scorecard に変換します：今日何が動くか、instruction-only か、adapter が必要か、
operator がセットアップを信頼する前に収集すべき evidence。

ECC の durable unit は共有ソースに残ります：

- `skills/*/SKILL.md`
- `rules/`
- `commands/`
- `hooks/hooks.json`
- `scripts/hooks/`
- MCP reference configs
- session and observability contracts

Harness 固有ファイルは loading、event shape、command 名、
または platform 制限の適応のみを行うべきです。

## 準拠状態 (Compliance States)

| State | Meaning |
| --- | --- |
| Native | ECC can install or verify the surface directly for this harness. |
| Adapter-backed | ECC has a thin adapter, plugin, or package surface, but parity differs by harness. |
| Instruction-backed | ECC can provide the guidance and files, but the harness does not expose the runtime hook/session surface ECC needs for enforcement. |
| Reference-only | The tool is useful as a design pressure or external runtime, but ECC does not yet ship a direct installer or adapter for it. |

## マトリクス (Matrix)

以下のマトリクスは
`scripts/lib/harness-adapter-compliance.js` から描画され、
`npm run harness:adapters -- --check` で検証されます。

<!-- harness-adapter-compliance:matrix-start -->
| Harness or runtime | State | Supported assets | Unsupported or different surfaces | Install or onramp | Verification command | Risk notes |
| --- | --- | --- | --- | --- | --- | --- |
| Claude Code | Native | Claude plugin assets; skills; commands; hooks; MCP config; local rules; statusline-oriented workflows | Claude-native hooks do not imply parity in other harnesses | `./install.sh --profile minimal --target claude`; Claude plugin install | `npm run harness:audit -- --format json`; `node scripts/session-inspect.js --list-adapters` | Avoid loading every skill by default; keep hooks opt-in and inspectable. |
| Codex | Instruction-backed | `AGENTS.md`; Codex plugin metadata; skills; MCP reference config; command patterns | Native hook enforcement and Claude slash-command semantics are not equivalent | `./install.sh --profile minimal --target codex`; repo-local `AGENTS.md` review | `npm run harness:audit -- --format json` | Treat hooks as policy text unless a native Codex hook surface exists. |
| OpenCode | Adapter-backed | OpenCode package/plugin metadata; shared skills; MCP config; event adapter patterns | Event names, plugin packaging, and command dispatch differ from Claude Code | OpenCode package or plugin surface from this repo | `node tests/scripts/build-opencode.test.js`; `npm run harness:audit -- --format json` | Keep hook logic in shared scripts and adapt only event shape at the edge. |
| Cursor | Adapter-backed | Cursor rules; project-local skills; hook adapter; shared scripts | Cursor hook events and rule loading differ from Claude Code | `./install.sh --profile minimal --target cursor` | `node tests/lib/install-targets.test.js`; `npm run harness:audit -- --format json` | Cursor adapters must preserve existing project rules and avoid silent overwrite. |
| Gemini | Instruction-backed | Gemini project-local instructions; shared skills; rules; compatibility docs | No full ECC hook parity; ecosystem ports must document drift from upstream ECC | `./install.sh --profile minimal --target gemini` | `node tests/lib/install-targets.test.js` | Treat Gemini ports as ecosystem adapters until validated end to end inside Gemini CLI. |
| Zed | Adapter-backed | Zed project settings; flattened project rules; shared skills; commands; agents | Zed external agents and native Agent Panel permissions are not Claude hooks | `./install.sh --profile minimal --target zed` | `node tests/lib/install-targets.test.js`; `npm run harness:audit -- --format json` | Keep project settings conservative and do not copy BYOK/OpenRouter secrets into `.zed/`. |
| dmux | Adapter-backed | session snapshots; tmux/worktree orchestration status; handoff exports | dmux is an orchestration runtime, not an install target for skills/rules | `node scripts/session-inspect.js --list-adapters`; dmux session target inspection | `node tests/lib/session-adapters.test.js` | Treat dmux events as session/runtime signals, not as a replacement for repo validation. |
| Orca | Reference-only | worktree lifecycle; review state; notification; provider-identity design pressure | No ECC installer or direct adapter today | Use as a comparison target for worktree/session state requirements | `npm run observability:ready` | Do not import product-specific assumptions; convert lessons into ECC event fields. |
| Superset | Reference-only | workspace presets; parallel-agent review loops; worktree isolation design pressure | No ECC installer or direct adapter today | Use as a comparison target for workspace preset taxonomy | `npm run observability:ready` | Keep ECC portable; do not require a desktop workspace to get basic value. |
| Ghast | Reference-only | terminal-native pane grouping; cwd grouping; search; notifications | No ECC installer or direct adapter today | Use as a comparison target for terminal-first session grouping | `node scripts/session-inspect.js --list-adapters` | Preserve terminal ergonomics before adding visual UI assumptions. |
| Terminal-only | Native | skills; rules; commands; scripts; harness audit; observability readiness; handoffs | No external UI, no automatic session control unless scripts are run explicitly | Clone repo; run commands directly; use minimal profile for project installs | `npm run harness:audit -- --format json`; `npm run observability:ready` | This is the fallback contract; every higher-level adapter should degrade to it. |
<!-- harness-adapter-compliance:matrix-end -->

## Scorecard オンプラム (Scorecard Onramp)

チームまたはリポジトリセットアップをより自律的にするよう ECC に頼る前に、この順序を使ってください：

```bash
npm run harness:adapters -- --check
npm run harness:audit -- --format json
npm run observability:ready
node scripts/session-inspect.js --list-adapters
node scripts/loop-status.js --json --write-dir .ecc/loop-status
```

結果をプロダクトバッジではなくセットアップ scorecard として読む：

- `harness:adapters -- --check` はこの公開マトリクスがまだ adapter ソースデータと
  必須 evidence フィールドに一致することを証明する。
- `harness:audit` は tool coverage、context efficiency、quality gate、
  memory persistence、eval coverage、security guardrail、cost efficiency をスコアリングする。
- `observability:ready` はリポジトリがまだローカル status、
  session、tool-activity、risk-ledger、release-onramp signal を露出することを証明する。
- `session-inspect --list-adapters` は現在の環境で実際に inspect 可能な session 面を示す。
- `loop-status --json` はより長い自律 run 向けに machine-readable handoff/status payload を作成する。

## データバックド Scorecard 契約 (Data-Backed Scorecard Contract)

各 adapter レコードは次を公開：

- `id`
- `state`
- `supported_assets`
- `unsupported_surfaces`
- `install_or_onramp`
- `verification_commands`
- `risk_notes`
- `last_verified_at`
- `owner`
- `source_docs`

公開 adapter クレームに install path、
verification command、risk note、owner、source doc、verification 日付のいずれかが欠けていると validator は失敗します。

## 運用ルール (Operating Rules)

- 同じワークフローの harness 固有 fork より小さな additive adapter を優先する。
- adapter に install path と verification command があるまで harness を native と呼ばない。
- enforcement が runtime-backed ではなく instruction-backed のとき、Codex、Gemini、Zed 面を正直に保つ。
- ECC に直接 adapter があるまで reference-only ツールを design pressure として扱う。
- terminal-only path を健全に保つ；それは portability floor である。
