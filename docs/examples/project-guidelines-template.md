# プロジェクトガイドライン テンプレート (Project Guidelines Template)

これは、以前 live ECC skill として配布されていた project-specific skill template である。

cross-project skill ではなく reference material であるため、現在は `docs/examples/` に置かれている。

project-specific skill の例である。自身のプロジェクト向けテンプレートとして使用する。

実運用アプリケーション [Zenith](https://zenith.chat)（AI-powered customer discovery platform）に基づく。

## 使用タイミング (When to Use)

対象プロジェクトで作業するときにこの skill を参照する。Project skills には以下が含まれる:
- アーキテクチャ概要 (Architecture overview)
- ファイル構造 (File structure)
- コードパターン (Code patterns)
- テスト要件 (Testing requirements)
- デプロイワークフロー (Deployment workflow)

---

## アーキテクチャ概要 (Architecture Overview)

**Tech Stack:**
- **Frontend**: Next.js 15 (App Router), TypeScript, React
- **Backend**: FastAPI (Python), Pydantic models
- **Database**: Supabase (PostgreSQL)
- **AI**: Claude API with tool calling and structured output
- **Deployment**: Google Cloud Run
- **Testing**: Playwright (E2E), pytest (backend), React Testing Library

**Services:**
```
┌─────────────────────────────────────────────────────────────┐
│                         Frontend                            │
│  Next.js 15 + TypeScript + TailwindCSS                     │
│  Deployed: Vercel / Cloud Run                              │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                         Backend                             │
│  FastAPI + Python 3.11 + Pydantic                          │
│  Deployed: Cloud Run                                       │
└─────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
        ┌──────────┐   ┌──────────┐   ┌──────────┐
        │ Supabase │   │  Claude  │   │  Redis   │
        │ Database │   │   API    │   │  Cache   │
        └──────────┘   └──────────┘   └──────────┘
```

---

## ファイル構造 (File Structure)

```
project/
├── frontend/
│   └── src/
│       ├── app/              # Next.js app router pages
│       │   ├── api/          # API routes
│       │   ├── (auth)/       # Auth-protected routes
│       │   └── workspace/    # Main app workspace
│       ├── components/       # React components
│       │   ├── ui/           # Base UI components
│       │   ├── forms/        # Form components
│       │   └── layouts/      # Layout components
│       ├── hooks/            # Custom React hooks
│       ├── lib/              # Utilities
│       ├── types/            # TypeScript definitions
│       └── config/           # Configuration
│
├── backend/
│   ├── routers/              # FastAPI route handlers
│   ├── models.py             # Pydantic models
│   ├── main.py               # FastAPI app entry
│   ├── auth_system.py        # Authentication
│   ├── database.py           # Database operations
│   ├── services/             # Business logic
│   └── tests/                # pytest tests
│
├── deploy/                   # Deployment configs
├── docs/                     # Documentation
└── scripts/                  # Utility scripts
```

---

## コードパターン (Code Patterns)

### API Response Format (FastAPI) (API Response Format (FastAPI))

```python
from pydantic import BaseModel
from typing import Generic, TypeVar, Optional

T = TypeVar('T')

class ApiResponse(BaseModel, Generic[T]):
    success: bool
    data: Optional[T] = None
    error: Optional[str] = None

    @classmethod
    def ok(cls, data: T) -> "ApiResponse[T]":
        return cls(success=True, data=data)

    @classmethod
    def fail(cls, error: str) -> "ApiResponse[T]":
        return cls(success=False, error=error)
```

### Frontend API Calls (TypeScript) (Frontend API Calls (TypeScript))

```typescript
interface ApiResponse<T> {
  success: boolean
  data?: T
  error?: string
}

async function fetchApi<T>(
  endpoint: string,
  options?: RequestInit
): Promise<ApiResponse<T>> {
  try {
    const response = await fetch(`/api${endpoint}`, {
      ...options,
      headers: {
        'Content-Type': 'application/json',
        ...options?.headers,
      },
    })

    if (!response.ok) {
      return { success: false, error: `HTTP ${response.status}` }
    }

    return await response.json()
  } catch (error) {
    return { success: false, error: String(error) }
  }
}
```

### Claude AI Integration (Structured Output) (Claude AI Integration (Structured Output))

```python
from anthropic import Anthropic
from pydantic import BaseModel

class AnalysisResult(BaseModel):
    summary: str
    key_points: list[str]
    confidence: float

async def analyze_with_claude(content: str) -> AnalysisResult:
    client = Anthropic()

    response = client.messages.create(
        model="claude-sonnet-4-5-20250514",
        max_tokens=1024,
        messages=[{"role": "user", "content": content}],
        tools=[{
            "name": "provide_analysis",
            "description": "Provide structured analysis",
            "input_schema": AnalysisResult.model_json_schema()
        }],
        tool_choice={"type": "tool", "name": "provide_analysis"}
    )

    # Extract tool use result
    tool_use = next(
        block for block in response.content
        if block.type == "tool_use"
    )

    return AnalysisResult(**tool_use.input)
```

### Custom Hooks (React) (Custom Hooks (React))

```typescript
import { useState, useCallback } from 'react'

interface UseApiState<T> {
  data: T | null
  loading: boolean
  error: string | null
}

export function useApi<T>(
  fetchFn: () => Promise<ApiResponse<T>>
) {
  const [state, setState] = useState<UseApiState<T>>({
    data: null,
    loading: false,
    error: null,
  })

  const execute = useCallback(async () => {
    setState(prev => ({ ...prev, loading: true, error: null }))

    const result = await fetchFn()

    if (result.success) {
      setState({ data: result.data!, loading: false, error: null })
    } else {
      setState({ data: null, loading: false, error: result.error! })
    }
  }, [fetchFn])

  return { ...state, execute }
}
```

---

## テスト要件 (Testing Requirements)

### Backend (pytest) (Backend (pytest))

```bash
# Run all tests
poetry run pytest tests/

# Run with coverage
poetry run pytest tests/ --cov=. --cov-report=html

# Run specific test file
poetry run pytest tests/test_auth.py -v
```

**テスト構成 (Test structure):**
```python
import pytest
from httpx import AsyncClient
from main import app

@pytest.fixture
async def client():
    async with AsyncClient(app=app, base_url="http://test") as ac:
        yield ac

@pytest.mark.asyncio
async def test_health_check(client: AsyncClient):
    response = await client.get("/health")
    assert response.status_code == 200
    assert response.json()["status"] == "healthy"
```

### Frontend (React Testing Library) (Frontend (React Testing Library))

```bash
# Run tests
npm run test

# Run with coverage
npm run test -- --coverage

# Run E2E tests
npm run test:e2e
```

**テスト構成 (Test structure):**
```typescript
import { render, screen, fireEvent } from '@testing-library/react'
import { WorkspacePanel } from './WorkspacePanel'

describe('WorkspacePanel', () => {
  it('renders workspace correctly', () => {
    render(<WorkspacePanel />)
    expect(screen.getByRole('main')).toBeInTheDocument()
  })

  it('handles session creation', async () => {
    render(<WorkspacePanel />)
    fireEvent.click(screen.getByText('New Session'))
    expect(await screen.findByText('Session created')).toBeInTheDocument()
  })
})
```

---

## デプロイワークフロー (Deployment Workflow)

### デプロイ前チェックリスト (Pre-Deployment Checklist)

- [ ] すべてのテストがローカルでパスしている
- [ ] `npm run build` が成功する (frontend)
- [ ] `poetry run pytest` がパスする (backend)
- [ ] ハードコードされた secret がない
- [ ] 環境変数が文書化されている
- [ ] データベースマイグレーションの準備ができている

### デプロイコマンド (Deployment Commands)

```bash
# Build and deploy frontend
cd frontend && npm run build
gcloud run deploy frontend --source .

# Build and deploy backend
cd backend
gcloud run deploy backend --source .
```

### 環境変数 (Environment Variables)

```bash
# Frontend (.env.local)
NEXT_PUBLIC_API_URL=https://api.example.com
NEXT_PUBLIC_SUPABASE_URL=https://xxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJ...

# Backend (.env)
DATABASE_URL=postgresql://...
ANTHROPIC_API_KEY=sk-ant-...
SUPABASE_URL=https://xxx.supabase.co
SUPABASE_KEY=eyJ...
```

---

## 重要なルール (Critical Rules)

1. **絵文字禁止** — code、comments、documentation に使用しない
2. **不変性 (Immutability)** — オブジェクトや配列を決して mutate しない
3. **TDD** — 実装前にテストを書く
4. **80% coverage** 以上を最低ラインとする
5. **小さなファイルを多数** — 典型的には 200-400 行、最大 800 行
6. **本番コードに console.log を置かない**
7. **try/catch による適切なエラーハンドリング**
8. **Pydantic/Zod による入力バリデーション**

---

## 関連スキル (Related Skills)

- `coding-standards.md` - 一般的なコーディングのベストプラクティス
- `backend-patterns.md` - API とデータベースのパターン
- `frontend-patterns.md` - React と Next.js のパターン
- `tdd-workflow/` - テスト駆動開発の方法論
