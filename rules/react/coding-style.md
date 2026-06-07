---
paths:
  - "**/*.tsx"
  - "**/*.jsx"
  - "**/components/**/*.ts"
  - "**/components/**/*.js"
  - "**/hooks/**/*.ts"
  - "**/hooks/**/*.js"
---
# React コーディングスタイル (React Coding Style)

> このファイルは [typescript/coding-style.md](../typescript/coding-style.md) および [common/coding-style.md](../common/coding-style.md) を拡張し、React 固有の内容を追加する。

## ファイル拡張子 (File Extensions)

- JSX を含むファイルは 1 行 snippet でも `.tsx`
- 純粋な logic、JSX のない custom hook、型定義、utility には `.ts`
- source file と対応する `.test.tsx` / `.test.ts`
- プロジェクトが意図的に TypeScript を避ける場合のみ `.jsx` — 新規の untyped React file は review で指摘する

## 命名 (Naming)

- Component: symbol も file も `PascalCase`（`UserCard.tsx`、default export `UserCard`）
- Custom hook: symbol は `useCamelCase`、プロジェクト規約が kebab-case なら file も kebab-case（`use-debounce.ts` が `useDebounce` を export）
- Context: `<Domain>Context` symbol、`<Domain>Provider` provider component、`use<Domain>` consumer hook
- Event handler: component 内は `handleClick`、`handleSubmit`；受け取る prop は `onClick`、`onSubmit`
- Boolean prop: `isLoading`、`hasError`、`canSubmit` — boolean だけの `loading` や `error` は使わない

## Component の形 (Component Shape)

```tsx
type Props = {
  user: User;
  onSelect: (id: string) => void;
};

export function UserCard({ user, onSelect }: Props) {
  return (
    <button type="button" onClick={() => onSelect(user.id)}>
      {user.name}
    </button>
  );
}
```

- closed な component prop shape には `type Props = {}` を優先
- declaration merging で拡張する、または public API の extension point として export する場合のみ `interface` を使う
- parameter list で props を必ず destructure する — body 内で `props.user` にアクセスしない
- return type は JSX から暗黙推論（条件分岐 return で union が inference を混乱させる場合のみ `function Foo(): JSX.Element`）

## JSX

- 子要素のない tag は self-close: `<img />`、`<UserCard user={u} />`
- DOM element が不要なら wrapper `<div>` より fragment `<>...</>` を使う
- 条件付きレンダリング: boolean は `{condition && <Foo />}`、either/or は ternary、guard clause は early return
- 複数行に読める logic を JSX 内に書かない — return の上で const に抽出するか function に切り出す

```tsx
// Prefer
const greeting = user.isAdmin ? "Welcome, admin" : `Hello ${user.name}`;
return <h1>{greeting}</h1>;

// Over
return <h1>{user.isAdmin ? "Welcome, admin" : `Hello ${user.name}`}</h1>;
```

## Server / Client 境界（Next.js App Router、RSC）(Server / Client Boundary (Next.js App Router, RSC))

- 新規 file は Server Component をデフォルト — state、effects、refs、browser API、event handler を使う場合のみ `"use client"` を追加
- `"use client"` directive は import より前の 1 行目に置く
- `"use server"` action file 内から Client Component file を import しない
- server-only code を client module 経由で re-export しない — bundler が黙って含めてしまう

## インポート (Imports)

- React import を最初: `import { useState } from "react"`
- 次に third-party lib、次に absolute project import、最後に relative
- type-only import: `import type { ReactNode } from "react"` — ESLint の `consistent-type-imports` 設定時は runtime と type を 1 文に混ぜない

## Hooks の規律 (Hooks Discipline)

完全な ruleset は [hooks.md](./hooks.md) を参照。スタイル上の要点:

- custom hook は `use` で始める — `eslint-plugin-react-hooks` で強制
- すべての hook call を component 先頭、conditional logic の前にまとめる
- 1 行 wrapper の ad-hoc hook は作らない — 呼び出しを inline する

## State

- まず local（`useState`）、共有が必要なときだけ lift
- 多くの component が読む横断 state（theme、auth、i18n）に Context — 高頻度更新には使わない
- route 変更を跨いで persist する、tab 間同期、devtools で debug する state には external store（Zustand、Jotai、Redux Toolkit）
- 導出できる state を duplicate しない — render 中に計算する

## Class Component

新規 code では禁止。非 trivial な変更で触る legacy class component は function component に変換する。

## Component ごとのファイル構成 (File Layout per Component)

```
components/UserCard/
  UserCard.tsx
  UserCard.module.css   # or styled-components, or Tailwind classes inline
  UserCard.test.tsx
  index.ts              # re-export only
```

trivial な presentational piece は single-file component で問題ない。
