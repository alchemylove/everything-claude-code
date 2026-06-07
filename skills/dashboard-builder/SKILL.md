---
name: dashboard-builder
description: Grafana、SigNoz 等でオペレーターの実問に答える監視ダッシュボードを構築。metrics を vanity board ではなく実用的な dashboard にするときに使用。Grafana, SigNoz, dashboard.
origin: ECC direct-port adaptation
version: "1.0.0"
---

# ダッシュボード ビルダー (Dashboard Builder)

タスクが人々が操作できるダッシュボードを構築することの場合に使用。

目標は「すべてのメトリクスを表示」ではありません。目標は以下の質問に答えることです：

- 健康ですか？
- ボトルネックはどこですか？
- 何が変わったのですか？
- 誰かが取るべき措置は何ですか？

## 使用時期 (When to Use)

- 「Kafkaモニタリングダッシュボードを構築」
- 「Elasticsearch用のGrafanaダッシュボードを作成」
- 「このサービス用のSigNozダッシュボードを作成」
- 「このメトリクスリストを実際の運用ダッシュボードに変える」

## ガードレール (Guardrails)

- ビジュアルレイアウトから始めない；オペレータの質問から始める
- 利用可能なすべてのメトリクスを含めない
- 健康、スループット、リソースパネルを構造なしで混ぜない
- タイトル、ユニット、合理的なしきい値なしでパネルを出荷しない

## ワークフロー (Workflow)

### 1. 運用質問を定義 (1. Define the operating questions)

以下の周りに整理：

- 健康/可用性
- レイテンシ/パフォーマンス
- スループット/ボリューム
- 飽和/リソース
- サービス固有のリスク

### 2. ターゲットプラットフォームスキーマを研究 (2. Study the target platform schema)

既存のダッシュボードを最初に検査：

- JSON構造
- クエリ言語

### 3. メトリクスを選択 (3. Build the minimum useful board)

オペレータが実際に見ているもの、アラートしているもと、対応するのに必要なメトリクスのみを含める。

### 4. レイアウトの構築 (4. Cut vanity panels)

質問ごとにパネルをグループ化。
