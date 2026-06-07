---
description: React のビルド失敗（Vite、webpack、Next.js、CRA、Parcel、esbuild、Bun）を段階的に修正する — JSX/TSX のコンパイルエラー、hydration mismatch、server/client component boundary の失敗、型不足。最小限の surgical fix のため react-build-resolver agent を起動する。
---

# React ビルドと修正 (React Build and Fix)

このコマンドは **react-build-resolver** agent を起動し、最小限の変更で React のビルドエラーを段階的に修正する。

## このコマンドの内容 (What This Command Does)

1. **ビルドシステムの検出**: Vite、webpack、Next.js、CRA、Parcel、esbuild、Bun を識別
2. **ビルドの実行**: プロジェクトの build script を実行
3. **エラーの解析**: レイヤー別にグループ化（TypeScript / bundler config / runtime / hydration）
4. **段階的な修正**: 1 件ずつ修正し、変更のたびにビルドを再実行
5. **サマリーの報告**: 修正内容と残件を表示

## 使用タイミング (When to Use)

`/react-build` を使う場面:

- `npm run build`（または pnpm/yarn/bun 相当）が失敗したとき
- TypeScript または React のアップグレード後に JSX/TSX のコンパイルエラーが出たとき
- 実行時に Next.js の hydration mismatch エラーが出たとき
- App Router で Server/Client Component boundary エラーが出たとき
- `react`、`react-dom`、`@types/react`、または bundler のインストール・アップグレード後
- React 関連の型不足や "module not found" エラー

## スコープ (Scope)

このコマンドは **React の build/bundler/runtime hydration** 失敗を担当する。React を介さない純粋な TypeScript の型エラーには、代わりに `/build-fix`（汎用）を使う。

## 実行する診断コマンド (Diagnostic Commands Run)

```bash
# Project build script (preferred)
npm run build --if-present
pnpm build 2>/dev/null
yarn build 2>/dev/null
bun run build 2>/dev/null

# Standalone typecheck
npm run typecheck --if-present
tsc --noEmit -p tsconfig.json

# Bundler-specific fallback
next build                          # Next.js
vite build                          # Vite
react-scripts build                 # CRA
webpack --mode=production           # webpack
parcel build src/index.html         # Parcel
bun build ./src/index.tsx --outdir=dist
```

## セッション例 (Example Session)

````text
User: /react-build

Agent:
# React Build Resolution

## Build System Detected
Vite (vite.config.ts present, @vitejs/plugin-react in deps)

## Initial Diagnostics

```bash
$ npm run build
> tsc -b && vite build

src/components/UserCard.tsx:1:8 - error TS6133: 'React' is declared but its value is never read.
src/components/Modal.tsx:12:15 - error TS7016: Could not find a declaration file for module 'react-portal'.
src/pages/Home.tsx:42:5 - error: 'useState' is not defined
```

Errors found: 3

## Fix 1: Old JSX transform leftover

File: src/components/UserCard.tsx:1
Cause: `tsconfig.json` already uses `"jsx": "react-jsx"`; the explicit `import React` is unused.

```tsx
// Removed
- import React from 'react';
```

```bash
$ npm run build
# 2 errors remaining
```

## Fix 2: Missing types

File: src/components/Modal.tsx
Cause: `@types/react-portal` not installed.

```bash
$ npm i -D @types/react-portal
added 1 package
```

```bash
$ npm run build
# 1 error remaining
```

## Fix 3: Missing hook import

File: src/pages/Home.tsx
Cause: `useState` referenced but not imported.

```tsx
- import { useEffect } from "react";
+ import { useEffect, useState } from "react";
```

```bash
$ npm run build
# Build successful!
```

## Final Verification

```bash
$ npm run build
✓ built in 2.34s

$ npm test
✓ 47 tests passed
```

## Summary

| Metric | Count |
|--------|-------|
| Build errors fixed | 3 |
| Files modified | 2 |
| Dependencies added | 1 (@types/react-portal) |
| Remaining issues | 0 |

Build Status: PASS: SUCCESS
````

## よくあるエラーと修正 (Common Errors Fixed)

| Error | Typical Fix |
|---|---|
| `'React' is not defined` | Set `"jsx": "react-jsx"` in tsconfig (React 17+) |
| Missing `@types/react` | `npm i -D @types/react @types/react-dom` |
| `Unexpected token '<'` | Add `@vitejs/plugin-react` / `babel-loader` |
| `You're importing a component that needs useState` (Next.js) | Add `"use client"` or move hook to a Client Component child |
| `Module not found: Can't resolve 'fs'` (Next.js) | Remove `fs` import or move logic into Server Component / API route |
| `Hydration failed because the initial UI does not match` | Move `Date.now()`/`Math.random()`/`window.*` to `useEffect` |
| `Invalid hook call` | Multiple React copies — dedupe via `resolutions`/`overrides` |
| `Element type is invalid` | Default vs named import mismatch |

## 修正戦略 (Fix Strategy)

1. **コンパイルエラーを最優先** — コードはビルドできる必要がある
2. **Hydration エラーを次に** — 本番の正確性に影響する
3. **Bundler config をその次** — plugin/loader の正しさを復元
4. **1 件ずつ修正** — 各変更を検証
5. **最小限の変更** — 説明なしの `// @ts-ignore` は使わない
6. **修正のたびに再実行** — 新しいエラーをすぐ表面化させる

## 停止条件 (Stop Conditions)

agent は次の場合に停止して報告する:

- 同じエラーが 3 回試行後も残る
- 修正が解消したエラーより多くのエラーを導入した
- build resolution を超えるアーキテクチャ変更が必要（例: RSC boundary の再設計）
- bundler のバージョンがインストール済み React major をサポートしなくなった

## 関連コマンド (Related Commands)

- `/react-test` — ビルドが通ったあとにテストを実行
- `/react-review` — ビルド成功後にコード品質をレビュー
- `/build-fix` — 汎用ビルド修正（非 React）
- `verification-loop` skill — フル verification loop

## 関連 (Related)

- Agent: `agents/react-build-resolver.md`
- Skills: `skills/react-patterns/`, `skills/frontend-patterns/`
- Rules: `rules/react/coding-style.md`, `rules/react/patterns.md`
