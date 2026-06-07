---
name: latency-critical-systems
description: Use for latency-sensitive systems such as realtime dashboards, market data, streaming agents, execution gateways, queues, caches, or HFT-like infrastructure where freshness and p95 latency matter.
origin: ECC
tools: Read, Write, Edit, Bash, Grep, Glob
---

# レイテンシクリティカルシステム (Latency Critical Systems)

ユーザーがリアルタイム動作、ホットパス、ストリーミング鮮度、実行速度を重視するときにこのスキルを使用する。HFT 的インフラを含むが、このスキルはエンジニアリング重視である。ライブ取引や金融アドバイスを許可しない。

## メトリクスの分離 (Split The Metrics)

すべてを「速い」にまとめない。次を追跡する:

- p50、p95、p99 レイテンシ
- スループット
- 鮮度年齢
- キュー深度
- キャッシュヒット率
- プロバイダー/API 応答時間
- ブラウザレンダリング時間
- 負荷下の正確性
- 障害とリトライ動作

## ホットパスのマッピング (Map The Hot Path)

ユーザー/イベントから最終可視状態までのパスを書く:

```text
source event -> provider API -> ingest worker -> queue -> cache -> edge route
-> client stream -> browser render -> user-visible state
```

次に各セグメントを個別に計測する。

## 最適化の順序 (Optimization Order)

1. 不要なラウンドトリップを除去する。
2. 鮮度メタデータ付きで安定読み取りをキャッシュする。
3. 小さな呼び出しと書き込みをバッチ化する。
4. 計算をデータまたはユーザーに近づける。
5. ホットパスとコールドパスを分離する。
6. キューが無制限に成長する前にバックプレッシャーを適用する。
7. 鮮度または UX が改善される場合のみストリーミングを使用する。
8. 古いデータ、劣化プロバイダー、不良キャッシュ状態のカナリアを追加する。

## 検証 (Verification)

デプロイ済みサーフェスが存在する場合はライブ読み戻しを使用する:

- HTTP タイミングとレスポンスヘッダー
- プロバイダー鮮度タイムスタンプ
- キューまたはジョブ状態
- エッジ/キャッシュ状態
- 実際の UI 鮮度のブラウザ検証
- リトライと劣化モード周辺のログ

マーケットデータまたは実行隣接パスでは、パスを ready と呼ぶ前にオーダーブック年齢、VWAP 前提、プロバイダーステータス、キルスイッチ動作も検証する。

## ガードレール (Guardrails)

- 必須バリデーションを落としてレイテンシを最適化しない。
- 高速キャッシュヒットの背後に古いデータを隠さない。
- 計測なしにクライアントラベルからミリ秒動作を主張しない。
- 明示的な承認ゲートなしにライブ注文、破壊的マイグレーション、顧客影響デプロイを実行しない。
- ログとベンチマークアーティファクトから秘密と非公開ペイロードを除外する。
