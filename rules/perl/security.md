---
paths:
  - "**/*.pl"
  - "**/*.pm"
  - "**/*.t"
  - "**/*.psgi"
  - "**/*.cgi"
---
# Perl セキュリティ (Perl Security)

> このファイルは [common/security.md](../common/security.md) を拡張し、Perl 固有の内容を追加する。

## 汚染モード (Taint Mode)

- すべての CGI/Web 向けスクリプトで `-T` フラグを使用する
- 外部コマンド実行前に `%ENV`（`$ENV{PATH}`、`$ENV{CDPATH}` など）をサニタイズする

## 入力検証 (Input Validation)

- アンテイントには許可リスト正規表現を使用する — `/(.*)/s` は絶対に使用しない
- すべてのユーザー入力を明示的なパターンで検証する:

```perl
if ($input =~ /\A([a-zA-Z0-9_-]+)\z/) {
    my $clean = $1;
}
```

## ファイル I/O (File I/O)

- **3引数 open のみ** — 2引数 open は使用しない
- `Cwd::realpath` でパストラバーサルを防止する:

```perl
use Cwd 'realpath';
my $safe_path = realpath($user_path);
die "Path traversal" unless $safe_path =~ m{\A/allowed/directory/};
```

## プロセス実行 (Process Execution)

- **リスト形式の `system()`** を使用する — 単一文字列形式は使用しない
- 出力キャプチャには **IPC::Run3** を使用する
- 変数補間付きのバッククォートは使用しない

```perl
system('grep', '-r', $pattern, $directory);  # safe
```

## SQL インジェクション防止 (SQL Injection Prevention)

常に DBI プレースホルダを使用する — SQL に補間しない:

```perl
my $sth = $dbh->prepare('SELECT * FROM users WHERE email = ?');
$sth->execute($email);
```

## セキュリティスキャン (Security Scanning)

**perlcritic** をセキュリティテーマで重大度 4 以上で実行する:

```bash
perlcritic --severity 4 --theme security lib/
```

## リファレンス (Reference)

スキル: `perl-security` で包括的な Perl セキュリティパターン、汚染モード、安全な I/O を参照。
