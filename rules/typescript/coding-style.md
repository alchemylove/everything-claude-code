---
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.js"
  - "**/*.jsx"
---
# TypeScript/JavaScript コーディングスタイル (TypeScript/JavaScript Coding Style)

> 本ファイルは [common/coding-style.md](../common/coding-style.md) を TypeScript/JavaScript 固有の内容で拡張します。

## 型と Interface (Types and Interfaces)

公開 API、共有モデル、コンポーネント props を型で明示的・読みやすく・再利用可能にする。

### 公開 API (Public APIs)

- export 関数、共有ユーティリティ、公開 class メソッドにパラメータ型と戻り値型を付ける
- 自明なローカル変数の型は TypeScript に推論させる
- 繰り返すインライン object 形状は名前付き型または interface に抽出する

```typescript
// WRONG: Exported function without explicit types
export function formatUser(user) {
  return `${user.firstName} ${user.lastName}`
}

// CORRECT: Explicit types on public APIs
interface User {
  firstName: string
  lastName: string
}

export function formatUser(user: User): string {
  return `${user.firstName} ${user.lastName}`
}
```

### Interface vs. Type Alias (Interfaces vs. Type Aliases)

- 拡張・実装されうる object 形状には `interface` を使用する
- union、intersection、tuple、mapped type、utility type には `type` を使用する
- 相互運用に `enum` が必要でない限り、string literal union を `enum` より優先する

```typescript
interface User {
  id: string
  email: string
}

type UserRole = 'admin' | 'member'
type UserWithRole = User & {
  role: UserRole
}
```

### `any` の回避 (Avoid `any`)

- アプリケーションコードでは `any` を避ける
- 外部または信頼できない入力には `unknown` を使い、安全に narrow する
- 値の型が呼び出し側に依存する場合は generics を使用する

```typescript
// WRONG: any removes type safety
function getErrorMessage(error: any) {
  return error.message
}

// CORRECT: unknown forces safe narrowing
function getErrorMessage(error: unknown): string {
  if (error instanceof Error) {
    return error.message
  }

  return 'Unexpected error'
}
```

### React Props

- コンポーネント props は名前付き `interface` または `type` で定義する
- callback props は明示的に型付けする
- 特別な理由がない限り `React.FC` は使わない

```typescript
interface User {
  id: string
  email: string
}

interface UserCardProps {
  user: User
  onSelect: (id: string) => void
}

function UserCard({ user, onSelect }: UserCardProps) {
  return <button onClick={() => onSelect(user.id)}>{user.email}</button>
}
```

### JavaScript ファイル (JavaScript Files)

- `.js` と `.jsx` では、型が明確さを高め、TypeScript 移行が現実的でない場合に JSDoc を使用する
- JSDoc を実行時の挙動と一致させる

```javascript
/**
 * @param {{ firstName: string, lastName: string }} user
 * @returns {string}
 */
export function formatUser(user) {
  return `${user.firstName} ${user.lastName}`
}
```

## 不変性 (Immutability)

不変更新には spread operator を使用する:

```typescript
interface User {
  id: string
  name: string
}

// WRONG: Mutation
function updateUser(user: User, name: string): User {
  user.name = name // MUTATION!
  return user
}

// CORRECT: Immutability
function updateUser(user: Readonly<User>, name: string): User {
  return {
    ...user,
    name
  }
}
```

## エラーハンドリング (Error Handling)

async/await と try-catch を使い、unknown エラーを安全に narrow する:

```typescript
interface User {
  id: string
  email: string
}

declare function riskyOperation(userId: string): Promise<User>

function getErrorMessage(error: unknown): string {
  if (error instanceof Error) {
    return error.message
  }

  return 'Unexpected error'
}

const logger = {
  error: (message: string, error: unknown) => {
    // Replace with your production logger (for example, pino or winston).
  }
}

async function loadUser(userId: string): Promise<User> {
  try {
    const result = await riskyOperation(userId)
    return result
  } catch (error: unknown) {
    logger.error('Operation failed', error)
    throw new Error(getErrorMessage(error))
  }
}
```

## 入力検証 (Input Validation)

schema ベース検証に Zod を使い、schema から型を推論する:

```typescript
import { z } from 'zod'

const userSchema = z.object({
  email: z.string().email(),
  age: z.number().int().min(0).max(150)
})

type UserInput = z.infer<typeof userSchema>

const validated: UserInput = userSchema.parse(input)
```

## Console.log

- 本番コードに `console.log` 文を置かない
- 適切なロギングライブラリを使用する
- 自動検出は hooks を参照
