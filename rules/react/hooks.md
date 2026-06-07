---
paths:
  - "**/*.tsx"
  - "**/*.jsx"
  - "**/hooks/**/*.ts"
  - "**/hooks/**/*.js"
  - "**/use-*.ts"
  - "**/use-*.tsx"
---
# React フック (React Hooks)

> このファイルは **React hooks**（`useState`、`useEffect`、`useMemo`、`useCallback`、custom hooks）を扱う — Claude Code の `hooks/` runtime system ではない。命名はこの repo 全体で使う per-language 規約 `rules/<lang>/hooks.md` に合わせている。
>
> このファイルは [typescript/patterns.md](../typescript/patterns.md) および [common/patterns.md](../common/patterns.md) を拡張し、React hooks 固有の内容を追加する。

## Hooks のルール (Rules of Hooks)

`eslint-plugin-react-hooks` を有効にし、`react-hooks/rules-of-hooks` を error に設定する。

1. hook は function component または別の hook のトップレベルでのみ呼ぶ
2. loop、conditional、nested function、early return の後では呼ばない
3. 毎 render で常に同じ順序で呼ぶ
4. React function component または custom hook（`use` で始まる function）の内側でのみ呼ぶ

```tsx
// WRONG: conditional hook
function Foo({ enabled }: { enabled: boolean }) {
  if (enabled) {
    const [x, setX] = useState(0); // rule violation
  }
}

// CORRECT: hook unconditional, condition inside
function Foo({ enabled }: { enabled: boolean }) {
  const [x, setX] = useState(0);
  if (!enabled) return null;
  return <span>{x}</span>;
}
```

## `useEffect` — 使わない場合 (When NOT to Use)

`useEffect` は external system（subscription、browser API、third-party library）との同期用。次には **適さない**:

- 導出 state — render 中に計算する
- レンダリング用の data 変換 — render 中に計算する
- prop 変更時の state リセット — 親に `key` を付けるか props から導出する
- state 変更の親への通知 — event handler で callback を呼ぶ
- app レベル singleton の初期化 — module 側または `main.tsx` で呼ぶ

```tsx
// WRONG: effect for derived state
const [fullName, setFullName] = useState("");
useEffect(() => {
  setFullName(`${first} ${last}`);
}, [first, last]);

// CORRECT: derive during render
const fullName = `${first} ${last}`;
```

## Dependency Array

- effect/callback 内で参照する reactive value はすべて dep array に含める
- `react-hooks/exhaustive-deps` lint rule を有効にする — 理由コメントなしで mute しない
- dep array が肥大化したら effect が多すぎる — 分割する
- dep に渡す function の stable identity: その function が別 hook の dep である、または memoized child に渡す場合のみ `useCallback` で包む

## Cleanup

subscription、interval、listener、in-flight request はすべて cleanup する。

```tsx
useEffect(() => {
  const controller = new AbortController();
  fetch(url, { signal: controller.signal }).then(handleResponse);
  return () => controller.abort();
}, [url]);
```

```tsx
useEffect(() => {
  const id = setInterval(tick, 1000);
  return () => clearInterval(id);
}, []);
```

cleanup 欠如 = dep 変更時の race condition、unmount 時の memory leak。

## `useMemo` と `useCallback` — 価値がある場合 (When Worth It)

デフォルト: **memoize しない**。次の場合のみ `useMemo` / `useCallback` を追加:

1. 値が `React.memo` された child の prop として渡され、identity が重要
2. 値が別の `useEffect` / `useMemo` / `useCallback` の dep
3. 計算が計測上高コスト（仮定で profile する）

早すぎる memoization はノイズを増やし、bug を隠し、置き換える recompute より遅くなることもある。

## Custom Hook

custom hook に切り出す場合:

- 同じ hook 列（state + effect + computed）が 2+ component に現れる
- logic に明確で名付け可能な目的がある（`useDebounce`、`useOnClickOutside`、`useLocalStorage`）
- component から独立して logic を test したい

切り出さない場合:

- caller が 1 つだけ — inline する
- 「hook」が別名の `useState` だけ — 間接化だけで価値なし

```tsx
export function useDebounce<T>(value: T, delay: number): T {
  const [debounced, setDebounced] = useState(value);
  useEffect(() => {
    const id = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(id);
  }, [value, delay]);
  return debounced;
}
```

## `useState` パターン (Patterns)

- mount 時のみ prop から initial state: 計算が高コストなら function を渡す `useState(() => computeInitial(prop))`
- 新 state が旧 state に依存する場合は functional updater: `setCount(c => c + 1)` — async や batched context 内で `setCount(count + 1)` は使わない
- 関連 state を 1 object にまとめるのは常に一緒に変わる場合のみ；そうでなければ複数 `useState` に分ける
- state transition が previous state に条件付き、または関連値が 3+ ある場合は `useReducer`

## `useRef` パターン (Patterns)

- imperative API 用 DOM ref（focus、scroll、third-party lib）
- re-render を起こさない mutable container（timer id、previous value、「is mounted」フラグ）
- render 中に `ref.current` を読み書きしない — effect または event handler 内のみ
- `useImperativeHandle` は child API を parent ref に公開するときのみ — 最後の手段

## `useSyncExternalStore`

external store（browser API、third-party state lib、custom event emitter）を subscribe する hook。concurrent rendering と安全に external state を扱う supported な方法。

```tsx
const isOnline = useSyncExternalStore(
  (cb) => {
    window.addEventListener("online", cb);
    window.addEventListener("offline", cb);
    return () => {
      window.removeEventListener("online", cb);
      window.removeEventListener("offline", cb);
    };
  },
  () => navigator.onLine,
  () => true,
);
```

## React 19 の追加 (React 19 Additions)

- `use()` — promise と context を inline で unwrap；条件付きで使える唯一の hook
- `useFormStatus()` / `useFormState()`（または `useActionState`）— prop drilling なしの form submission state
- `useOptimistic()` — server action pending 中の optimistic UI update
- `useTransition()` — 非 urgent state update をマークし urgent を responsive に保つ

プロジェクトが React 19+ を target なら、手組み equivalent よりこれらを優先する。

## Stale Closure トラップ (Stale Closure Trap)

async handler と interval は作成された render の値を capture する。修正方法:

1. `setState` の functional updater form を使う
2. 変わる値を `useEffect` の dep array に入れ handler を再構築する
3. sync された ref から読む

## Lint 設定 (Lint Configuration)

必須 rule:

```json
{
  "rules": {
    "react-hooks/rules-of-hooks": "error",
    "react-hooks/exhaustive-deps": "warn"
  }
}
```

新規 code では CI で `exhaustive-deps` warning を error として扱う。
