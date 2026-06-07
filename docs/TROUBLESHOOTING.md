# トラブルシューティング (Troubleshooting)

ECC ユーザーに影響しうる現在の Claude Code bug 向けに、コミュニティが報告した回避策。

これらは ECC の bug ではなく、上流の Claude Code 動作です。以下の項目は、Claude Code `v2.1.79`（macOS、hook 多用、MCP connector 有効）において [issue #644](https://github.com/affaan-m/everything-claude-code/issues/644) で収集された本番検証済み回避策の要約です。上流修正が入るまでの実用的な暫定策として扱ってください。

## 未解決 Claude Code Bug 向けコミュニティ回避策 (Community Workarounds For Open Claude Code Bugs)

### 成功している hook に誤った "Hook Error" ラベル (False "Hook Error" labels on otherwise successful hooks)

**症状:** Hook は正常に実行されるが、Claude Code の transcript に `Hook Error` が表示される。

**有効な対処:**

- hook 開始時に stdin を消費する（shell hook では `input=$(cat)`）ことで、親プロセスが未消費パイプを見ないようにする。
- 単純な allow/block hook では、人間が読める診断を stderr に送り、hook 実装が明示的に structured stdout を要求しない限り stdout は静かに保つ。
- 対処不要な子プロセスの stderr はリダイレクトする。
- 正しい終了コードを使う：`0` は allow、`2` は block、その他の非ゼロ終了は error として扱われる。

**例:**

```bash
# Good: block with stderr message and exit 2
input=$(cat)
echo "[BLOCKED] Reason here" >&2
exit 2
```

### `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` による予想より早い compaction (Earlier-than-expected compaction with `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE`)

**症状:** `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` を下げると、compaction が遅くなるのではなく早くなる。

**有効な対処:**

- 一部の現在の Claude Code ビルドでは、低い値が compaction 閾値を下げる可能性がある。
- 作業余地を増やしたい場合は `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` を削除し、論理的なタスク境界で手動 `/compact` を優先する。
- auto-compact 閾値を無理に下げる代わりに、ECC の `strategic-compact` ガイダンスを使う。

### MCP connector が接続済みに見えるが compaction 後に失敗 (MCP connectors look connected but fail after compaction)

**症状:** Gmail や Google Drive の MCP ツールが、UI では connector が認証済みに見えても compaction 後に失敗する。

**有効な対処:**

- compaction 後に影響を受けた connector をオフにしてから再度オンにする。
- Claude Code ビルドが対応していれば、compaction 後に connector 認証を再確認するよう警告する `PostCompact` reminder hook を追加する。
- これは恒久的な修正ではなく、auth 状態のリカバリ手順として扱う。

### Hook 編集がホットリロードされない (Hook edits do not hot-reload)

**症状:** `settings.json` の hook 変更がセッション再起動まで反映されない。

**有効な対処:**

- hook 変更後に Claude Code セッションを再起動する。
- 上級ユーザーは `kill -HUP $PPID` を使ったローカル `/reload` コマンドをスクリプト化することもあるが、shell 依存で普遍的に信頼できないため ECC は同梱しない。

### 繰り返し `529 Overloaded` レスポンス (Repeated `529 Overloaded` responses)

**症状:** hook/tool/context 圧力が高い状態で Claude Code が失敗し始める。

**有効な対処:**

- セットアップが対応していれば `ENABLE_TOOL_SEARCH=auto:5` で tool-definition 圧力を下げる。
- 日常作業では `MAX_THINKING_TOKENS` を下げる。
- セットアップが露出していれば `CLAUDE_CODE_SUBAGENT_MODEL=haiku` など安価なモデルに subagent 作業をルーティングする。
- プロジェクトごとに未使用の MCP server を無効化する。
- auto-compaction を待つ代わりに、自然なブレークポイントで手動 compact する。

## 関連 ECC Docs (Related ECC Docs)

- より短い hook/compaction/MCP リカバリチェックリストは [hook-bug-workarounds.md](./hook-bug-workarounds.md)。
- ECC の hook ライフサイクルと終了コード動作は [hooks/README.md](../hooks/README.md)。
- コストと context 管理設定は [token-optimization.md](./token-optimization.md)。
- 元の報告と検証環境は [issue #644](https://github.com/affaan-m/everything-claude-code/issues/644)。
