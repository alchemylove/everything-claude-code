# コーディングスタイル (Coding Style)

## 不変性（重要）(Immutability (CRITICAL))

常に新しいオブジェクトを作成し、既存オブジェクトを変更しない:

```
// Pseudocode
WRONG:  modify(original, field, value) → changes original in-place
CORRECT: update(original, field, value) → returns new copy with change
```

理由: 不変データは隠れた副作用を防ぎ、デバッグを容易にし、安全な並行処理を可能にします。

## 基本原則 (Core Principles)

### KISS (Keep It Simple)

- 実際に動作する最も単純な解決策を優先する
- 早すぎる最適化を避ける
- 巧妙さより明確さを優先する

### DRY (Don't Repeat Yourself)

- 繰り返しロジックを共有関数やユーティリティに抽出する
- コピペによる実装のドリフトを避ける
- 繰り返しが実在するときに抽象化を導入する（投機的ではない）

### YAGNI (You Aren't Gonna Need It)

- 必要になる前に feature や抽象化を作らない
- 投機的な汎用性を避ける
- シンプルに始め、圧力が実在するときにリファクタリングする

## ファイル構成 (File Organization)

多数の小ファイル > 少数の大ファイル:
- 高凝集、低結合
- 典型的には 200–400 行、最大 800 行
- 大きなモジュールからユーティリティを抽出する
- 型ではなく feature／ドメインで整理する

## エラーハンドリング (Error Handling)

常に包括的にエラーを処理する:
- すべてのレベルで明示的にエラーを処理する
- UI 向けコードではユーザーに分かりやすいエラーメッセージを提供する
- サーバー側では詳細なエラーコンテキストをログに記録する
- エラーを黙って握りつぶさない

## 入力検証 (Input Validation)

システム境界で常に検証する:
- 処理前にすべてのユーザー入力を検証する
- 可能なら schema ベースの検証を使用する
- 明確なエラーメッセージで fail fast する
- 外部データ（API レスポンス、ユーザー入力、ファイル内容）を信頼しない

## 命名規約 (Naming Conventions)

- 変数と関数: 説明的な名前の `camelCase`
- Boolean: `is`、`has`、`should`、`can` プレフィックスを優先
- Interface、型、コンポーネント: `PascalCase`
- 定数: `UPPER_SNAKE_CASE`
- カスタム hooks: `use` プレフィックス付き `camelCase`

## 避けるべきコードの臭い (Code Smells to Avoid)

### 深いネスト (Deep Nesting)

ロジックが積み重なる場合は、ネストした条件分岐より early return を優先する。

### マジックナンバー (Magic Numbers)

意味のある閾値、遅延、制限には名前付き定数を使用する。

### 長い関数 (Long Functions)

大きな関数は、責務が明確な小さな単位に分割する。

## コード品質チェックリスト (Code Quality Checklist)

作業完了とマークする前に:
- [ ] コードが読みやすく、命名が適切
- [ ] 関数が小さい（<50 行）
- [ ] ファイルが焦点を絞っている（<800 行）
- [ ] 深いネストがない（>4 レベル）
- [ ] 適切なエラーハンドリング
- [ ] ハードコード値がない（定数または config を使用）
- [ ] mutation がない（不変パターンを使用）
