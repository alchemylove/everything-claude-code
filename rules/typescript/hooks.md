---
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.js"
  - "**/*.jsx"
---
# TypeScript/JavaScript Hooks

> 本ファイルは [common/hooks.md](../common/hooks.md) を TypeScript/JavaScript 固有の内容で拡張します。

## PostToolUse Hooks

`~/.claude/settings.json` で設定する:

- **Prettier**: 編集後に JS/TS ファイルを自動フォーマット
- **TypeScript check**: `.ts`/`.tsx` 編集後に `tsc` を実行
- **console.log warning**: 編集ファイル内の `console.log` について警告

## Stop Hooks

- **console.log audit**: セッション終了前に変更ファイルすべてで `console.log` をチェック
