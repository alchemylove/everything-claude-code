---
name: bun-runtime
description: Bun as runtime, package manager, bundler, and test runner. When to choose Bun vs Node, migration notes, and Vercel support.
---

# Bun ランタイム (Bun Runtime)

Bun は高速なオールインワン JavaScript ランタイムおよびツールキットです。ランタイム、パッケージマネージャー、バンドラー、テストランナーを兼ね備えています。

## 使用タイミング (When to Use)

- **Bun を優先:** 新規 JS/TS プロジェクト、インストール/実行速度が重要なスクリプト、Bun ランタイムでの Vercel デプロイ、単一ツールチェーン（run + install + test + build）を望む場合。
- **Node を優先:** 最大のエコシステム互換性、Node を前提としたレガシーツール、既知の Bun 互換問題がある依存関係がある場合。

次の場合に使用: Bun の採用、Node からの移行、Bun スクリプト/テストの作成・デバッグ、Vercel や他プラットフォームでの Bun 設定。

## 仕組み (How It Works)

- **Runtime**: Node 互換のドロップインランタイム（JavaScriptCore 上に構築、Zig で実装）。
- **Package manager**: `bun install` は npm/yarn より大幅に高速。現在の Bun ではデフォルトのロックファイルは `bun.lock`（テキスト）。旧バージョンは `bun.lockb`（バイナリ）を使用。
- **Bundler**: アプリとライブラリ向けの組み込みバンドラーとトランスパイラ。
- **Test runner**: Jest 風 API の組み込み `bun test`。

**Node からの移行**: `node script.js` を `bun run script.js` または `bun script.js` に置き換える。`npm install` の代わりに `bun install` を実行。ほとんどのパッケージは動作する。npm スクリプトには `bun run`、npx 風のワンショット実行には `bun x` を使用。Node 組み込みモジュールはサポート。パフォーマンス向上のため、存在する場合は Bun API を優先。

**Vercel**: プロジェクト設定でランタイムを Bun に設定。ビルド: `bun run build` または `bun build ./src/index.ts --outdir=dist`。インストール: 再現可能なデプロイには `bun install --frozen-lockfile`。

## 例 (Examples)

### 実行とインストール (Run and install)

```bash
# Install dependencies (creates/updates bun.lock or bun.lockb)
bun install

# Run a script or file
bun run dev
bun run src/index.ts
bun src/index.ts
```

### スクリプトと環境変数 (Scripts and env)

```bash
bun run --env-file=.env dev
FOO=bar bun run script.ts
```

### テスト (Testing)

```bash
bun test
bun test --watch
```

```typescript
// test/example.test.ts
import { expect, test } from "bun:test";

test("add", () => {
  expect(1 + 2).toBe(3);
});
```

### ランタイム API (Runtime API)

```typescript
const file = Bun.file("package.json");
const json = await file.json();

Bun.serve({
  port: 3000,
  fetch(req) {
    return new Response("Hello");
  },
});
```

## ベストプラクティス (Best Practices)

- 再現可能なインストールのためロックファイル（`bun.lock` または `bun.lockb`）をコミットする。
- スクリプトには `bun run` を優先。TypeScript は Bun が `.ts` をネイティブ実行。
- 依存関係を最新に保つ。Bun とエコシステムは急速に進化している。
