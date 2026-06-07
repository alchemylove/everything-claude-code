---
name: api-connector-builder
description: Build a new API connector or provider by matching the target repo's existing integration pattern exactly. Use when adding one more integration without inventing a second architecture.
origin: ECC direct-port adaptation
version: "1.0.0"
---

# API コネクター ビルダー (API Connector Builder)

リポジトリネイティブな統合サーフェスを追加する場合に使用します。汎用 HTTP クライアントではありません。

ポイントはホスト リポジトリのパターンと一致することです：

- コネクター レイアウト
- 構成スキーマ
- 認証モデル
- エラー処理
- テスト スタイル
- 登録/発見ワイヤリング

## 使用するとき (When to Use)

- 「このプロジェクトの Jira コネクターを構築する」
- 「既存のパターンに従う Slack プロバイダーを追加する」
- 「この API の新しい統合を作成する」
- 「リポジトリのコネクター スタイルに一致するプラグインを構築する」

## ガード レール (Guardrails)

- リポジトリに既に統合アーキテクチャがある場合は、新しい統合アーキテクチャを発明しないでください。
- ベンダー ドキュメントだけから始めないでください。最初に既存の repo 内コネクターから始めてください。
- リポジトリがレジストリ ワイヤリング、テスト、およびドキュメントを期待する場合は、トランスポート コードで停止しないでください。
- リポジトリに新しい現在のパターンがある場合は、古いコネクターをカーゴカルト化しないでください。

## ワークフロー (Workflow)

### 1. ハウス スタイルを学ぶ (1. Learn the house style)

少なくとも 2 つの既存のコネクター/プロバイダーを検査して、マップしてください：

- ファイル レイアウト
- 抽象化の境界
- 構成モデル
- 再試行 / ページネーション コンベンション
- レジストリ フック
- テスト フィクスチャと命名

### 2. ターゲット統合を絞り込む (2. Narrow the target integration)

リポジトリが実際に必要とするサーフェスのみを定義します：

- 認証フロー
- キー エンティティ
- コア読み取り/書き込み操作
- ページネーションとレート制限
- Webhook またはポーリング モデル

### 3. リポジトリネイティブ レイヤーで構築 (3. Build in repo-native layers)

一般的なスライス：

- 構成/スキーマ
- クライアント/トランスポート
- マッピング レイヤー
- コネクター/プロバイダー エントリ ポイント
- 登録
- テスト

### 4. ソース パターンに対して検証 (4. Validate against the source pattern)

新しいコネクターは、別のエコシステムから インポートされたのではなく、コードベースで明白に見えるはずです。

## リファレンス シェイプ (Reference Shapes)

### プロバイダー スタイル (Provider-style)

```text
providers/
  existing_provider/
    __init__.py
    provider.py
    config.py
```

### コネクター スタイル (Connector-style)

```text
integrations/
  existing/
    client.py
    models.py
    connector.py
```

### TypeScript プラグイン スタイル (TypeScript plugin-style)

```text
src/integrations/
  existing/
    index.ts
    client.ts
    types.ts
    test.ts
```

## 品質チェックリスト (Quality Checklist)

- [ ] 既存の repo 内統合パターンに一致します
- [ ] 構成検証が存在します
- [ ] 認証とエラー処理が明示的です
- [ ] ページネーション/再試行動作がリポジトリ規範に従います
- [ ] レジストリ/発見ワイヤリングが完成しました
- [ ] テストはホスト リポジトリのスタイルを反映しています
- [ ] ドキュメント/例がリポジトリで期待されている場合は更新されます

## 関連スキル (Related Skills)

- `backend-patterns`
- `mcp-server-patterns`
- `github-ops`
