---
name: documentation-lookup
description: Use up-to-date library and framework docs via Context7 MCP instead of training data. Activates for setup questions, API references, code examples, or when the user names a framework (e.g. React, Next.js, Prisma).
---

# ドキュメント検索 (Documentation Lookup)

ユーザーがライブラリ、フレームワーク、API について質問したときは、学習データに頼らず Context7 MCP（ツール `resolve-library-id` と `query-docs`）で最新ドキュメントを取得する。

## コアコンセプト (Core Concepts)

- **Context7**: ライブドキュメントを公開する MCP サーバー。ライブラリと API には学習データの代わりにこれを使う。
- **resolve-library-id**: ライブラリ名とクエリから Context7 互換のライブラリ ID（例: `/vercel/next.js`）を返す。
- **query-docs**: 指定したライブラリ ID と質問に対するドキュメントとコードスニペットを取得。有効なライブラリ ID を得るため、常に先に resolve-library-id を呼ぶ。

## 使用タイミング (When to use)

次の場合に有効化:

- セットアップや設定の質問（例: "How do I configure Next.js middleware?"）
- ライブラリに依存するコードの依頼（"Write a Prisma query for..."）
- API やリファレンス情報の依頼（"What are the Supabase auth methods?"）
- 特定のフレームワークやライブラリの言及（React, Vue, Svelte, Express, Tailwind, Prisma, Supabase など）

リクエストがライブラリ、フレームワーク、API の正確で最新の挙動に依存する場合は常にこのスキルを使う。Context7 MCP が設定されたハーネス（Claude Code、Cursor、Codex など）全体で適用。

## 仕組み (How it works)

### ステップ 1: ライブラリ ID を解決 (Step 1: Resolve the Library ID)

**resolve-library-id** MCP ツールを次の引数で呼ぶ:

- **libraryName**: ユーザーの質問から取ったライブラリまたは製品名（例: `Next.js`, `Prisma`, `Supabase`）。
- **query**: ユーザーの完全な質問。結果の関連性ランキングを向上させる。

ドキュメントを照会する前に、Context7 互換のライブラリ ID（形式 `/org/project` または `/org/project/version`）を取得する必要がある。このステップで有効なライブラリ ID を得る前に query-docs を呼ばない。

### ステップ 2: 最適な一致を選択 (Step 2: Select the Best Match)

解決結果から、次の基準で1つを選ぶ:

- **Name match**: ユーザーが求めたものに最も一致するものを優先。
- **Benchmark score**: スコアが高いほどドキュメント品質が良い（100 が最高）。
- **Source reputation**: 利用可能なら High または Medium を優先。
- **Version**: ユーザーがバージョンを指定した場合（例: "React 19", "Next.js 15"）、リストにあればバージョン固有のライブラリ ID を優先（例: `/org/project/v1.2.0`）。

### ステップ 3: ドキュメントを取得 (Step 3: Fetch the Documentation)

**query-docs** MCP ツールを次の引数で呼ぶ:

- **libraryId**: ステップ 2 で選んだ Context7 ライブラリ ID（例: `/vercel/next.js`）。
- **query**: ユーザーの具体的な質問やタスク。関連スニペットを得るため具体的に。

制限: 質問あたり query-docs（または resolve-library-id）を3回以上呼ばない。3回後も不明確なら不確実性を述べ、推測せずに得られた最良の情報を使う。

### ステップ 4: ドキュメントを使用 (Step 4: Use the Documentation)

- 取得した最新情報でユーザーの質問に答える。
- 役立つ場合はドキュメントの関連コード例を含める。
- 重要な場合はライブラリやバージョンを引用（例: "In Next.js 15..."）。

## 例 (Examples)

### 例: Next.js middleware (Example: Next.js middleware)

1. **resolve-library-id** を `libraryName: "Next.js"`, `query: "How do I set up Next.js middleware?"` で呼ぶ。
2. 結果から名前と benchmark score で最適な一致（例: `/vercel/next.js`）を選ぶ。
3. **query-docs** を `libraryId: "/vercel/next.js"`, `query: "How do I set up Next.js middleware?"` で呼ぶ。
4. 返されたスニペットとテキストで回答。関連すればドキュメントの最小 `middleware.ts` 例を含める。

### 例: Prisma query (Example: Prisma query)

1. **resolve-library-id** を `libraryName: "Prisma"`, `query: "How do I query with relations?"` で呼ぶ。
2. 公式 Prisma ライブラリ ID（例: `/prisma/prisma`）を選択。
3. その `libraryId` とクエリで **query-docs** を呼ぶ。
4. ドキュメントの短いコードスニペットとともに Prisma Client パターン（例: `include` または `select`）を返す。

### 例: Supabase auth methods (Example: Supabase auth methods)

1. **resolve-library-id** を `libraryName: "Supabase"`, `query: "What are the auth methods?"` で呼ぶ。
2. Supabase ドキュメントのライブラリ ID を選ぶ。
3. **query-docs** を呼び、認証メソッドを要約し取得ドキュメントの最小例を示す。

## ベストプラクティス (Best Practices)

- **具体的に**: 可能ならユーザーの完全な質問をクエリに使い関連性を高める。
- **バージョン意識**: ユーザーがバージョンに言及したら、解決ステップでバージョン固有のライブラリ ID を使う。
- **公式ソースを優先**: 複数一致がある場合、コミュニティフォークより公式またはプライマリパッケージを優先。
- **機密データなし**: Context7 に送るクエリから API キー、パスワード、トークンなどの秘密を編集。resolve-library-id や query-docs に渡す前に、ユーザーの質問に秘密が含まれる可能性があると扱う。
