---
description: "TypeScript security: common ルールの拡張"
globs: ["**/*.ts", "**/*.tsx", "**/*.js", "**/*.jsx"]
alwaysApply: false
---
# TypeScript/JavaScript Security (TypeScript/JavaScript Security)

> このファイルは common security ルールを TypeScript/JavaScript 固有の内容で拡張する。

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

- 包括的なセキュリティ監査には **security-reviewer** skill を使用
