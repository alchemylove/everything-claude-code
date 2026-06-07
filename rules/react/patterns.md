---
paths:
  - "**/*.tsx"
  - "**/*.jsx"
  - "**/components/**/*.ts"
  - "**/components/**/*.js"
  - "**/app/**/*.tsx"
  - "**/pages/**/*.tsx"
---
# React パターン (React Patterns)

> このファイルは [typescript/patterns.md](../typescript/patterns.md) および [common/patterns.md](../common/patterns.md) を拡張し、React 固有の内容を追加する。hook 固有の rule は [hooks.md](./hooks.md) を参照。

## Container / Presentational 分割 (Container / Presentational Split)

Container component は data fetching、state、side effect を所有する。Presentational component は props を受け取って render する — service call なし、local UI state 以外の hook なし。

```tsx
// Container — owns data
export function UserPage({ userId }: { userId: string }) {
  const { data: user, isLoading } = useUser(userId);
  if (isLoading) return <Spinner />;
  if (!user) return <NotFound />;
  return <UserCard user={user} onSelect={handleSelect} />;
}

// Presentational — pure
export function UserCard({ user, onSelect }: { user: User; onSelect: (id: string) => void }) {
  return <button onClick={() => onSelect(user.id)}>{user.name}</button>;
}
```

## State の置き場所の決定木 (State Location Decision Tree)

1. 1 component だけが使う → その中の `useState`
2. 親 + 少数の子が使う → 最も近い共通 ancestor に lift し props で渡す
3. 離れた branch 全体で使う → React Context **低頻度 read のみ**（theme、auth、locale）
4. tree 全体で共有する高頻度 update → external store（Zustand、Jotai、Redux Toolkit）
5. server 由来 data → server-state library（TanStack Query、SWR、RSC fetch）— application state ではない

頻繁に変わる値に Context を誤用すると、update のたびにすべての consumer が re-render する。

## Server / Client Component 境界（RSC、Next.js App Router）(Server / Client Component Boundary (RSC, Next.js App Router))

- Server Component がデフォルト — server で実行し client に ship せず、直接 `await` できる
- Client Component は file 先頭の `"use client"` で opt-in
- data は下向き: Server Component は Client Component を render し serializable props を渡せる
- Client Component は Server Component を import できないが、`children` や named slot 経由で受け取れる

```tsx
// Server (default)
export default async function Page() {
  const user = await fetchUser();
  return <UserClient user={user} />;
}

// Client
"use client";
export function UserClient({ user }: { user: User }) {
  const [tab, setTab] = useState("profile");
  return <Tabs value={tab} onChange={setTab}>{user.name}</Tabs>;
}
```

- Client Component file から `"server-only"` package（DB client、secret）を import しない — Server Component または Server Action で包む
- 機微 module には `import "server-only"` を付け、client file が import したら bundler が error にする

## Suspense + Error Boundary

すべての Suspense boundary の上に Error Boundary が必要。この pair が両方の state を扱う。

```tsx
<ErrorBoundary fallback={<ErrorView />}>
  <Suspense fallback={<Skeleton />}>
    <UserDetails id={id} />
  </Suspense>
</ErrorBoundary>
```

- Suspense boundary は route root ではなく data が必要な場所の近くに置く
- 狭い boundary を複数置くと loaded content が段階的に現れる
- Error Boundary は Class Component である必要がある（React 19 では functional equivalent はまだない）または `react-error-boundary` などの library wrapper を使う

## Form

### Uncontrolled（React 19 + form actions）(Uncontrolled (React 19 + form actions))

明確な submit ステップがある form では uncontrolled input と form action を優先。browser が value を所有し、React は submit 時に `FormData` で読む。

```tsx
async function action(formData: FormData) {
  "use server";
  await saveUser({ name: String(formData.get("name")) });
}

export function UserForm() {
  return (
    <form action={action}>
      <input name="name" required />
      <button type="submit">Save</button>
    </form>
  );
}
```

### Controlled

value が他 UI を駆動する、real-time validation や formatting が必要な場合は controlled input を使う。

```tsx
const [email, setEmail] = useState("");
return <input value={email} onChange={(e) => setEmail(e.target.value)} />;
```

### Form Library

複雑な form（multi-step、dynamic field array、cross-field validation）には library を使う:

- React Hook Form — re-render 最小、uncontrolled-first
- TanStack Form — typed、framework-agnostic
- Final Form — subscription ベース re-render が重要な場合

## Data Fetching

| Strategy | When |
|---|---|
| RSC fetch（Server Component 内の `await`） | Next.js App Router の per-request data、client-side cache 不要 |
| TanStack Query | client-side cache、mutation、optimistic update、polling |
| SWR | 軽量 cache + revalidation、TanStack Query より単純 |
| `useEffect` 内の `fetch` | 避ける — race condition、cache なし、retry なし。one-off fire-and-forget のみ許容 |

real cache library があるのに `useEffect` で fetch しない — deduping、cache invalidation、error retry、Suspense 統合を扱う。

## List と Key (Lists and Keys)

- `key` は render 間で stable — reorder、insert、delete があり得る list で `index` は使わない
- `key` は sibling 間で unique、global ではない
- index key で reorder した list は child component の state が誤った row に付く

## 継承より合成 (Composition over Inheritance)

- slot スタイル合成には `children` を渡す
- パラメータ化レンダリングには render-prop function を渡す
- plug-in ポイントには component 型を渡す: `renderItem={UserRow}`
- 振る舞いを specialize するために component class を extend しない

## Compound Component

関連 control（Tabs、Accordion、Menu）には Context で state を共有する compound component を使う:

```tsx
<Tabs defaultValue="profile">
  <Tabs.List>
    <Tabs.Trigger value="profile">Profile</Tabs.Trigger>
    <Tabs.Trigger value="settings">Settings</Tabs.Trigger>
  </Tabs.List>
  <Tabs.Panel value="profile"><ProfileForm /></Tabs.Panel>
  <Tabs.Panel value="settings"><SettingsForm /></Tabs.Panel>
</Tabs>
```

## Portal

modal、tooltip、toast container など、親の `overflow: hidden` や `z-index` stacking context から escape する必要があるものには `createPortal` を使う。`index.html` に mount した stable DOM node に render する。

## Ref と Forwarding（React 19+）(Refs and Forwarding (React 19+))

React 19 では function component が通常の prop として `ref` を受け取れる — `forwardRef` は不要。

```tsx
export function Input({ ref, ...rest }: { ref?: React.Ref<HTMLInputElement> } & InputProps) {
  return <input ref={ref} {...rest} />;
}
```

React 18 の older codebase では依然として `forwardRef` が必要。

## スコープ外（ポインタ節）(Out of Scope (Pointer Sections))

### Next.js（App Router）(Next.js (App Router))

- Server Actions、Route Handlers、Middleware、Parallel/Intercepted Routes、streaming Metadata
- 別 framework 関心事として扱う — deep Next 固有 pattern を追加する場合は専用 `rules/nextjs/` track を提案
- 現時点では App Router 固有は Next.js 公式 docs に従う

### React Native

- platform 固有 import（`Platform.OS`、`.ios.tsx` / `.android.tsx`）、`StyleSheet`、navigation library（React Navigation、Expo Router）
- 別 track として扱う — `rules/react-native/` はまだない
- この file の React core hooks/patterns は依然として適用される

## Skill 参照 (Skill Reference)

React 固有の deep dive は `skills/react-patterns/SKILL.md`。cross-framework frontend 関心事は `skills/frontend-patterns/SKILL.md`。accessibility は `skills/accessibility/SKILL.md`。
