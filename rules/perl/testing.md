---
paths:
  - "**/*.pl"
  - "**/*.pm"
  - "**/*.t"
  - "**/*.psgi"
  - "**/*.cgi"
---
# Perl テスト (Perl Testing)

> このファイルは [common/testing.md](../common/testing.md) を拡張し、Perl 固有の内容を追加する。

## フレームワーク (Framework)

新規プロジェクトには **Test2::V0** を使用する（Test::More ではない）:

```perl
use Test2::V0;

is($result, 42, 'answer is correct');

done_testing;
```

## ランナー (Runner)

```bash
prove -l t/              # adds lib/ to @INC
prove -lr -j8 t/         # recursive, 8 parallel jobs
```

`lib/` を `@INC` に含めるため、常に `-l` を使用する。

## カバレッジ (Coverage)

**Devel::Cover** を使用する — 80% 以上を目標:

```bash
cover -test
```

## モック (Mocking)

- **Test::MockModule** — 既存モジュールのメソッドをモック
- **Test::MockObject** — ゼロからテストダブルを作成

## 注意点 (Pitfalls)

- テストファイルは常に `done_testing` で終了する
- `prove` で `-l` フラグを忘れない

## リファレンス (Reference)

スキル: `perl-testing` で Test2::V0、prove、Devel::Cover を使った詳細な Perl TDD パターンを参照。
