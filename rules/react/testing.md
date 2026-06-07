---
paths:
  - "**/*.test.tsx"
  - "**/*.test.jsx"
  - "**/*.spec.tsx"
  - "**/*.spec.jsx"
  - "**/__tests__/**/*.ts"
  - "**/__tests__/**/*.tsx"
---
# React テスト (React Testing)

> このファイルは [typescript/testing.md](../typescript/testing.md) および [common/testing.md](../common/testing.md) を拡張し、React 固有の内容を追加する。

## Library 選択 (Library Choice)

- **React Testing Library (RTL)** — component test の標準。render された DOM 経由で挙動を test する。
- **Vitest** — 新規 Vite ベース project では preferred runner。Jest より高速、native ESM、同じ API。
- **Jest** — Next.js / CRA project では依然 default。RTL は同一。
- **Playwright Component Testing** — real browser engine が必要な component test（animation、layout、complex events）
- **Cypress Component Testing** — 代替 real-browser component runner

project ごとに component test runner は 1 つ — 同一 repo で RTL + Playwright CT を混在させない。

## 基本原則 (Core Principle)

user が見て操作するものを test し、implementation detail は test しない。

- まず accessible role で query、次に label、次に text — 他に合わない場合のみ `data-testid` に fallback
- internal state、child に渡した props、どの hook が呼ばれたかを assert しない
- refactor しても test が壊れない = test は挙動を test していた；それが goal

## Query 優先順位 (Query Priority)

RTL は 3 系統の query を提供。上からこの優先順:

1. **Accessible to everyone**
   - `getByRole(role, { name })` — 第一選択
   - `getByLabelText` — form input 用
   - `getByPlaceholderText` — label がない場合（label を追加する）
   - `getByText` — 非 interactive text 用
   - `getByDisplayValue` — 現在値のある form field 用

2. **Semantic queries**
   - `getByAltText` — image 用
   - `getByTitle` — 最後の手段、accessibility 価値低

3. **Test IDs**
   - `getByTestId("some-id")` — 上記が使えない場合のみ escape hatch

`getBy*` は一致なしで throw。`queryBy*` は null を返す（不在 assert 用）。`findBy*` は promise（async 用）。

## User Interaction

`fireEvent` より `userEvent` を優先。`userEvent` は real browser シーケンス（focus、keydown、beforeinput、input、keyup）を模倣 — `fireEvent` は単一 synthetic event。

```tsx
import userEvent from "@testing-library/user-event";

test("submits the form", async () => {
  const user = userEvent.setup();
  render(<UserForm onSubmit={handleSubmit} />);

  await user.type(screen.getByLabelText("Email"), "user@example.com");
  await user.click(screen.getByRole("button", { name: /save/i }));

  expect(handleSubmit).toHaveBeenCalledWith({ email: "user@example.com" });
});
```

- `userEvent` 呼び出しは常に `await` — async
- 各 test 先頭で `userEvent.setup()` を 1 回呼び、返った `user` を reuse

## 非同期 Assertion (Async Assertions)

```tsx
// WRONG: synchronous query for async-rendered content
expect(screen.getByText("Loaded")).toBeInTheDocument();   // throws — not in DOM yet

// CORRECT: findBy* (returns a promise, retries)
expect(await screen.findByText("Loaded")).toBeInTheDocument();

// CORRECT: waitFor for non-element assertions
await waitFor(() => expect(saveSpy).toHaveBeenCalled());
```

- async element 出現には `findBy*`
- side effect など他 matcher の async expectation には `waitFor`
- `setTimeout` + assertion は使わない — flaky

## MSW による Network Mock (Network Mocking with MSW)

network 境界に触れる test には Mock Service Worker。MSW は network layer で動き、component、hook、fetch library はすべて production と同様に振る舞う。

```tsx
// test setup
import { setupServer } from "msw/node";
import { http, HttpResponse } from "msw";

const server = setupServer(
  http.get("/api/users/:id", ({ params }) =>
    HttpResponse.json({ id: params.id, name: "Alice" }),
  ),
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

test ごとの override:

```tsx
test("renders error on 500", async () => {
  server.use(http.get("/api/users/:id", () => new HttpResponse(null, { status: 500 })));
  render(<UserPage id="1" />);
  expect(await screen.findByText(/something went wrong/i)).toBeInTheDocument();
});
```

## Component の Snapshot Test は避ける (Avoid Snapshot Tests for Components)

render 出力の snapshot は brittle、review しにくく、reviewer が rubber-stamp しがち。次の場合のみ使う:

- 純粋 data serialization（例: stable string を出す transformer）
- 非 visual 出力の意図しない regression 検出

component visual regression には Playwright / Cypress / Percy screenshot — DOM diff ではなく actual visual diff。

## Test Setup Helper

provider は 1 回 wrap:

```tsx
function renderWithProviders(ui: React.ReactElement) {
  return render(
    <QueryClientProvider client={new QueryClient()}>
      <ThemeProvider theme={lightTheme}>
        <Router>{ui}</Router>
      </ThemeProvider>
    </QueryClientProvider>,
  );
}
```

`test-utils.tsx` から export し everywhere で使う。

## Custom Hook Test

RTL の `renderHook` を使う:

```tsx
import { renderHook, act } from "@testing-library/react";

test("useCounter increments", () => {
  const { result } = renderHook(() => useCounter());
  act(() => result.current.increment());
  expect(result.current.count).toBe(1);
});
```

- state 変更呼び出しは常に `act` で wrap
- internal implementation ではなく public hook API 経由で test

## Accessibility Assertion

```tsx
import { axe } from "vitest-axe";   // or jest-axe

test("UserCard has no a11y violations", async () => {
  const { container } = render(<UserCard user={mockUser} />);
  expect(await axe(container)).toHaveNoViolations();
});
```

component test で axe assertion を実行 — missing label、ARIA misuse、color contrast（限定的）を catch。

## Playwright / Cypress を使うタイミング (When to Reach for Playwright / Cypress)

RTL + JSDOM の component test ではできない:

- real layout（flexbox、grid、viewport 依存 rendering）
- scrolling、drag-and-drop、clipboard からの paste
- browser-native animation、CSS transitions
- cross-frame interaction（iframe、popup）

それらには Playwright Component Testing または E2E Playwright/Cypress。 [e2e-testing skill](../../skills/e2e-testing/SKILL.md) を参照。

## カバレッジ目標 (Coverage Targets)

| Layer | Target |
|---|---|
| Pure utility functions | ≥90% |
| Custom hooks | ≥85% |
| Components (presentational) | ≥80% — behavior、lines ではない |
| Container components | ≥70% — golden path + error state |
| Pages (E2E covered separately) | route ごとに smoke test 最低限 |

## アンチパターン (Anti-Patterns)

- `container.querySelector` で assert — accessibility query を bypass
- render 回数で assert — implementation detail
- React hook を mock（`jest.mock("react", ...)`）— component を refactor
- デフォルトで child component を mock — integration を test し parent を isolation で test しない
- manual `act()` warning を無視 — real bug を示す

## Skill 参照 (Skill Reference)

E2E test 例、MSW pattern、accessibility test scaffolding は `skills/react-testing/SKILL.md` を参照。
