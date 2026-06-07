---
description: "TypeScript hooks: common ルールの拡張"
globs: ["**/*.ts", "**/*.tsx", "**/*.js", "**/*.jsx"]
alwaysApply: false
---
# TypeScript/JavaScript Hooks (TypeScript/JavaScript Hooks)

> このファイルは common hooks ルールを TypeScript/JavaScript 固有の内容で拡張する。

## PostToolUse Hooks

`~/.claude/settings.json` で設定:

- **Prettier**: 編集後に JS/TS ファイルを auto-format
- **TypeScript check**: `.ts`/`.tsx` ファイル編集後に `tsc` を実行
- **console.log warning**: 編集ファイルの `console.log` を警告

## Stop Hooks

- **console.log audit**: セッション終了前に変更されたすべてのファイルの `console.log` をチェック
