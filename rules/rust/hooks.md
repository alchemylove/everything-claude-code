---
paths:
  - "**/*.rs"
  - "**/Cargo.toml"
---
# Rust フック (Rust Hooks)

> このファイルは [common/hooks.md](../common/hooks.md) を拡張し、Rust 固有の内容を追加する。

## PostToolUse Hooks

`~/.claude/settings.json` で設定:

- **cargo fmt**: 編集後に `.rs` ファイルを自動フォーマット
- **cargo clippy**: Rust ファイル編集後に lint チェックを実行
- **cargo check**: 変更後にコンパイルを検証（`cargo build` より高速）
