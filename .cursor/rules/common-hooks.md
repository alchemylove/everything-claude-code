---
description: "Hooks system: 種類、auto-accept permissions、TodoWrite ベストプラクティス"
alwaysApply: true
---
# Hooks System (Hooks System)

## Hook 種類 (Hook Types)

- **PreToolUse**: tool 実行前（バリデーション、パラメータ変更）
- **PostToolUse**: tool 実行後（auto-format、チェック）
- **Stop**: セッション終了時（最終検証）

## Auto-Accept Permissions

慎重に使用:
- 信頼できる明確な plan 向けに有効化
- 探索的作業では無効化
- dangerously-skip-permissions フラグは使わない
- 代わりに `~/.claude.json` で `allowedTools` を設定

## TodoWrite ベストプラクティス (TodoWrite Best Practices)

TodoWrite tool で:
- 複数ステップのタスクの進捗を追跡
- 指示の理解を検証
- リアルタイムの steering を有効化
- 細かい実装ステップを表示

Todo list が明らかにするもの:
- 順序が狂ったステップ
- 欠けている項目
- 不要な余分な項目
- 粒度の誤り
- 誤解された要件
