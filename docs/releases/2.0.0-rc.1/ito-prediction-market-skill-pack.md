# Itô 予測市場 Skill パック (Itô Prediction-Market Skill Pack)

この rc.1 note は、2つのビジネスを分離したまま ECC の skill 配布ループと Itô 予測市場 workflow を接続する公開 teaser skill pack を記録する。

ECC は open agent-harness substrate のまま、ECC Tools は hosted GitHub App / Pro surface のままである。Itô は別の予測市場バスケットビジネスである。リンクは配布である: ECC は予測市場バスケットの調査、比較、説明、計画を agent がより得意にする再利用可能 skill を出荷できる。live Itô API アクセスはゲートされたままである。

## 含まれる Skill (Included Skills)

| Skill | 用途 (Use) |
| --- | --- |
| `ito-market-intelligence` | ソース根拠のある event、underlier、venue、流動性、ニュース context |
| `ito-basket-compare` | バスケットを knowledge base、portfolio note、financial context、thesis と比較 |
| `ito-trade-planner` | market と venue レビュー向けの手動・非助言 worksheet を構築 |
| `ito-data-atlas-agent` | 人間の編集ポイント付き background research/drafting agent を設計 |
| `prediction-market-oracle-research` | 予測市場を agent と decision intelligence 向け data/oracle 入力として扱う |
| `prediction-market-risk-review` | 助言、venue、security、privacy、execution 境界をレビュー |

## アクセスモデル (Access Model)

公開 skill は調査と計画に Itô 認証情報なしで動作する。Itô 連携呼び出しには明示的なゲート済みアクセスが必要:

```bash
export ITO_API_KEY=...
```

live key、account データ、position、private strategy、venue 認証情報を公開 doc、prompt、commit、slide deck、support ticket に含めないこと。

推奨公開 CTA:

> Itô skill pack は今日、公開の research/planning workflow として動作する。live basket データが必要なら DM または Itô API key のアクセスをリクエストしてください。

## 非助言境界 (Non-Advisory Boundary)

これらの skill は投資、法律、税務、取引助言を提供しない。取引は実行しない。ユーザーが次を行うのを助けられる:

- market と underlier を検査する;
- バスケットを自分の note や制約と比較する;
- resolution と venue mechanics を理解する;
- 予測市場シグナルをより広い調査プロセスの1入力として使う;
- ユーザー自身がレビューできる手動 worksheet を下書きする。

## グロースループ (Growth Loop)

一般的な product-integration 契約は [`docs/architecture/platform-value-loop.md`](../../architecture/platform-value-loop.md) を参照。

ループは意図的に単純である:

1. ECC ユーザーが有用な公開予測市場 skill を発見する。
2. builder が公開ソースで skill を実行し、Itô 型 workflow を見る。
3. 本気のユーザーが live Itô basket データ向けにゲート済み API アクセスをリクエストする。
4. Itô 利用がより多くの operator pattern を生む。
5. サニタイズ済み pattern が新しい ECC skill になりうる。

これは ECC Tools を Itô 製品に見せず、ビジネス間の subscription 所有権を混ぜずに agent/tooling トラフィックを Itô に向ける。

## 有用なチェーン (Useful Chain)

フル workflow では次をチェーンする:

`deep-research` -> `x-api` または `exa-search` -> `ito-market-intelligence` ->
`ito-basket-compare` -> `prediction-market-risk-review` ->
`ito-trade-planner`

企業または業界ユースケースでは、trade planning を `prediction-market-oracle-research` に置き換え、出力を dashboard、decision memo、agent memory record にルーティングする。
