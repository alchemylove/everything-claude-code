---
name: nanoclaw-repl
description: Operate and extend NanoClaw v2, ECC's zero-dependency session-aware REPL built on claude -p.
origin: ECC
---

# NanoClaw REPL (NanoClaw REPL)

`scripts/claw.js` の実行または拡張時にこの skill を使用する。

## 機能 (Capabilities)

- markdown 永続化セッション
- `/model` による model 切り替え
- `/load` による動的 skill 読み込み
- `/branch` によるセッション分岐
- `/search` によるクロスセッション検索
- `/compact` による履歴コンパクション
- `/export` による md/json/txt エクスポート
- `/metrics` によるセッションメトリクス

## 運用ガイダンス (Operating Guidance)

1. セッションはタスクに集中させる。
2. 高リスク変更の前に branch する。
3. 主要マイルストーン後に compact する。
4. 共有・アーカイブ前に export する。

## 拡張ルール (Extension Rules)

- 外部 runtime 依存をゼロに保つ
- markdown-as-database 互換を維持する
- コマンドハンドラは決定的かつローカルに保つ
