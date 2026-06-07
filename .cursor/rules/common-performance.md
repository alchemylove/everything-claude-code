---
description: "Performance: model 選択、context 管理、build トラブルシューティング"
alwaysApply: true
---
# Performance Optimization (Performance Optimization)

## Model 選択戦略 (Model Selection Strategy)

**Haiku 4.5**（Sonnet の 90% 能力、3 倍のコスト削減）:
- 頻繁に呼び出す軽量 agent
- ペアプログラミングとコード生成
- multi-agent システムの worker agent

**Sonnet 4.6**（最高の coding model）:
- メインの開発作業
- multi-agent workflow の orchestration
- 複雑な coding タスク

**Opus 4.5**（最深の推論）:
- 複雑なアーキテクチャ判断
- 最大限の推論が必要な要件
- リサーチと分析タスク

## Context Window 管理 (Context Window Management)

context window の最後の 20% は避ける:
- 大規模リファクタリング
- 複数ファイルにまたがる feature 実装
- 複雑な相互作用のデバッグ

context 感度が低いタスク:
- 単一ファイル編集
- 独立した utility 作成
- ドキュメント更新
- 単純な bug 修正

## Extended Thinking + Plan Mode

Extended thinking はデフォルトで有効。内部推論に最大 31,999 token を確保。

Extended thinking の制御:
- **Toggle**: Option+T（macOS）/ Alt+T（Windows/Linux）
- **Config**: `~/.claude/settings.json` で `alwaysThinkingEnabled` を設定
- **Budget cap**: `export MAX_THINKING_TOKENS=10000`
- **Verbose mode**: Ctrl+O で thinking 出力を表示

深い推論が必要な複雑なタスクでは:
1. extended thinking が有効であることを確認（デフォルトで on）
2. 構造化アプローチのために **Plan Mode** を有効化
3. 徹底分析のために複数ラウンドの critique を使用
4. 多様な視点のために split role sub-agent を使用

## Build トラブルシューティング (Build Troubleshooting)

build が失敗した場合:
1. **build-error-resolver** agent を使用
2. エラーメッセージを分析
3. 段階的に修正
4. 各修正後に検証
