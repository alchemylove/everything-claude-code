---
paths:
  - "**/*.tsx"
  - "**/*.jsx"
  - "**/components/**/*.ts"
  - "**/app/**/*.ts"
  - "**/pages/**/*.ts"
---
# React セキュリティ (React Security)

> このファイルは [typescript/security.md](../typescript/security.md) および [common/security.md](../common/security.md) を拡張し、React 固有の内容を追加する。

## `dangerouslySetInnerHTML` 経由の XSS

CRITICAL。prop 名は意図的に scary — すべての使用を code review の停止点として扱う。

```tsx
// CRITICAL: unsanitized user input
<div dangerouslySetInnerHTML={{ __html: userBio }} />

// CORRECT options:
// 1. Render as text
<div>{userBio}</div>

// 2. Render parsed markdown via a library that sanitizes
<ReactMarkdown>{userBio}</ReactMarkdown>

// 3. If raw HTML is required, sanitize first with DOMPurify
import DOMPurify from "isomorphic-dompurify";
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(userBio) }} />
```

すべての `dangerouslySetInnerHTML` 呼び出しの audit checklist:

- input は常に我々の管理下か？ source を文書化する。
- user 由来なら: **同一 call site** で sanitize されているか？（API 境界での sanitize はすべての consumer が verified な場合のみ許容。）
- sanitizer config は denylist ではなく allowlist か？

## 危険な URL Scheme (Unsafe URL Schemes)

`href`、`src`、`xlink:href` の `javascript:` と `data:` URL は任意 code を実行する。

```tsx
// CRITICAL: javascript: URL injection
<a href={user.website}>Visit</a>   // if user.website = "javascript:alert(1)"

// CORRECT: validate scheme
function safeUrl(url: string): string | undefined {
  try {
    const parsed = new URL(url);
    if (["http:", "https:", "mailto:"].includes(parsed.protocol)) return url;
  } catch {
    return undefined;
  }
  return undefined;
}
<a href={safeUrl(user.website)}>Visit</a>
```

React は development で `href` の `javascript:` URL を warn するが runtime では block しない。`data:` URL なども通る。常に validate する。

## `rel` なしの `target="_blank"`

`rel="noopener noreferrer"` なしの `<a target="_blank">` は target page が `window.opener` にアクセスし navigation hijack できる。

```tsx
// WRONG
<a href={externalUrl} target="_blank">External</a>

// CORRECT
<a href={externalUrl} target="_blank" rel="noopener noreferrer">External</a>
```

modern browser は `target="_blank"` 時にデフォルトで `noopener` だが、browser default に依存しない — 明示する。

## Server Action の入力検証 (Server Action Input Validation)

Server Actions（`"use server"`）は public API endpoint と同じ trust level で動く。すべての input を validate する。

```tsx
"use server";
import { z } from "zod";

const Input = z.object({
  email: z.string().email(),
  age: z.number().int().min(0).max(120),
});

export async function updateUser(_state: unknown, formData: FormData) {
  const parsed = Input.safeParse({
    email: formData.get("email"),
    age: Number(formData.get("age")),
  });
  if (!parsed.success) return { error: parsed.error.flatten() };
  // ...
}
```

- action 内で authenticate — client-side route gate を信頼しない
- authorize: 現在 user が mutate する特定 record への permission があることを確認
- 機微 action には rate limit

## Env Var 経由の Secret 露出 (Secret Exposure via Env Vars)

prefix 付き env var は client bundle に含まれる。public として扱う。

| Framework | Public prefix | Private |
|---|---|---|
| Next.js | `NEXT_PUBLIC_*` | All others |
| Vite | `VITE_*` | `.env` server-side only |
| Create React App | `REACT_APP_*`, plus `NODE_ENV` and `PUBLIC_URL` | All others (anything without the `REACT_APP_` prefix is server-side only) |
| Remix | `process.env` access in `loader`/`action` only | Same |

```ts
// CRITICAL: secret leaked to client bundle
const apiKey = process.env.NEXT_PUBLIC_STRIPE_SECRET_KEY;
```

env var を触る PR ごとに audit: この文字列が public bundle にあれば問題か？

## 認証 / 認可 (Authentication / Authorization)

- session を `localStorage` に保存しない — 任意 XSS からアクセス可能。httpOnly secure cookie を使う。
- 機微 UI を gate する client-set state を信頼しない。JSX の render-gating は表示を防ぐだけで access は防がない — API が enforce する。
- CSRF: cookie ベース auth には CSRF token または `SameSite=Strict`/`Lax` cookie
- framework default を使わない form action には double-submit cookie または origin verification

## Content Security Policy（CSP）(Content Security Policy (CSP))

server-side で設定。React app の最低限許容 CSP:

```
default-src 'self';
script-src 'self' 'nonce-{REQUEST_NONCE}';
style-src 'self' 'unsafe-inline';
img-src 'self' data: https:;
connect-src 'self' https://api.example.com;
frame-ancestors 'none';
```

- `script-src` で `unsafe-inline` と `unsafe-eval` を避ける
- inline script がある SSR（Next.js streaming、hydration data）では per-request nonce — Next.js と Remix は nonce injection をサポート
- CSS-in-JS library では `style-src 'unsafe-inline'` が避けられないことが多い — tradeoff を文書化

## Object Spread 経由の Prototype Pollution

```tsx
// WRONG: untrusted JSON spread directly into state
const update = await req.json();
setState({ ...state, ...update });    // attacker controls __proto__

// CORRECT: parse with a schema, or guard keys
const Allowed = z.object({ name: z.string(), email: z.string().email() });
const parsed = Allowed.parse(await req.json());
setState({ ...state, ...parsed });
```

## SSR Template Injection

`renderToString` または `renderToPipeableStream` 使用時:

- JSX 内に render する値は React が escape — safe
- `dangerouslySetInnerHTML` に渡す値は escape されない — client と同じ rule
- React output 周りの手組み HTML wrapper は escape または sanitize — user input を surrounding HTML template に連結しない

## サードパーティ Component (Third-Party Components)

- UI library 追加前に `npm audit`
- library が input に内部で `dangerouslySetInnerHTML` を使わないか確認（例: rich text editor）
- version を pin し、major upgrade 前に changelog を review
- HTML string を prop で受け取る component に注意

## 本番での Source Map 露出 (Source Map Exposure in Production)

production build は source map なしで ship するか、error tracker（Sentry）に upload し public bundle から除去。public source map は内部 logic と file structure を漏らす。

## Agent サポート (Agent Support)

- codebase 全体の包括 security audit には `security-reviewer` agent
- React 固有 pattern と上記 rule の active code review には `react-reviewer` agent
