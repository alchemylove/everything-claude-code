---
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.js"
  - "**/*.jsx"
---
# TypeScript/JavaScript セキュリティ (TypeScript/JavaScript Security)

> 本ファイルは [common/security.md](../common/security.md) を TypeScript/JavaScript 固有の内容で拡張します。

## Secret 管理 (Secret Management)

```typescript
// NEVER: Hardcoded secrets
const apiKey = "sk-proj-xxxxx"

// ALWAYS: Environment variables
const apiKey = process.env.OPENAI_API_KEY

if (!apiKey) {
  throw new Error('OPENAI_API_KEY not configured')
}
```

## Agent サポート (Agent Support)

- 包括的なセキュリティ監査には **security-reviewer** skill を使用する
