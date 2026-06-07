# Continuous Learning v2 仕様 (Continuous Learning v2 Spec)

このドキュメントは v2 continuous-learning アーキテクチャを記述します：

1. Hook ベースの観測キャプチャ
2. バックグラウンド observer 分析ループ
3. Instinct のスコアリングと永続化
4. Instinct から再利用可能な skills/commands への進化

主な実装は次にあります：
- `skills/continuous-learning-v2/`
- `scripts/hooks/`

docs と翻訳向けの安定した参照パスとしてこのファイルを使用してください。
