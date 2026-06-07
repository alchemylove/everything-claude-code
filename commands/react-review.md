---
description: hook の正しさ、render performance、server/client component boundary、accessibility、React 固有のセキュリティを含む包括的な React/JSX コードレビュー。react-reviewer agent を起動する（TSX/JSX 変更時は typescript-reviewer も併用）。
---

# React コードレビュー (React Code Review)

このコマンドは React 固有のコードレビューのため **react-reviewer** agent を起動する。`.tsx`/`.jsx` を触る pull request では、`react-reviewer` と `typescript-reviewer` の両方を実行すべき — それぞれが別 lane を担当する。

## このコマンドの内容 (What This Command Does)

1. **React 変更の特定**: `git diff` で変更された `.tsx`/`.jsx`（および React を含む `.ts`/`.js`）を検出
2. **Lint の実行**: `eslint-plugin-react-hooks` と `eslint-plugin-jsx-a11y` 付きで `eslint` を実行
3. **Typecheck**: `tsc --noEmit` またはプロジェクトの canonical typecheck コマンドを実行
4. **React lane のみレビュー**: Hook rules、RSC boundary、accessibility、render performance、React 固有のセキュリティ
5. **レポート生成**: 重大度（CRITICAL / HIGH / MEDIUM）で issue を分類

## 使用タイミング (When to Use)

`/react-review` を使う場面:

- PR または commit が `.tsx`/`.jsx` を触るとき
- React component、custom hook、page を書いた・変更したあと
- React コードをマージする前
- UI component の accessibility を監査するとき
- 新しい hook の rules-of-hooks と dependency の正しさをレビューするとき
- Next.js App Router の server/client component boundary を監査するとき

React import のない純粋な `.ts`/`.js` 変更には、`/code-review`（汎用）を使うか `typescript-reviewer` を直接起動する。

## スコープ: `/code-review` と TypeScript レビューとの違い (Scope vs `/code-review` and TypeScript Review)

| Tool | Scope |
|---|---|
| `react-reviewer` (this command) | Hooks rules、JSX、RSC、a11y、React 固有のセキュリティ、render perf |
| `typescript-reviewer` | 汎用 TS/JS — `any` の乱用、async の正しさ、Node セキュリティ |
| `security-reviewer` | プロジェクト全体のセキュリティ監査 |
| `/code-review` | 未コミット変更または PR の汎用レビュー |

TSX/JSX の PR では `react-reviewer` と `typescript-reviewer` の両方を起動する。各所見は設計上重複しない。

## レビューカテゴリ (Review Categories)

### CRITICAL（必須修正） (CRITICAL (Must Fix))

- サニタイズされていない入力への `dangerouslySetInnerHTML`
- 未検証のユーザー URL（`javascript:`、`data:`）を使った `href`/`src`
- 入力検証のない Server Action
- client bundle 内の secret（`NEXT_PUBLIC_*`、`VITE_*`、`REACT_APP_*`）
- セッショントークンを `localStorage`/`sessionStorage` に保存
- 条件付き hook 呼び出し（Rules of Hooks 違反）
- state の直接変更
- component または custom hook 外での hook 呼び出し

### HIGH（修正推奨） (HIGH (Should Fix))

- `useEffect`/`useMemo`/`useCallback` の deps 不足（根拠なく `exhaustive-deps` を無効化）
- 派生 state のための effect
- cleanup のない effect
- handler/interval での stale closure
- Client Component 内の server-only import
- Client Component への props 経由の機微データ漏洩
- 認証チェックのない Server Actions
- accessibility 違反（label 不足、非セマンティックなインタラクティブ要素、ARIA の誤用）
- 動的リストでの `key={index}`
- 重複 state、useEffect チェーン

### MEDIUM（検討） (MEDIUM (Consider))

- 計測された利得のない過剰 memoization
- memoized child への prop としてのインライン新規 object/function
- route root のみの Suspense（progressive reveal なし）
- virtualization のない長いリスト
- `useContext` 経由の高頻度値
- 非自明なフォームでの自作 validation
- 3 階層を超える prop drilling
- 200 行超の component
- 新規コードでの class component

## 実行する自動チェック (Automated Checks Run)

```bash
# Lint (required for any meaningful review)
npx eslint . --ext .tsx,.jsx,.ts,.js

# Typecheck (skip cleanly for JS-only projects)
npm run typecheck --if-present
[ -f tsconfig.json ] && tsc --noEmit -p tsconfig.json

# Targeted a11y rules
npx eslint . --rule 'jsx-a11y/alt-text: error' \
              --rule 'jsx-a11y/anchor-is-valid: error' \
              --rule 'jsx-a11y/click-events-have-key-events: error'

# Supply-chain
npm audit
```

`eslint-plugin-react-hooks` または `eslint-plugin-jsx-a11y` が未設定の場合、レビューはその gap を HIGH の config issue として報告し、続行する。

## 使用例 (Example Usage)

````text
User: /react-review

Agent:
# React Code Review Report

## Files Reviewed
- src/components/UserCard.tsx (modified)
- src/hooks/useUser.ts (new)

## Lint Results
PASS: eslint clean
PASS: typecheck clean

## Issues Found

[CRITICAL] Unsanitized dangerouslySetInnerHTML
File: src/components/UserCard.tsx:42
Issue: User-controlled bio rendered as raw HTML.
Why: XSS via stored script tags in user input.
Fix: Sanitize with DOMPurify or render as text:
```tsx
import DOMPurify from "isomorphic-dompurify";
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(user.bio) }} />
```

[HIGH] Effect cleanup missing
File: src/hooks/useUser.ts:18
Issue: `fetch` call without AbortController; setState on unmounted component possible.
Fix: Add AbortController and cleanup:
```ts
useEffect(() => {
  const ac = new AbortController();
  fetch(`/api/users/${id}`, { signal: ac.signal })
    .then(r => r.json())
    .then(setUser);
  return () => ac.abort();
}, [id]);
```

## Summary
- CRITICAL: 1
- HIGH: 1
- MEDIUM: 0

Recommendation: FAIL: Block merge until CRITICAL issue is fixed
````

## 承認基準 (Approval Criteria)

| Status | Condition |
|---|---|
| PASS: Approve | No CRITICAL or HIGH issues |
| WARNING: Warning | Only MEDIUM issues (merge with caution) |
| FAIL: Block | CRITICAL or HIGH issues found |

## 他コマンドとの連携 (Integration with Other Commands)

- ビルドが壊れている場合は先に `/react-build` を実行
- component テストが通ることを `/react-test` で確認
- マージ前に `/react-review` を実行
- 同一 PR の非 React 固有の懸念には `/code-review` を使用

## 関連 (Related)

- Agent: `agents/react-reviewer.md`
- Companion agent: `agents/typescript-reviewer.md`（TSX/JSX PR では併用）
- Skills: `skills/react-patterns/`, `skills/react-testing/`, `skills/accessibility/`
- Rules: `rules/react/`
