# Cross-Harness アーキテクチャ (Cross-Harness Architecture)

ECC は再利用可能な workflow レイヤーです。Harness は実行面です。

目標はエージェント作業の durable 部分を1リポジトリに保つこと：

- skills
- rules and instructions
- hooks（harness がサポートする場合）
- MCP configuration
- install manifests
- session and orchestration patterns

Claude Code、Codex、OpenCode、Cursor、Gemini、将来の harness は、
すべてのツールで新しい workflow モデルを要求する代わりに、エッジでそれらの asset を適応すべきです。

operator 向けサポートマトリクスと scorecard ワークフローは
[Harness Adapter Compliance Matrix](harness-adapter-compliance.md) を参照。
フルスタックプラットフォームフレーミングとプロダクト統合ループは
[ECC Platform Value Loop](platform-value-loop.md) を参照。

## ポータビリティモデル (Portability Model)

| Surface | Shared Source | Harness Adapter | Current Status |
|---------|---------------|-----------------|----------------|
| Skills | `skills/*/SKILL.md` | Claude plugin, Codex plugin, `.agents/skills`, Cursor skill copies, OpenCode plugin/config | Supported with harness-specific packaging |
| Rules and instructions | `rules/`, `AGENTS.md`, translated docs | Claude rules install, Codex `AGENTS.md`, Cursor rules, OpenCode instructions | Supported, but not identical across harnesses |
| Hooks | `hooks/hooks.json`, `scripts/hooks/` | Claude native hooks, OpenCode plugin events, Cursor hook adapter | Hook-backed in Claude/OpenCode/Cursor; instruction-backed in Codex |
| MCPs | `.mcp.json`, `mcp-configs/` | Native MCP config import per harness | Supported where the harness exposes MCP |
| Commands | `commands/`, CLI scripts | Claude slash commands, compatibility shims, CLI entrypoints | Supported, but command semantics vary |
| Sessions | `ecc2/`, session adapters, orchestration scripts | TUI/daemon, tmux/worktree orchestration, harness-specific runners | Alpha |

## 変更なしで渡るもの (What Travels Unchanged)

`SKILL.md` が最もポータブルな unit です。

良い ECC skill は：

- `name`、`description`、`origin` を持つ YAML frontmatter を使う
- skill をいつ使うかを記述する
- secret を埋め込まず必要な tool または connector を述べる
- 例を repo-relative または generic に保つ
- セクションが明確にラベルされていない限り harness のみの command 前提を避ける

同じソース skill は主に指示、制約、workflow 形状であるため複数 harness にインストールできます。

## 適応されるもの (What Gets Adapted)

各 harness は異なる loading と enforcement 動作を持ちます：

- Claude Code は plugin asset を読み込み、native hook 実行を持つ。
- Codex は `AGENTS.md`、plugin metadata、skills、MCP config を読むが、hook parity は instruction-driven。
- OpenCode は adapter レイヤー経由で ECC hook ロジックを再利用できる plugin/event システムを持つ。
- Cursor は独自の rule と hook レイアウトを使うため、ECC は `.cursor/` 配下に翻訳面を維持する。
- Gemini サポートは install/instruction 指向で、完全 hook parity ではなく compatibility 面として扱うべき。

Adapter は薄く保つべきです。共有振る舞いは `skills/`、`rules/`、`hooks/`、`scripts/`、`mcp-configs/` に属します。

## Hermes 境界 (Hermes Boundary)

Hermes は公開 ECC runtime ではありません。

Hermes は ECC asset を消費できる operator shell です：

- 選択した ECC skills を Hermes skills ディレクトリに import
- tool アクセスに ECC MCP 規約を使用
- 再利用可能 ECC パターン経由で chat、CLI、cron、handoff workflow をルーティング
- 繰り返しローカル operator 作業をサニタイズされた ECC skills に蒸留

公開リポジトリは再利用可能パターンを出荷し、ローカル Hermes 状態は出荷しません。

出荷する：

- サニタイズされたセットアップ docs
- repo-relative デモプロンプト
- 一般的 operator skills
- プライベート認証情報に依存しない例

出荷しない：

- OAuth token または API key
- 生の `~/.hermes` エクスポート
- 個人ワークスペース memory
- プライベートデータセット
- レビューされていないローカルのみ automation pack

## 実例 (Worked Example)

`skills/hermes-imports/SKILL.md` を harness 横断の同じ skill ソースとして使う。

ワークフローは：

1. durable 振る舞いを `skills/hermes-imports/SKILL.md` に一度執筆。
2. secret、ローカル path、生 operator memory を skill から除外。
3. 各 harness が skill の読み込み方法を適応させる。
4. ソース skill と harness 向け metadata を別々にテスト。

Claude Code は Claude plugin 面経由で skill を得、関連 hook を native に enforce できる。

Codex はリポジトリ指示、`.codex-plugin/plugin.json`、MCP reference config を読む。同じ skill ソースは依然としてワークフローを記述するが、Codex が native hook 面を追加しない限り hook parity は instruction-backed。

OpenCode は OpenCode package/plugin 面経由で skill を得る。Event handling は adapter レイヤー経由で ECC hook ロジックを再利用でき、skill テキストは変更なし。

同じワークフローの harness コピーを3つ編集する必要があるなら、共有ソースの場所が間違っています。ワークフローを `skills/` に戻し、harness エッジでは loading、event shape、command routing のみ適応する。

## 今日 vs 後日 (Today vs Later)

今日サポート：

- `skills/` の共有 skill ソース
- Claude Code plugin packaging
- Codex plugin metadata と MCP reference config
- OpenCode package/plugin 面
- Cursor 適応 rules、hooks、skills
- alpha Rust control plane としての `ecc2/`

まだ成熟中：

- すべての harness 横断の正確な hook parity
- Hermes への自動 skill sync
- `ecc2/` のリリース packaging
- cross-harness session resume 意味論
- より深い memory と operator planning レイヤー
- 外部プロダクトが skill pack、gated API、eval、case study を ECC に還元する
  完全プラットフォームループ

## 新規作業のルール (Rule For New Work)

ワークフローを追加するとき、まず durable 振る舞いを ECC に置く。

Harness 固有ファイルは次の場合のみ使う：

- 共有 asset の読み込み
- event shape の適応
- command 名のマッピング
- platform 制限の処理

ワークフローが1 harness でしか動かないなら、その境界を直接文書化する。
