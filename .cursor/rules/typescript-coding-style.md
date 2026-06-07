---
description: "TypeScript coding style: common ルールの拡張"
globs: ["**/*.ts", "**/*.tsx", "**/*.js", "**/*.jsx"]
alwaysApply: false
---
# TypeScript/JavaScript Coding Style (TypeScript/JavaScript Coding Style)

> このファイルは common coding style ルールを TypeScript/JavaScript 固有の内容で拡張する。

## 不変性 (Immutability)

不変更新には spread operator を使用:

```typescript
// WRONG: Mutation
function updateUser(user, name) {
  user.name = name  // MUTATION!
  return user
}

// CORRECT: Immutability
function updateUser(user, name) {
  return {
    ...user,
    name
  }
}
```

## エラーハンドリング (Error Handling)

async/await と try-catch を使用:

```typescript
try {
  const result = await riskyOperation()
  return result
} catch (error) {
  console.error('Operation failed:', error)
  throw new Error('Detailed user-friendly message')
}
```

## 入力バリデーション (Input Validation)

schema ベースのバリデーションに Zod を使用:

```typescript
import { z } from 'zod'

const schema = z.object({
  email: z.string().email(),
  age: z.number().int().min(0).max(150)
})

const validated = schema.parse(input)
```

## Console.log

- 本番コードに `console.log` 文を置かない
- 代わりに適切な logging library を使用
- 自動検出は hooks を参照
