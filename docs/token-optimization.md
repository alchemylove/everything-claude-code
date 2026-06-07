# Token 最適化ガイド (Token Optimization Guide)

token 消費を減らし、セッション品質を延ばし、日次制限内でより多くの作業を行うための実用設定と習慣。

> 関連: モデル選択戦略は `rules/common/performance.md`、自動 compaction 提案は `skills/strategic-compact/`。

---

## 推奨設定 (Recommended Settings)

ほとんどのユーザー向け推奨デフォルト。パワーユーザーはワークロードに応じてさらに調整可能 — 例えば単純タスクでは `MAX_THINKING_TOKENS` を下げ、複雑なアーキテクチャ作業では上げる。

`~/.claude/settings.json` に追加：

```json
{
  "model": "sonnet",
  "env": {
    "MAX_THINKING_TOKENS": "10000",
    "CLAUDE_CODE_SUBAGENT_MODEL": "haiku"
  }
}
```

### 各設定の効果 (What each setting does)

| Setting | Default | Recommended | Effect |
|---------|---------|-------------|--------|
| `model` | opus | **sonnet** | Sonnet はコーディングタスクの約80%を十分こなす。複雑な推論には `/model opus`。約60%コスト削減。 |
| `MAX_THINKING_TOKENS` | 31,999 | **10,000** | Extended thinking は内部推論にリクエストあたり最大31,999 output token を予約。これを下げると隠れコストを約70%削減。些細なタスクでは `0` で無効化。 |
| `CLAUDE_CODE_SUBAGENT_MODEL` | _(inherits main)_ | **haiku** | Subagent（Task tool）はこのモデルで実行。Haiku は約80%安く、探索、ファイル読取、テスト実行に十分。 |
| `ECC_CONTEXT_MONITOR_COST_WARNINGS` | on | **off for subscription users** | context 枯渇、scope、loop 警告は残しつつ、エージェント向け API レート見積警告を抑制。 |

### auto-compaction 上書きに関するコミュニティ注記 (Community note on auto-compaction overrides)

最近の Claude Code ビルドでは、`CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` が compaction 閾値を下げるだけで、デフォルト未満の値が遅くではなく早く compact するというコミュニティ報告があります。セットアップでそうなる場合は上書きを削除し、手動 `/compact` と ECC の `strategic-compact` ガイダンスに頼ってください。[Troubleshooting](./TROUBLESHOOTING.md) を参照。

### Extended thinking の切り替え (Toggling extended thinking)

- **Alt+T**（Windows/Linux）または **Option+T**（macOS）— オン/オフ切り替え
- **Ctrl+O** — thinking 出力を表示（verbose モード）

---

## モデル選択 (Model Selection)

タスクに適したモデルを使う：

| Model | Best for | Cost |
|-------|----------|------|
| **Haiku** | Subagent 探索、ファイル読取、単純ルックアップ | Lowest |
| **Sonnet** | 日常コーディング、レビュー、テスト作成、実装 | Medium |
| **Opus** | 複雑なアーキテクチャ、多段推論、微妙な問題のデバッグ | Highest |

セッション中のモデル切り替え：

```
/model sonnet     # default for most work
/model opus       # complex reasoning
/model haiku      # quick lookups
```

---

## Context 管理 (Context Management)

### コマンド (Commands)

| Command | When to use |
|---------|-------------|
| `/clear` | 無関係なタスクの間。古い context はその後のすべてのメッセージで token を無駄にする。 |
| `/compact` | 論理的なタスク境界（計画後、デバッグ後、フォーカス切替前）。 |
| `/cost` | 現在セッションの token 支出を確認。 |

### API レートコスト見積警告 (API-rate cost estimate warnings)

ECC の context monitor はローカル hook telemetry から API レートコスト見積を emit できます。Claude サブスクリプションでそれらの見積が実際の請求を反映しない場合、エージェント向けコスト警告のみ無効化：

```bash
export ECC_CONTEXT_MONITOR_COST_WARNINGS=off
```

Windows PowerShell:

```powershell
[Environment]::SetEnvironmentVariable('ECC_CONTEXT_MONITOR_COST_WARNINGS', 'off', 'User')
```

これは context 枯渇警告、scope 警告、loop 警告、`/cost`、コスト telemetry ファイルは無効化しません。

### Strategic compaction

`strategic-compact` skill（`skills/strategic-compact/`）は auto-compaction に頼る代わりに論理的間隔で `/compact` を提案します。auto-compaction はタスク途中で発火しうります。hook セットアップは skill の README を参照。

**Compact するタイミング:**
- 探索後、実装前
- マイルストーン完了後
- デバッグ後、新しい作業を続ける前
- 大きな context シフトの前

**Compact しないタイミング:**
- 関連変更の実装途中
- アクティブな issue のデバッグ中
- マルチファイルリファクタリング中

### Subagent が context を保護 (Subagents protect your context)

メインセッションで多くのファイルを読む代わりに探索に subagent（Task tool）を使う。Subagent は20ファイルを読んでもサマリーだけ返す — メイン context はクリーンなまま。

---

## MCP Server 管理 (MCP Server Management)

有効な MCP server ごとに tool 定義が context window に追加されます。README の警告：**プロジェクトあたり10未満を維持**。

ヒント:
- `/mcp` でアクティブ server と context コストを確認
- ライブ runtime 変更には `/mcp` で Claude Code MCP server を無効化。Claude Code はそれらの runtime 無効化を `~/.claude.json` に永続化
- CLI ツールがあれば優先（GitHub MCP の代わりに `gh`、AWS MCP の代わりに `aws`）
- すでに読み込まれた Claude Code MCP server の無効化に `.claude/settings.json` や `.claude/settings.local.json` は頼らない；`/mcp` を使う
- `ECC_DISABLED_MCPS` は `install.sh`、`npx ecc-install`、Codex MCP マージなど install/sync flow 中の ECC 生成 MCP config 出力にのみ影響。ライブ Claude Code トグルではない
- `memory` MCP server はデフォルト設定だが skill、agent、hook では未使用 — 無効化を検討

---

## Agent Teams コスト警告 (Agent Teams Cost Warning)

[Agent Teams](https://code.claude.com/docs/en/agent-teams)（実験的）は複数の独立 context window を spawn します。各 teammate が別々に token を消費します。

- 並列性が明確な価値を持つタスク（マルチモジュール作業、並列レビュー）でのみ使用
- 単純な逐次タスクでは subagent（Task tool）の方が token 効率が良い
- 有効化: settings の `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`

---

## 将来: configure-ecc 統合 (Future: configure-ecc Integration)

`configure-ecc` install wizard はセットアップ中にこれらの環境変数をコストトレードオフの説明付きで設定できる可能性があります。制限に当たってからこれらの設定を発見するのではなく、新規ユーザーが初日から最適化できるようにします。

---

## クイックリファレンス (Quick Reference)

```bash
# Daily workflow
/model sonnet              # Start here
/model opus                # Only for complex reasoning
/clear                     # Between unrelated tasks
/compact                   # At logical breakpoints
/cost                      # Check spending

# Environment variables (add to ~/.claude/settings.json "env" block)
MAX_THINKING_TOKENS=10000
CLAUDE_CODE_SUBAGENT_MODEL=haiku
CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```
