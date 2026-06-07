---
name: mcp-server-patterns
description: Build MCP servers with Node/TypeScript SDK — tools, resources, prompts, Zod validation, stdio vs Streamable HTTP. Use Context7 or official MCP docs for latest API.
---

# MCP サーバーパターン (MCP Server Patterns)

Model Context Protocol（MCP）は、AI アシスタントがサーバーからツールを呼び出し、リソースを読み取り、プロンプトを使用できるようにする。MCP サーバーの構築・保守時にこのスキルを使用する。SDK API は進化するため、現在のメソッド名とシグネチャは Context7（"MCP" で query-docs）または公式 MCP ドキュメントを確認すること。

## 使用タイミング (When to Use)

次の場合に使用: 新規 MCP サーバーの実装、ツールやリソースの追加、stdio と HTTP の選択、SDK のアップグレード、MCP 登録とトランスポートの問題のデバッグ。

## 仕組み (How It Works)

### コア概念 (Core concepts)

- **Tools**: モデルが呼び出せるアクション（例: 検索、コマンド実行）。SDK バージョンにより `registerTool()` または `tool()` で登録。
- **Resources**: モデルが取得できる読み取り専用データ（例: ファイル内容、API レスポンス）。`registerResource()` または `resource()` で登録。ハンドラは通常 `uri` 引数を受け取る。
- **Prompts**: クライアントが表示できる再利用可能なパラメータ化プロンプトテンプレート（例: Claude Desktop）。`registerPrompt()` または同等の API で登録。
- **Transport**: ローカルクライアント（例: Claude Desktop）には stdio。リモート（Cursor、クラウド）には Streamable HTTP を推奨。レガシー HTTP/SSE は後方互換性のため。

Node/TypeScript SDK は `tool()` / `resource()` または `registerTool()` / `registerResource()` を公開する場合がある。公式 SDK は時期により変更されている。常に現在の [MCP docs](https://modelcontextprotocol.io) または Context7 で確認すること。

### stdio での接続 (Connecting with stdio)

ローカルクライアントでは、stdio トランスポートを作成し、サーバーの connect メソッドに渡す。正確な API は SDK バージョンにより異なる（例: コンストラクタ vs ファクトリ）。現在のパターンは公式 MCP ドキュメントまたは Context7 で "MCP stdio server" を検索すること。

サーバーロジック（tools + resources）はトランスポートから独立させ、エントリポイントで stdio または HTTP を差し替え可能にする。

### リモート（Streamable HTTP）(Remote (Streamable HTTP)) (Remote)

Cursor、クラウド、その他のリモートクライアントには **Streamable HTTP**（現行仕様の単一 MCP HTTP エンドポイント）を使用。後方互換性が必要な場合のみレガシー HTTP/SSE をサポート。

## 例 (Examples)

### インストールとサーバー設定 (Install and server setup)

```bash
npm install @modelcontextprotocol/sdk zod
```

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { z } from "zod";

const server = new McpServer({ name: "my-server", version: "1.0.0" });
```

ツールとリソースは、使用中の SDK バージョンが提供する API で登録する。一部バージョンは `server.tool(name, description, schema, handler)`（位置引数）、他は `server.tool({ name, description, inputSchema }, handler)` または `registerTool()` を使用。リソースも同様 — API が提供する場合はハンドラに `uri` を含める。コピペエラーを避けるため、現在の `@modelcontextprotocol/sdk` シグネチャは公式 MCP ドキュメントまたは Context7 で確認すること。

入力バリデーションには **Zod**（または SDK が推奨するスキーマ形式）を使用する。

## ベストプラクティス (Best Practices)

- **Schema first**: すべてのツールに入力スキーマを定義し、パラメータと戻り値の形状を文書化する。
- **Errors**: モデルが解釈できる構造化エラーまたはメッセージを返す。生のスタックトレースは避ける。
- **Idempotency**: 可能な限りべき等なツールを優先し、リトライを安全にする。
- **Rate and cost**: 外部 API を呼ぶツールではレート制限とコストを考慮し、ツール説明に記載する。
- **Versioning**: package.json で SDK バージョンを固定し、アップグレード時はリリースノートを確認する。

## 公式 SDK とドキュメント (Official SDKs and Docs)

- **JavaScript/TypeScript**: `@modelcontextprotocol/sdk`（npm）。現在の登録とトランスポートパターンは Context7 でライブラリ名 "MCP" を使用。
- **Go**: GitHub の公式 Go SDK（`modelcontextprotocol/go-sdk`）。
- **C#**: .NET 向け公式 C# SDK。
