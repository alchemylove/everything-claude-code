---
name: coding-standards
description: 命名、可読性、不変性、コード品質レビューのベースライン規約。フレームワーク固有は frontend-patterns / backend-patterns を使用。coding standards, immutability, code quality.
---

# コーディング規約とベストプラクティス (Coding Standards & Best Practices)

プロジェクト横断で適用できる基本コーディング規約。

このスキルは共有の土台であり、詳細なフレームワークプレイブックではない。

- React、状態、フォーム、レンダリング、UI アーキテクチャには `frontend-patterns` を使用。
- repository/service レイヤー、エンドポイント設計、バリデーション、サーバー固有の関心事には `backend-patterns` または `api-design` を使用。
- フルスキルのウォークスルーではなく最短の再利用ルールレイヤーが必要な場合は `rules/common/coding-style.md` を使用。

## 有効化タイミング (When to Activate)

- 新しいプロジェクトやモジュールを開始するとき
- 品質と保守性のためコードをレビューするとき
- 既存コードを規約に合わせてリファクタリングするとき
- 命名、フォーマット、構造の一貫性を強制するとき
- リント、フォーマット、型チェックルールをセットアップするとき
- 新規コントリビューターをコーディング規約にオンボードするとき

## スコープの境界 (Scope Boundaries)

このスキルを有効化する対象:
- 説明的な命名
- 不変性のデフォルト
- 可読性、KISS、DRY、YAGNI の強制
- エラーハンドリングの期待とコードスメルレビュー

このスキルを主な情報源として使わない対象:
- React の構成、hooks、レンダリングパターン
- バックエンドアーキテクチャ、API 設計、データベースレイヤリング
- より狭い ECC スキルが既にある場合のドメイン固有フレームワークガイダンス

## コード品質の原則 (Code Quality Principles)

### 1. 可読性優先 (1. Readability First)
- コードは書くより読まれる
- 明確な変数名と関数名
- コメントより自己説明的なコードを優先
- 一貫したフォーマット

### 2. KISS (2. KISS)
- 動く最もシンプルな解決策
- 過剰設計を避ける
- 早すぎる最適化をしない
- 賢いコードより理解しやすいコード

### 3. DRY (3. DRY)
- 共通ロジックを関数に抽出
- 再利用可能なコンポーネントを作成
- モジュール間でユーティリティを共有
- コピペプログラミングを避ける

### 4. YAGNI (4. YAGNI)
- 必要になる前に機能を作らない
- 投機的な汎用性を避ける
- 必要になったときだけ複雑さを追加
- シンプルに始め、必要時にリファクタ

## TypeScript/JavaScript 規約 (TypeScript/JavaScript Standards)

### 変数命名 (Variable Naming)

```typescript
// PASS: GOOD: Descriptive names
const marketSearchQuery = 'election'
const isUserAuthenticated = true
const totalRevenue = 1000

// FAIL: BAD: Unclear names
const q = 'election'
const flag = true
const x = 1000
```

### 関数命名 (Function Naming)

```typescript
// PASS: GOOD: Verb-noun pattern
async function fetchMarketData(marketId: string) { }
function calculateSimilarity(a: number[], b: number[]) { }
function isValidEmail(email: string): boolean { }

// FAIL: BAD: Unclear or noun-only
async function market(id: string) { }
function similarity(a, b) { }
function email(e) { }
```

### 不変性パターン（重要）(Immutability Pattern (CRITICAL))

```typescript
// PASS: ALWAYS use spread operator
const updatedUser = {
  ...user,
  name: 'New Name'
}

const updatedArray = [...items, newItem]

// FAIL: NEVER mutate directly
user.name = 'New Name'  // BAD
items.push(newItem)     // BAD
```

### エラーハンドリング (Error Handling)

```typescript
// PASS: GOOD: Comprehensive error handling
async function fetchData(url: string) {
  try {
    const response = await fetch(url)

    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`)
    }

    return await response.json()
  } catch (error) {
    console.error('Fetch failed:', error)
    throw new Error('Failed to fetch data')
  }
}

// FAIL: BAD: No error handling
async function fetchData(url) {
  const response = await fetch(url)
  return response.json()
}
```

### async/await ベストプラクティス (Async/Await Best Practices)

```typescript
// PASS: GOOD: Parallel execution when possible
const [users, markets, stats] = await Promise.all([
  fetchUsers(),
  fetchMarkets(),
  fetchStats()
])

// FAIL: BAD: Sequential when unnecessary
const users = await fetchUsers()
const markets = await fetchMarkets()
const stats = await fetchStats()
```

### 型安全性 (Type Safety)

```typescript
// PASS: GOOD: Proper types
interface Market {
  id: string
  name: string
  status: 'active' | 'resolved' | 'closed'
  created_at: Date
}

function getMarket(id: string): Promise<Market> {
  // Implementation
}

// FAIL: BAD: Using 'any'
function getMarket(id: any): Promise<any> {
  // Implementation
}
```

## React ベストプラクティス (React Best Practices)

### コンポーネント構造 (Component Structure)

```typescript
// PASS: GOOD: Functional component with types
interface ButtonProps {
  children: React.ReactNode
  onClick: () => void
  disabled?: boolean
  variant?: 'primary' | 'secondary'
}

export function Button({
  children,
  onClick,
  disabled = false,
  variant = 'primary'
}: ButtonProps) {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={`btn btn-${variant}`}
    >
      {children}
    </button>
  )
}

// FAIL: BAD: No types, unclear structure
export function Button(props) {
  return <button onClick={props.onClick}>{props.children}</button>
}
```

### カスタム Hooks (Custom Hooks)

```typescript
// PASS: GOOD: Reusable custom hook
export function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value)

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value)
    }, delay)

    return () => clearTimeout(handler)
  }, [value, delay])

  return debouncedValue
}

// Usage
const debouncedQuery = useDebounce(searchQuery, 500)
```

### 状態管理 (State Management)

```typescript
// PASS: GOOD: Proper state updates
const [count, setCount] = useState(0)

// Functional update for state based on previous state
setCount(prev => prev + 1)

// FAIL: BAD: Direct state reference
setCount(count + 1)  // Can be stale in async scenarios
```

### 条件付きレンダリング (Conditional Rendering)

```typescript
// PASS: GOOD: Clear conditional rendering
{isLoading && <Spinner />}
{error && <ErrorMessage error={error} />}
{data && <DataDisplay data={data} />}

// FAIL: BAD: Ternary hell
{isLoading ? <Spinner /> : error ? <ErrorMessage error={error} /> : data ? <DataDisplay data={data} /> : null}
```

## API 設計規約 (API Design Standards)

### REST API 規約 (REST API Conventions)

```
GET    /api/markets              # List all markets
GET    /api/markets/:id          # Get specific market
POST   /api/markets              # Create new market
PUT    /api/markets/:id          # Update market (full)
PATCH  /api/markets/:id          # Update market (partial)
DELETE /api/markets/:id          # Delete market

# Query parameters for filtering
GET /api/markets?status=active&limit=10&offset=0
```

### レスポンス形式 (Response Format)

```typescript
// PASS: GOOD: Consistent response structure
interface ApiResponse<T> {
  success: boolean
  data?: T
  error?: string
  meta?: {
    total: number
    page: number
    limit: number
  }
}

// Success response
return NextResponse.json({
  success: true,
  data: markets,
  meta: { total: 100, page: 1, limit: 10 }
})

// Error response
return NextResponse.json({
  success: false,
  error: 'Invalid request'
}, { status: 400 })
```

### 入力バリデーション (Input Validation)

```typescript
import { z } from 'zod'

// PASS: GOOD: Schema validation
const CreateMarketSchema = z.object({
  name: z.string().min(1).max(200),
  description: z.string().min(1).max(2000),
  endDate: z.string().datetime(),
  categories: z.array(z.string()).min(1)
})

export async function POST(request: Request) {
  const body = await request.json()

  try {
    const validated = CreateMarketSchema.parse(body)
    // Proceed with validated data
  } catch (error) {
    if (error instanceof z.ZodError) {
      return NextResponse.json({
        success: false,
        error: 'Validation failed',
        details: error.errors
      }, { status: 400 })
    }
  }
}
```

## ファイル構成 (File Organization)

### プロジェクト構造 (Project Structure)

```
src/
├── app/                    # Next.js App Router
│   ├── api/               # API routes
│   ├── markets/           # Market pages
│   └── (auth)/           # Auth pages (route groups)
├── components/            # React components
│   ├── ui/               # Generic UI components
│   ├── forms/            # Form components
│   └── layouts/          # Layout components
├── hooks/                # Custom React hooks
├── lib/                  # Utilities and configs
│   ├── api/             # API clients
│   ├── utils/           # Helper functions
│   └── constants/       # Constants
├── types/                # TypeScript types
└── styles/              # Global styles
```

### ファイル命名 (File Naming)

```
components/Button.tsx          # PascalCase for components
hooks/useAuth.ts              # camelCase with 'use' prefix
lib/formatDate.ts             # camelCase for utilities
types/market.types.ts         # camelCase with .types suffix
```

## コメントとドキュメント (Comments & Documentation)

### コメントを書くタイミング (When to Comment)

```typescript
// PASS: GOOD: Explain WHY, not WHAT
// Use exponential backoff to avoid overwhelming the API during outages
const delay = Math.min(1000 * Math.pow(2, retryCount), 30000)

// Deliberately using mutation here for performance with large arrays
items.push(newItem)

// FAIL: BAD: Stating the obvious
// Increment counter by 1
count++

// Set name to user's name
name = user.name
```

### 公開 API 向け JSDoc (JSDoc for Public APIs)

```typescript
/**
 * Searches markets using semantic similarity.
 *
 * @param query - Natural language search query
 * @param limit - Maximum number of results (default: 10)
 * @returns Array of markets sorted by similarity score
 * @throws {Error} If OpenAI API fails or Redis unavailable
 *
 * @example
 * ```typescript
 * const results = await searchMarkets('election', 5)
 * console.log(results[0].name) // "Trump vs Biden"
 * ```
 */
export async function searchMarkets(
  query: string,
  limit: number = 10
): Promise<Market[]> {
  // Implementation
}
```

## パフォーマンスベストプラクティス (Performance Best Practices)

### メモ化 (Memoization)

```typescript
import { useMemo, useCallback } from 'react'

// PASS: GOOD: Memoize expensive computations
const sortedMarkets = useMemo(() => {
  return markets.sort((a, b) => b.volume - a.volume)
}, [markets])

// PASS: GOOD: Memoize callbacks
const handleSearch = useCallback((query: string) => {
  setSearchQuery(query)
}, [])
```

### 遅延読み込み (Lazy Loading)

```typescript
import { lazy, Suspense } from 'react'

// PASS: GOOD: Lazy load heavy components
const HeavyChart = lazy(() => import('./HeavyChart'))

export function Dashboard() {
  return (
    <Suspense fallback={<Spinner />}>
      <HeavyChart />
    </Suspense>
  )
}
```

### データベースクエリ (Database Queries)

```typescript
// PASS: GOOD: Select only needed columns
const { data } = await supabase
  .from('markets')
  .select('id, name, status')
  .limit(10)

// FAIL: BAD: Select everything
const { data } = await supabase
  .from('markets')
  .select('*')
```

## テスト規約 (Testing Standards)

### テスト構造（AAA パターン）(Test Structure (AAA Pattern))

```typescript
test('calculates similarity correctly', () => {
  // Arrange
  const vector1 = [1, 0, 0]
  const vector2 = [0, 1, 0]

  // Act
  const similarity = calculateCosineSimilarity(vector1, vector2)

  // Assert
  expect(similarity).toBe(0)
})
```

### テスト命名 (Test Naming)

```typescript
// PASS: GOOD: Descriptive test names
test('returns empty array when no markets match query', () => { })
test('throws error when OpenAI API key is missing', () => { })
test('falls back to substring search when Redis unavailable', () => { })

// FAIL: BAD: Vague test names
test('works', () => { })
test('test search', () => { })
```

## コードスメル検出 (Code Smell Detection)

次のアンチパターンに注意:

### 1. 長い関数 (1. Long Functions)
```typescript
// FAIL: BAD: Function > 50 lines
function processMarketData() {
  // 100 lines of code
}

// PASS: GOOD: Split into smaller functions
function processMarketData() {
  const validated = validateData()
  const transformed = transformData(validated)
  return saveData(transformed)
}
```

### 2. 深いネスト (2. Deep Nesting)
```typescript
// FAIL: BAD: 5+ levels of nesting
if (user) {
  if (user.isAdmin) {
    if (market) {
      if (market.isActive) {
        if (hasPermission) {
          // Do something
        }
      }
    }
  }
}

// PASS: GOOD: Early returns
if (!user) return
if (!user.isAdmin) return
if (!market) return
if (!market.isActive) return
if (!hasPermission) return

// Do something
```

### 3. マジックナンバー (3. Magic Numbers)
```typescript
// FAIL: BAD: Unexplained numbers
if (retryCount > 3) { }
setTimeout(callback, 500)

// PASS: GOOD: Named constants
const MAX_RETRIES = 3
const DEBOUNCE_DELAY_MS = 500

if (retryCount > MAX_RETRIES) { }
setTimeout(callback, DEBOUNCE_DELAY_MS)
```

**覚えておくこと**: コード品質は交渉の余地がない。明確で保守可能なコードが迅速な開発と自信を持ったリファクタリングを可能にする。
