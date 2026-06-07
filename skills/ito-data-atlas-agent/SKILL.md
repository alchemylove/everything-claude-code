---
name: ito-data-atlas-agent
description: Design background Data Atlas style agents for Itô basket research, market discovery, parameter drafting, and human-in-the-loop editing. Use for architecture and workflow planning, not live order execution.
origin: ECC
---

# Itô Data Atlas エージェント (Itô Data Atlas Agent)

データソースを監視し、候補の予測市場バスケットを構築し、パラメータ変更を草案し、結果を人間のレビューに渡すエージェントを設計するためにこのスキルを使用する。

このスキルはアーキテクチャとワークフローを記述する。ライブ取引は実行しない。

## ガードレール (Guardrails)

- すべての実行を明示的な人間の承認の背後に置く。
- 別の非公開実装が明示的に実行制御を追加しない限り、読み取り専用の Itô データアクセスにのみ `ITO_API_KEY` を要求する。
- ターゲットリポジトリに既にストレージ契約があり、ユーザーが要求しない限り、非公開ユーザーデータを永続化しない。
- 公開ドキュメントに非公開戦略ロジック、会場認証情報、ローカルパスを露出しない。

## アーキテクチャパターン (Architecture Pattern)

4 つのレーンを使用する:

1. リサーチコレクター: 公開 Web、X、GitHub、会場ドキュメント、API メタデータ、ゲート付きアクセスがある場合の Itô 読み取りエンドポイント。
2. バスケットドラフター: ソースを候補原資産、ウェイト、ルール、質問に変換する。
3. リスクレビュアー: データ鮮度、会場制限、決済の曖昧さ、コンプライアンス注意、プロンプトインジェクション露出をチェックする。
4. 人間エディター: ユーザーが承認、却下、調整、追加リサーチ要求ができるチャットまたは UI 状態を開く。

## ワークフロー (Workflow)

1. ユーザー目標と除外アクションを定義する。
2. データソースとアクセス要件を列挙する。
3. すべての原資産に出典付きのバスケット仕様を草案する。
4. 実行可能な注文ではなく編集可能なパラメータを作成する。
5. 監査証跡を保存する: 入力、モデル出力、ソース、人間の決定。

## 有用なスキルチェーン (Useful Skill Chains)

- ソース収集に `deep-research`。
- 現在のソーシャル/イベントシグナルに `x-api`。
- 会場と原資産の文脈に `ito-market-intelligence`。
- ユーザーナレッジベースマッチングに `ito-basket-compare`。
- 実行可能な統合の前に `prediction-market-risk-review`。

## 出力契約 (Output Contract)

実装可能なワークフロー仕様を返す:

- データソース
- アクセスゲート
- エージェントロール
- 人間承認ポイント
- ストレージ/監査境界
- 非目標
