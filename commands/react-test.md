---
description: React 向けに TDD workflow を強制する。React Testing Library でテストを先に書き（behavior 重視、accessibility 優先）、その後 component を実装する。Vitest または Jest を検出し、coverage 目標を検証する。
---

# React TDD コマンド (React TDD Command)

このコマンドは、実行時に検出した React Testing Library と Vitest または Jest を用いて、React の test-driven development を強制する。

## このコマンドの内容 (What This Command Does)

1. **Component シグネチャの定義**: component、prop type、export を scaffold
2. **Behavior テストを先に記述**: RTL クエリ（role 優先）、`userEvent`、ネットワークは MSW — RED
3. **テストの実行**: 正しい理由で失敗することを確認
4. **最小実装**: 通過に足るだけ — GREEN
5. **リファクタ**: テストを green のまま改善
6. **Coverage の確認**: [rules/react/testing.md](../rules/react/testing.md) の目標を満たす

## 使用タイミング (When to Use)

`/react-test` を使う場面:

- 新しい React component または custom hook を実装するとき
- 未テストの component に coverage を追加するとき
- bug 修正（再現する失敗テストを先に書く）
- フォーム、state machine、accessibility が重要な UI を構築するとき
- RTL + Vitest/Jest workflow のオンボーディング

## TDD サイクル (TDD Cycle)

```
RED      -> Write failing test for the next behavior
GREEN    -> Implement minimal component code to pass
REFACTOR -> Improve component, tests stay green
REPEAT   -> Next behavior
```

## Runner 検出 (Runner Detection)

```bash
test -f vitest.config.ts -o -f vitest.config.js -o -f vite.config.ts   # Vitest
grep -l '"jest"' package.json                                          # Jest
```

新しい Vite ベースのプロジェクトでは Vitest を優先し、既存セットアップでは Jest を尊重する。

## セッション例 (Example Session)

````text
User: /react-test I need a SearchInput component with debounced search

Agent:
# TDD Session: SearchInput

## Step 1: Define Signature

```tsx
// src/components/SearchInput.tsx
type Props = {
  onSearch: (query: string) => void;
  placeholder?: string;
  debounceMs?: number;
};

export function SearchInput({ onSearch, placeholder, debounceMs }: Props) {
  throw new Error("not implemented");
}
```

## Step 2: Write Behavior Tests (RED)

```tsx
// src/components/SearchInput.test.tsx
import { describe, expect, test, vi } from "vitest";
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { SearchInput } from "./SearchInput";

describe("SearchInput", () => {
  test("renders with placeholder", () => {
    render(<SearchInput onSearch={() => {}} placeholder="Search users" />);
    expect(screen.getByPlaceholderText("Search users")).toBeInTheDocument();
  });

  test("calls onSearch after typing", async () => {
    vi.useFakeTimers();
    const user = userEvent.setup({ advanceTimers: vi.advanceTimersByTime });
    const onSearch = vi.fn();
    render(<SearchInput onSearch={onSearch} debounceMs={300} />);

    await user.type(screen.getByRole("textbox"), "alice");

    expect(onSearch).not.toHaveBeenCalled();        // before debounce
    vi.advanceTimersByTime(300);
    expect(onSearch).toHaveBeenCalledWith("alice"); // after debounce

    vi.useRealTimers();
  });

  test("does not call onSearch when typing pauses then continues", async () => {
    vi.useFakeTimers();
    const user = userEvent.setup({ advanceTimers: vi.advanceTimersByTime });
    const onSearch = vi.fn();
    render(<SearchInput onSearch={onSearch} debounceMs={300} />);

    await user.type(screen.getByRole("textbox"), "ali");
    vi.advanceTimersByTime(200);                    // mid-debounce
    await user.type(screen.getByRole("textbox"), "ce");
    vi.advanceTimersByTime(300);

    expect(onSearch).toHaveBeenCalledTimes(1);
    expect(onSearch).toHaveBeenCalledWith("alice");

    vi.useRealTimers();
  });

  test("is keyboard reachable and accessible", () => {
    render(<SearchInput onSearch={() => {}} />);
    const input = screen.getByRole("textbox");
    input.focus();
    expect(input).toHaveFocus();
  });
});
```

## Step 3: Run Tests — Verify FAIL

```bash
$ vitest run src/components/SearchInput.test.tsx

× src/components/SearchInput.test.tsx (4 tests) ✘ Error: not implemented
```

✓ Tests fail as expected.

## Step 4: Implement Minimal Code (GREEN)

```tsx
import { useEffect, useState } from "react";

export function SearchInput({ onSearch, placeholder, debounceMs = 300 }: Props) {
  const [query, setQuery] = useState("");

  useEffect(() => {
    const id = setTimeout(() => onSearch(query), debounceMs);
    return () => clearTimeout(id);
  }, [query, onSearch, debounceMs]);

  return (
    <input
      type="text"
      value={query}
      placeholder={placeholder}
      onChange={(e) => setQuery(e.target.value)}
    />
  );
}
```

## Step 5: Run Tests — Verify PASS

```bash
$ vitest run src/components/SearchInput.test.tsx

✓ src/components/SearchInput.test.tsx (4 tests) 47ms
```

## Step 6: Coverage

```bash
$ vitest run --coverage src/components/SearchInput.test.tsx

% Stmts: 100  % Branch: 100  % Funcs: 100  % Lines: 100
```

## TDD Complete!
````

## テストパターン (Test Patterns)

### Behavior、implementation ではない (Behavior, not implementation)

`getByRole`、`getByLabelText`、`getByText` を使う。`container.querySelector` や component state の assertion は避ける。

### テストごとに `userEvent.setup()`

```tsx
const user = userEvent.setup();
await user.click(screen.getByRole("button", { name: /save/i }));
```

### ネットワークは MSW

```tsx
beforeAll(() => server.listen({ onUnhandledRequest: "error" }));
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

server.use(http.post("/api/users", () => HttpResponse.json({ id: "1" }, { status: 201 })));
```

### Custom hooks

```tsx
const { result } = renderHook(() => useCounter(0));
act(() => result.current.increment());
expect(result.current.count).toBe(1);
```

### Accessibility

```tsx
import { axe } from "vitest-axe";
expect(await axe(container)).toHaveNoViolations();
```

## Coverage 目標 (Coverage Targets)

| Layer | Target |
|---|---|
| Pure utilities | >=90% |
| Custom hooks | >=85% |
| Presentational components | >=80% |
| Container components | >=70% |
| Pages | E2E covered separately |

CI で閾値を強制するには `vitest.config.ts` / `jest.config.js` を設定する。

## 避けるべきアンチパターン (Anti-Patterns to Avoid)

- `container.querySelector(...)` — accessibility クエリをバイパスする
- render 回数の assertion
- `react` 自体のモック（`jest.mock("react", ...)`）
- デフォルトで child component をモック（重い副作用がある child のみ）
- `act()` warning の無視 — 実際の bug を示す
- レンダー済み component の snapshot テスト（脆く、形式的承認になりがち）— Playwright/Cypress の visual diff を使う

## テストコマンド (Test Commands)

```bash
# Vitest
vitest                              # watch
vitest run                          # one-shot
vitest run --coverage               # with coverage
vitest run path/to/file.test.tsx    # single file

# Jest
jest --watch
jest --coverage
jest path/to/file.test.tsx

# CI mode
CI=true vitest run --coverage
```

## 関連コマンド (Related Commands)

- `/react-build` — テスト実行前にビルドエラーを修正
- `/react-review` — 実装後にレビュー
- `verification-loop` skill — フル verification loop

## 関連 (Related)

- Skills: `skills/react-testing/`, `skills/tdd-workflow/`, `skills/accessibility/`, `skills/e2e-testing/`
- Rules: `rules/react/testing.md`
- Agents: `react-reviewer`（テスト品質をレビュー）、`tdd-guide`（TDD プロセスを強制）
