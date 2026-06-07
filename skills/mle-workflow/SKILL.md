---
name: mle-workflow
description: Production machine-learning engineering workflow for data contracts, reproducible training, model evaluation, deployment, monitoring, and rollback. Use when building, reviewing, or hardening ML systems beyond one-off notebooks.
allowed-tools: Read, Write, Edit, Bash, Grep, Glob
---

# 機械学習エンジニアリングワークフロー (Machine Learning Engineering Workflow)

明確なデータ契約、反復可能な学習、測定可能な品質ゲート、デプロイ可能なアーティファクト、運用モニタリングを備えた本番 ML システムにモデル作業を変換するためにこのスキルを使用する。

## 有効化タイミング (When to Activate)

- 本番 ML 機能、モデル更新、ランキングシステム、レコメンダー、分類器、埋め込みワークフロー、予測パイプラインの計画またはレビュー
- ノートブックコードを再利用可能な学習、評価、バッチ推論、オンライン推論パイプラインに変換
- モデル昇格基準、オフライン/オンライン eval、実験追跡、ロールバックパスの設計
- データドリフト、ラベルリーク、古い特徴量、アーティファクト不一致、学習と serving ロジックの不整合による障害のデバッグ
- モデルモニタリング、カナリアロールアウト、シャドウトラフィック、デプロイ後品質チェックの追加

## スコープ調整 (Scope Calibration)

目の前のシステムに合うレーンだけを使用する。このスキルはランキング、検索、レコメンド、分類器、予測、埋め込み、LLM ワークフロー、異常検知、バッチ分析に有用だが、すべてに一つのアーキテクチャを押し付けるべきではない。

- すべてのモデルに教師ありラベル、オンライン serving、feature store、PyTorch、GPU、人間レビュー、A/B テスト、リアルタイムフィードバックがあると仮定しない。
- データ契約、ベースライン、eval スクリプト、ロールバックメモで変更がレビュー可能になるなら、重い MLOps 機構を追加しない。
- プロジェクトにラベル、遅延アウトカム、スライス定義、本番トラフィック、モニタリング責任者が欠けている場合は前提を明示する。
- 例は交換可能な足場として扱う。メトリクス、serving モード、データストア、ロールアウト機構をプロジェクト固有の同等物に置き換える。

## 関連スキル (Related Skills)

- Python 実装と pytest カバレッジには `python-patterns` と `python-testing`
- 深層学習モデル、データローダー、デバイス処理、学習ループには `pytorch-patterns`
- 昇格ゲートとエージェント支援回帰チェックには `eval-harness` と `ai-regression-testing`
- データストレージと分析サーフェスには `database-migrations`、`postgres-patterns`、`clickhouse-io`
- serving、シークレット、コンテナ、本番強化には `deployment-patterns`、`docker-patterns`、`security-review`

## SWE サーフェスの再利用 (Reuse the SWE Surface)

MLE をソフトウェアエンジニアリングから分離して扱わない。ほとんどの ECC SWE ワークフローは ML システムに直接適用でき、しばしばより厳しい障害モードを持つ:

推奨の `minimal --with capability:machine-learning` インストールは、このスキルと並行してコアエージェントサーフェスを利用可能に保つ。スキルのみまたはエージェント制限のあるハーネスでは、ターゲットがエージェントをサポートする場合 `skill:mle-workflow` と `agent:mle-reviewer` を組み合わせる。

| SWE surface | MLE use |
|-------------|---------|
| `product-capability` / `architecture-decision-records` | モデル作業を明示的なプロダクト契約に変換し、不可逆なデータ・モデル・ロールアウトの選択を記録 |
| `repo-scan` / `codebase-onboarding` / `code-tour` | 並行 ML スタックを導入する前に既存の学習、特徴量、serving、eval、モニタリングパスを発見 |
| `plan` / `feature-dev` | モデル変更をデータ、eval、serving、ロールバックフェーズを持つプロダクト機能としてスコープ |
| `tdd-workflow` / `python-testing` | 実装前に特徴量変換、分割ロジック、メトリクス計算、アーティファクト読み込み、推論スキーマをテスト |
| `code-reviewer` / `mle-reviewer` | コード品質に加え ML 固有のリーク、再現性、昇格、モニタリングリスクをレビュー |
| `build-fix` / `pr-test-analyzer` | 壊れた CI、不安定な eval、欠落フィクスチャ、環境固有のモデル・依存関係障害を診断 |
| `quality-gate` / `test-coverage` | 変換、メトリクス、推論契約、昇格ゲート、ロールバック動作の自動証拠を要求 |
| `eval-harness` / `verification-loop` | オフラインメトリクス、スライスチェック、レイテンシ予算、ロールバックドリルを反復可能なゲートに変換 |
| `ai-regression-testing` | 本番バグをすべて回帰として保持: 欠落特徴量、古いラベル、不良アーティファクト、スキーマドリフト、serving 不一致 |
| `api-design` / `backend-patterns` | 予測 API、バッチジョブ、べき等な再学習エンドポイント、レスポンスエンベロープを設計 |
| `database-migrations` / `postgres-patterns` / `clickhouse-io` | ラベル、特徴量スナップショット、予測ログ、実験メトリクス、ドリフト分析をバージョン管理 |
| `deployment-patterns` / `docker-patterns` | ヘルスチェック、リソース制限、ロールバック付きの再現可能な学習・serving イメージをパッケージ |
| `canary-watch` / `dashboard-builder` | モデルバージョン、スライス、ドリフト、レイテンシ、コスト、遅延ラベルダッシュボードでロールアウト健全性を可視化 |
| `security-review` / `security-scan` | モデルアーティファクト、ノートブック、プロンプト、データセット、ログのシークレット、PII、安全でないデシリアライズ、サプライチェーンリスクをチェック |
| `e2e-testing` / `browser-qa` / `accessibility` | 予測を消費する重要プロダクトフローをテスト（説明可能性とフォールバック UI 状態を含む） |
| `benchmark` / `performance-optimizer` | スループット、p95 レイテンシ、メモリ、GPU 利用率、予測または再学習あたりのコストを測定 |
| `cost-aware-llm-pipeline` / `token-budget-advisor` | 最大モデルにデフォルトせず、品質、レイテンシ、予算で LLM/埋め込みワークロードをルーティング |
| `documentation-lookup` / `search-first` | コーディング前にモデル serving、feature store、ベクトル DB、eval ツールの現行ライブラリ動作を検証 |
| `git-workflow` / `github-ops` / `opensource-pipeline` | 明確なスコープ、生成アーティファクト除外、再現可能なテスト証拠で MLE 変更をレビュー用にパッケージ |
| `strategic-compact` / `dmux-workflows` | 長い ML 作業を並列トラックに分割: データ契約、eval ハーネス、serving パス、モニタリング、ドキュメント |

## 10 の MLE タスクシミュレーション (Ten MLE Task Simulations)

MLE 作業の計画またはレビュー時にカバレッジチェックとしてこれらのシミュレーションを使用する。強い MLE ワークフローは各タスクを明示的な契約、再利用可能な SWE サーフェス、自動証拠、レビュー可能なアーティファクトに還元するはずである。

| ID | Common MLE task | Streamlined ECC path | Required output | Pipeline lanes covered |
|----|-----------------|----------------------|-----------------|------------------------|
| MLE-01 | 曖昧な予測、ランキング、レコメンダー、分類器、埋め込み、予測機能を枠組み化 | `product-capability`, `plan`, `architecture-decision-records`, `mle-workflow` | 関心者、意思決定責任者、成功メトリクス、許容できないミス、前提、制約、最初の実験を命名する Iteration Compact | product contract, stakeholder loss, risk, rollout |
| MLE-02 | メトリクス目標、ラベル、データソース、ミス予算を定義 | `repo-scan`, `database-reviewer`, `database-migrations`, `postgres-patterns`, `clickhouse-io` | エンティティ粒度、ラベルタイミング、ラベル信頼度、特徴量タイミング、point-in-time join、分割ポリシー、データセットスナップショットを含むデータ・メトリクス契約 | data contract, metric design, leakage, reproducibility |
| MLE-03 | 複雑化前にベースラインモデルとスコアリングパスを構築 | `tdd-workflow`, `python-testing`, `python-patterns`, `code-reviewer` | 混同行列、キャリブレーション注記、レイテンシ/コスト見積もり、既知の弱点、スコア形状と決定性のテストを含むベースラインスコアラー | baseline, scoring, testing, serving parity |
| MLE-04 | アウトカムを分離する仮説から特徴量を生成 | `python-patterns`, `pytorch-patterns`, `docker-patterns`, `deployment-patterns` | シグナル源、欠損値、外れ値、相関、リークチェック、学習/serving 等価性をカバーする特徴量計画と変換モジュール | feature pipeline, leakage, training, artifacts |
| MLE-05 | トレードオフ下で閾値、設定、モデル複雑度を調整 | `eval-harness`, `ai-regression-testing`, `quality-gate`, `test-coverage` | precision、recall、F1、AUC、キャリブレーション、グループスライス、レイテンシ、コスト、複雑度、許容エラークラスを比較する閾値/設定レポート | evaluation, threshold, promotion, regression |
| MLE-06 | エラー分析を実行し、ミスを次の実験に変換 | `eval-harness`, `ai-regression-testing`, `mle-reviewer`, `silent-failure-hunter` | false positive、false negative、曖昧ラベル、古い特徴量、欠落シグナル、バグトレースのエラークラスタレポートと教訓の記録 | error analysis, bug trace, iteration, regression |
| MLE-07 | バッチまたはオンライン推論用にモデルアーティファクトをパッケージ | `api-design`, `backend-patterns`, `security-review`, `security-scan` | 前処理、設定、依存制約、スキーマバリデーション、安全な読み込み、PII 安全ログを含むバージョン管理アーティファクトバンドル | artifact, security, inference contract |
| MLE-08 | フィードバック取得付きオンライン serving またはバッチスコアリングを出荷 | `api-design`, `backend-patterns`, `e2e-testing`, `browser-qa`, `accessibility` | レスポンスエンベロープ、タイムアウト、バッチング、フォールバック、モデルバージョン、信頼度、フィードバックログ、プロダクトフローテストを含む予測エンドポイントまたはバッチジョブ | serving, batch inference, fallback, user workflow |
| MLE-09 | シャドウトラフィック、カナリア、A/B テスト、ロールバックでモデルをロールアウト | `canary-watch`, `dashboard-builder`, `verification-loop`, `performance-optimizer` | トラフィック分割、ダッシュボード、p95 レイテンシ、コスト、品質ガードレール、ロールバックアーティファクト、ロールバックトリガーを命名するロールアウト計画 | deployment, canary, rollback |
| MLE-10 | ローンチ後の本番モデルを運用、デバッグ、更新 | `silent-failure-hunter`, `dashboard-builder`, `mle-reviewer`, `doc-updater`, `github-ops` | ドリフトチェック、遅延ラベル健全性、アラート責任者、ランブック更新、再学習基準、PR 証拠を含む観察台帳と更新計画 | monitoring, incident response, retraining |

## イテレーションコンパクト (Iteration Compact)

モデルコードに触れる前に、作業を一つのレビュー可能なアーティファクトに圧縮する。PR 説明に収まるほど短く、他のエンジニアがトレードオフに異議を唱えられるほど精密であるべき。

```text
Goal:
Who cares:
Decision owner:
User or system action changed by the model:
Success metric:
Guardrail metrics:
Mistake budget:
Unacceptable mistakes:
Acceptable mistakes:
Assumptions:
Constraints:
Labels and data snapshot:
Baseline:
Candidate signals:
Threshold or config plan:
Eval slices:
Known risks:
Next experiment:
Rollback or fallback:
```

この compact は強い SWE 設計メモの MLE 版である。信頼できないメトリクスの最適化、実際のエラーモードに対処しない特徴量の追加、ロールバックなしの複雑化の出荷を防ぐ。

## 意思決定ブレイン (Decision Brain)

タスクが曖昧、影響大、メトリクス重視のときはこのループを使用する:

1. モデルではなく意思決定から始める。下流の振る舞いを変えるアクションを命名する。
2. 誰が関心を持ちなぜかを命名する。ステークホルダーごとに false positive、false negative、レイテンシ、計算コスト、不透明性、機会損失のコストは異なる。
3. 曖昧さを仮説に変換する。アウトカムを分離するシグナル、それを反証する証拠、打ち負かすべき単純なベースラインを問う。
4. 独自システムを発明する前に、先行事例または近い既知の問題を調査する。
5. `(probability, confidence) x (cost, severity, importance, impact)` で選択肢をスコアする。
6. 敵対的行動、インセンティブ、選択的開示、分布シフト、フィードバックループを考慮する。
7. 最も重要なミスを最も減らす最も単純な変更を優先する。単純さは怠慢ではない。反復速度を保ちながらミスを最小化する方法である。
8. 意思決定、証拠、反論、次の可逆ステップを記録する。

## メトリクスとミス経済学 (Metric and Mistake Economics)

習慣ではなく障害コストからメトリクスを選ぶ:

- チームが抽象的な accuracy ではなく具体的な false positive と false negative を議論できるよう、早期に混同行列を使用する。
- 誤った陽性判断のコストが支配的なら precision を優先する。
- 見逃しのコストが支配的なら recall を優先する。
- precision/recall トレードオフが本当にバランスされ説明可能な場合のみ F1 を使用する。
- 単一閾値より順位品質が重要なら AUC またはランキングメトリクスを使用する。
- 実行可能なモデル複雑度を形作るため、レイテンシ、スループット、メモリ、コストを第一級メトリクスとして追跡する。
- オフライン改善を祝う前にベースラインと現行本番モデルと比較する。
- 実世界のフィードバックシグナルはバイアス、ラグ、カバレッジギャップを持つ遅延ラベルとして扱う。分析なしに ground truth としない。

すべてのメトリクス選択は、どのミスを安くし、どのミスを起こしやすくし、誰がそのコストを負担するかを述べるべきである。

## データと特徴量仮説 (Data and Feature Hypotheses)

特徴量は分離の理論から来るべき:

- テキスト、カテゴリフィールド、数値履歴、グラフ関係、新しさ、頻度、集約は候補シグナルファミリーであり、自動的な特徴量ではない。
- すべての特徴量ファミリーについて、なぜアウトカムを分離すべきか、どう未来情報をリークしうるかを述べる。
- ノイズの多いラベルには、裁定、ラベル信頼度、ソフトターゲット、信頼度重み付けを検討する。
- クラス不均衡には、重み付き損失、リサンプリング、閾値移動、キャリブレーション済み意思決定ルールを比較する。
- 欠損値について、欠如が情報的か、補完可能か、棄権の理由かを決める。
- 外れ値について、クリップ、バケット化、調査、稀だが重要なシグナルとして保持するかを決める。
- 相関特徴量について、冗長、不安定、利用不可の未来状態の代理かをチェックする。

エラー分析がベースラインが追加シグナルや容量で plausibly 修正できる理由で失敗していることを示すまで、モデル複雑度を追加しない。

## エラー分析ループ (Error Analysis Loop)

各ベースライン、学習実行、閾値変更、設定変更の後:

1. ミスを false positive、false negative、棄権、低信頼度ケース、システム障害に分割する。
2. 共通特性でエラーをクラスタ化: 言語、エンティティタイプ、ソース、時間、地域、デバイス、スパース性、新しさ、特徴量鮮度、ラベルソース、モデルバージョン。
3. モデルミスをデータバグ、ラベル曖昧性、プロダクト曖昧性、計測ギャップ、serving 不一致から分離する。
4. 各主要クラスタを四つの動きの一つに追跡: より良いラベル、より良い特徴量、より良い閾値/設定、より良いプロダクトフォールバック。
5. 重要なミスをすべて回帰テスト、eval スライス、ダッシュボードパネル、ランブックエントリとして保持する。
6. 次の反復を曖昧な「モデル改善」ではなく反証可能な実験として書く。

最強の MLE ループは train -> metric -> ship ではない。mistake -> cluster -> hypothesis -> experiment -> evidence -> simpler system である。

## 観察台帳 (Observation Ledger)

コード、PR、実験レポート、ランブックの横にコンパクトな意思決定と証拠の軌跡を保持する:

```text
Iteration:
Change:
Why this mattered:
Metric movement:
Slice movement:
False positives:
False negatives:
Unexpected errors:
Decision:
Tradeoff accepted:
Lesson captured:
Regression added:
Debt created:
Next iteration:
```

台帳でモデル作業を累積的にする。各反復が次の意思決定を容易にし、単に別のアーティファクトを生むだけでないことが目標である。

## コアワークフロー (Core Workflow)

### 1. 予測契約の定義 (1. Define the Prediction Contract)

モデルコードを書く前にプロダクトレベルの契約を捕捉する:

- 予測ターゲットと意思決定責任者
- 入力エンティティ、出力スキーマ、信頼度/キャリブレーションフィールド、許容レイテンシ
- バッチ、オンライン、ストリーミング、またはハイブリッド serving モード
- モデル、feature store、依存関係が利用不可のときのフォールバック動作
- 高影響判断のための人間レビューまたはオーバーライドパス
- 入力、予測、ラベルのプライバシー、保持、監査要件

「モデルを改善する」を要件として受け入れない。モデルを観測可能なプロダクト振る舞いと測定可能な受け入れゲートに結びつける。

### 2. データ契約の固定 (2. Lock the Data Contract)

すべての ML タスクには明示的なデータ契約が必要:

- エンティティ粒度と主キー
- ラベル定義、ラベルタイムスタンプ、ラベル利用可能遅延
- 特徴量タイムスタンプ、鮮度 SLA、point-in-time join ルール
- 学習、検証、テスト、バックテスト分割ポリシー
- 必須列、許容 null、範囲、カテゴリ、単位
- 学習アーティファクトやログに入れてはならない PII または機微フィールド
- 再現性のためのデータセットバージョンまたはスナップショット ID

まずリークを防ぐ。特徴量が予測時に利用不可、または未来情報で join されている場合は除去するか、分析専用パスに移す。

### 3. 再現可能パイプラインの構築 (3. Build a Reproducible Pipeline)

学習コードは隠れたノートブック状態なしに他のエンジニアが実行可能であるべき:

- すべてのハイパーパラメータとパスに型付き設定ファイルまたは dataclass を使用
- パッケージとモデル依存関係を固定
- 乱数シードを設定し、非決定的 GPU 動作を文書化
- データセットバージョン、コード SHA、設定ハッシュ、メトリクス、アーティファクト URI を記録
- 前処理ロジックをノートブックではなくモデルアーティファクトと一緒に保存
- 学習、eval、推論変換を共有するか一つのソースから生成
- すべてのステップをべき等にし、リトライがアーティファクトやメトリクスを破壊しないようにする

不変値と純粋な変換関数を優先する。特徴量生成中の共有データフレームやグローバル設定のミューテーションを避ける。

```python
import hashlib
from dataclasses import dataclass
from pathlib import Path


@dataclass(frozen=True)
class TrainingConfig:
    dataset_uri: str
    model_dir: Path
    seed: int
    learning_rate: float
    batch_size: int


def artifact_name(config: TrainingConfig, code_sha: str) -> str:
    config_key = f"{config.dataset_uri}:{config.seed}:{config.learning_rate}:{config.batch_size}"
    config_hash = hashlib.sha256(config_key.encode("utf-8")).hexdigest()[:12]
    return f"{code_sha[:12]}-{config_hash}"
```

### 4. 昇格前の評価 (4. Evaluate Before Promotion)

昇格基準は学習完了前に宣言する:

- ベースラインモデルと現行本番モデルの比較
- プロダクト振る舞いに整合した主要メトリクス
- レイテンシ、キャリブレーション、公平性スライス、コスト、エラー集中のガードレールメトリクス
- 重要コホート、地域、デバイス、言語、データソースのスライスメトリクス
- メトリクスがノイズの多い場合の信頼区間または反復実行分散
- 高影響モデルの人間による失敗例レビュー
- 明示的な「出荷しない」閾値

```python
PROMOTION_GATES = {
    "auc": ("min", 0.82),
    "calibration_error": ("max", 0.04),
    "p95_latency_ms": ("max", 80),
}


def assert_promotion_ready(metrics: dict[str, float]) -> None:
    missing = sorted(name for name in PROMOTION_GATES if name not in metrics)
    if missing:
        raise ValueError(f"Model promotion metrics missing required gates: {missing}")

    failures = {
        name: value
        for name, (direction, threshold) in PROMOTION_GATES.items()
        for value in [metrics[name]]
        if (direction == "min" and value < threshold)
        or (direction == "max" and value > threshold)
    }
    if failures:
        raise ValueError(f"Model failed promotion gates: {failures}")
```

オフラインメトリクスはゲートであり保証ではない。モデルがプロダクト振る舞いを変えるときは、フルロールアウト前にシャドウ評価、カナリアロールアウト、または A/B テストを計画する。

### 5. Serving 用パッケージング (5. Package for Serving)

ML アーティファクトは serving 契約がテスト可能なときのみ本番準備完了:

- モデルアーティファクトにバージョン、学習データ参照、設定、前処理を含める
- 入力スキーマが無効、古い、範囲外の特徴量を拒否する
- 出力スキーマに有用な場合はモデルバージョンと信頼度または説明フィールドを含める
- serving パスにタイムアウト、バッチング、リソース制限、フォールバック動作がある
- CPU/GPU 要件が明示的でテスト済み
- 予測ログは PII を避け、デバッグとラベル join に十分な識別子を含む
- 統合テストが欠落特徴量、古い特徴量、不正型、空バッチ、フォールバックパスをカバーする

学習専用特徴量コードが serving 特徴量コードと乖離しない。等価性を証明するテストなしには許可しない。

### 6. モデルの運用 (6. Operate the Model)

モデルモニタリングにはシステムと品質の両シグナルが必要:

- 可用性、エラー率、タイムアウト率、キュー深度、p50/p95/p99 レイテンシ
- 特徴量 null 率、範囲ドリフト、カテゴリドリフト、鮮度ドリフト
- 予測分布ドリフトと信頼度分布ドリフト
- ラベル到着健全性と遅延品質メトリクス
- ビジネス KPI ガードレールとロールバックトリガー
- カナリアとロールバック用のバージョン別ダッシュボード

すべてのデプロイには、前のアーティファクト、設定、データ依存関係、トラフィック切替機構を命名するロールバック計画があるべき。

## レビューチェックリスト (Review Checklist)

- [ ] 予測契約が明示的でテスト可能
- [ ] データ契約がエンティティ粒度、ラベルタイミング、特徴量タイミング、スナップショット/バージョンを定義
- [ ] リークリスクが予測時利用可能性に対してチェック済み
- [ ] 学習がコード、設定、データバージョン、シードから再現可能
- [ ] メトリクスがベースラインと現行本番モデルと比較されている
- [ ] 高リスクコホート向けにスライスメトリクスとガードレールが含まれる
- [ ] 昇格ゲートが自動化され fail closed
- [ ] 学習と serving 変換が共有または等価性テスト済み
- [ ] モデルアーティファクトがバージョン、設定、データセット参照、前処理を保持
- [ ] serving パスが入力を検証し、タイムアウト、フォールバック、ロールバック動作を持つ
- [ ] モニタリングがシステム健全性、特徴量ドリフト、予測ドリフト、遅延ラベルをカバー
- [ ] 機微データがアーティファクト、ログ、プロンプト、例から除外されている

## アンチパターン (Anti-Patterns)

- モデル再現にノートブック状態が必要
- ランダム分割が未来データを検証またはテストセットにリーク
- 特徴量 join がイベント時刻とラベル利用可能性を無視
- オフラインメトリクスは改善するが重要スライスは悪化
- 閾値がテストセットで繰り返し調整される
- 学習前処理が serving コードに手動コピーされる
- 予測ログにモデルバージョンが欠落
- モニタリングがサービス稼働のみをチェックし、データや予測品質をチェックしない
- ロールバックに既知の良好アーティファクトへの切替ではなく再学習が必要

## 出力期待 (Output Expectations)

このスキルを使用するときは、具体的なアーティファクトを返す: データ契約、昇格ゲート、パイプラインステップ、テスト計画、デプロイ計画、またはレビュー所見。本番準備をブロックする未知を前提で埋めずに明示する。
