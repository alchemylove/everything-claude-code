# Hooks システム (Hooks System)

## Hook 種別 (Hook Types)

- **PreToolUse**: ツール実行前（検証、パラメータ変更）
- **PostToolUse**: ツール実行後（自動フォーマット、チェック）
- **Stop**: セッション終了時（最終検証）

## 自動承認パーミッション (Auto-Accept Permissions)

慎重に使用する:
- 信頼できる明確な計画向けに有効化する
- 探索的作業では無効化する
- dangerously-skip-permissions フラグは使わない
- 代わりに `~/.claude.json` で `allowedTools` を設定する

## TodoWrite ベストプラクティス (TodoWrite Best Practices)

TodoWrite ツールを次の目的で使用する:
- 複数ステップタスクの進捗を追跡する
- 指示の理解を検証する
- リアルタイムのステアリングを可能にする
- 細かい実装ステップを表示する

Todo リストで明らかになること:
- 順序が狂ったステップ
- 欠けている項目
- 不要な余分な項目
- 粒度の誤り
- 要件の誤解
