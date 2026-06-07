# パフォーマンス最適化 (Performance Optimization)

## モデル選択戦略 (Model Selection Strategy)

**Haiku 4.5**（Sonnet 能力の 90%、コスト 3 倍削減）:
- 頻繁に呼び出す軽量 agent
- ペアプログラミングとコード生成
- マルチ agent システムの worker agent

**Sonnet 4.6**（最高のコーディングモデル）:
- メインの開発作業
- マルチ agent ワークフローのオーケストレーション
- 複雑なコーディングタスク

**Opus 4.5**（最深の推論）:
- 複雑なアーキテクチャ判断
- 最大限の推論が必要な要件
- 調査と分析タスク

## コンテキストウィンドウ管理 (Context Window Management)

次の作業ではコンテキストウィンドウの最後の 20% を避ける:
- 大規模リファクタリング
- 複数ファイルにまたがる feature 実装
- 複雑な相互作用のデバッグ

コンテキスト感度が低いタスク:
- 単一ファイルの編集
- 独立したユーティリティの作成
- ドキュメント更新
- 単純な bug 修正

## Extended Thinking + Plan Mode

Extended thinking はデフォルトで有効で、内部推論に最大 31,999 tokens を確保します。

Extended thinking の制御:
- **Toggle**: Option+T（macOS）/ Alt+T（Windows/Linux）
- **Config**: `~/.claude/settings.json` で `alwaysThinkingEnabled` を設定
- **Budget cap**: `export MAX_THINKING_TOKENS=10000`
- **Verbose mode**: Ctrl+O で thinking 出力を表示

深い推論が必要な複雑タスクでは:
1. extended thinking が有効であることを確認する（デフォルトでオン）
2. 構造化アプローチのため **Plan Mode** を有効化する
3. 徹底分析のため複数ラウンドの critique を行う
4. 多様な視点のため役割分割 sub-agent を使用する

## ビルドのトラブルシューティング (Build Troubleshooting)

ビルドが失敗した場合:
1. **build-error-resolver** agent を使用する
2. エラーメッセージを分析する
3. 段階的に修正する
4. 各修正後に検証する
