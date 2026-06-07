---
paths:
  - "**/*.rb"
  - "**/*.rake"
  - "**/Gemfile"
  - "**/Gemfile.lock"
  - "**/config/routes.rb"
---
# Ruby フック (Ruby Hooks)

> このファイルは [common/hooks.md](../common/hooks.md) を拡張し、Ruby および Rails 固有の内容を追加する。

## PostToolUse フック (PostToolUse Hooks)

binstub とチェックイン済みツールを優先するようにプロジェクトローカルのフックを設定する:

- **RuboCop**: Ruby 編集後に `bundle exec rubocop -A <file>` またはプロジェクトのより安全なフォーマッタコマンドを実行する。
- **Brakeman**: セキュリティに関わる Rails の変更後に `bundle exec brakeman --no-progress` を実行する。
- **テスト**: 変更されたファイルに対して最も狭い範囲の `bin/rails test ...` または `bundle exec rspec ...` コマンドを実行する。
- **Bundler audit**: `Gemfile` または `Gemfile.lock` が変更され、プロジェクトに bundler-audit がインストールされている場合、`bundle exec bundle-audit check --update` を実行する。

## 警告 (Warnings)

- アプリケーションコードにコミットされた `debugger`、`binding.irb`、`binding.pry`、`puts`、`pp`、`p` の呼び出しに対して警告する。
- 編集が CSRF 保護を無効にしたり、マスアサインメントを拡大したり、パラメータ化なしで生の SQL を追加した場合に警告する。
- マイグレーションが可逆的なパスや文書化されたロールアウト計画なしにデータを破壊的に変更する場合に警告する。

## CI ゲートの提案 (CI Gate Suggestions)

```bash
bundle exec rubocop
bundle exec brakeman --no-progress
bin/rails test
bundle exec rspec
```

プロジェクトに存在するコマンドのみを使用する。メンテナーの承認なしに新しいフック依存関係をインストールしない。
