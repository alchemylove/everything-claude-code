---
paths:
  - "**/*.pl"
  - "**/*.pm"
  - "**/*.t"
  - "**/*.psgi"
  - "**/*.cgi"
---
# Perl コーディングスタイル (Perl Coding Style)

> このファイルは [common/coding-style.md](../common/coding-style.md) を拡張し、Perl 固有の内容を追加する。

## 標準 (Standards)

- 常に `use v5.36`（`strict`、`warnings`、`say`、サブルーチンシグネチャを有効化）
- サブルーチンシグネチャを使用する — `@_` を手動で展開しない
- 明示的な改行付きの `print` よりも `say` を優先

## 不変性 (Immutability)

- **Moo** で `is => 'ro'` と **Types::Standard** をすべての属性に使用
- blessed ハッシュリファレンスを直接使用しない — 常に Moo/Moose アクセサを使用
- **OO オーバーライドノート**: `builder` または `default` を持つ Moo の `has` 属性は、計算された読み取り専用値として許容される

## フォーマット (Formatting)

以下の設定で **perltidy** を使用:

```
-i=4    # 4-space indent
-l=100  # 100 char line length
-ce     # cuddled else
-bar    # opening brace always right
```

## リンティング (Linting)

**perlcritic** をテーマ `core`、`pbp`、`security` で重大度 3 で使用する。

```bash
perlcritic --severity 3 --theme 'core || pbp || security' lib/
```

## リファレンス (Reference)

スキル: `perl-patterns` で包括的なモダン Perl のイディオムとベストプラクティスを参照。
