# Hook Bug 回避策 (Hook Bug Workarounds)

hook 多用の ECC セットアップに影響しうる現在の Claude Code bug 向けに、コミュニティ検証済みの回避策。

このページは意図的に狭く設計されています。より長いトラブルシューティング面から、推測的または未サポートの設定アドバイスを繰り返さず、最高シグナルの運用修正のみを集めます。これらは ECC の bug ではなく、上流の Claude Code 動作です。

## このページを使うタイミング (When To Use This Page)

次を具体的にデバッグしているときにこのページを使ってください：

- 正常な hook 実行に誤った `Hook Error` ラベル
- 予想より早い compaction
- 認証済みに見えるが compaction 後に失敗する MCP connector
- ホットリロードされない hook 編集
- hook/tool 圧力が高い状態での繰り返し `529 Overloaded` レスポンス

より完全な ECC トラブルシューティング面は [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) を使用してください。

## 高シグナル回避策 (High-Signal Workarounds)

### 誤った `Hook Error` ラベル (False `Hook Error` labels)

有効な対処:

- shell hook 開始時に stdin を消費する（`input=$(cat)`）。
- hook が明示的に structured stdout を要求しない限り、単純な allow/block hook では stdout を静かに保つ。
- 人間が読める診断を stderr に送る。
- 正しい終了コードを使う：`0` allow、`2` block、その他の非ゼロ値は error として扱われる。

```bash
input=$(cat)
echo "[BLOCKED] Reason here" >&2
exit 2
```

### 予想より早い compaction (Earlier-than-expected compaction)

有効な対処:

- 下げるとビルドで compaction が早くなる場合は `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` を削除する。
- 自然なタスク境界で手動 `/compact` を優先する。
- 閾値を無理に下げる代わりに ECC の `strategic-compact` ガイダンスを使う。

### MCP 認証は生きているように見えるが compaction 後に失敗 (MCP auth looks live but fails after compaction)

有効な対処:

- compaction 後に影響を受けた connector をオフにしてから再度オンにする。
- Claude Code ビルドが対応していれば、connector 認証を再確認するよう伝える軽量な `PostCompact` reminder hook を追加する。
- これは恒久的な修正ではなく、リカバリリマインダーとして扱う。

### Hook 編集がホットリロードされない (Hook edits do not hot-reload)

有効な対処:

- hook 変更後に Claude Code セッションを再起動する。
- 上級ユーザーは shell ローカルの reload ヘルパーを使うこともあるが、shell と platform 依存のため ECC は同梱しない。

### 繰り返し `529 Overloaded` (Repeated `529 Overloaded`)

有効な対処:

- セットアップが対応していれば `ENABLE_TOOL_SEARCH=auto:5` で tool-definition 圧力を下げる。
- 日常作業では `MAX_THINKING_TOKENS` を下げる。
- セットアップが露出していれば `CLAUDE_CODE_SUBAGENT_MODEL=haiku` など安価なモデルに subagent 作業をルーティングする。
- プロジェクトごとに未使用の MCP server を無効化する。
- auto-compaction を待つ代わりに、自然なブレークポイントで手動 compact する。

## 関連 ECC Docs (Related ECC Docs)

- [TROUBLESHOOTING.md](./TROUBLESHOOTING.md)
- [token-optimization.md](./token-optimization.md)
- [hooks/README.md](../hooks/README.md)
- [issue #644](https://github.com/affaan-m/everything-claude-code/issues/644)
