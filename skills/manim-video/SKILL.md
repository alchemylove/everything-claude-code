---
name: manim-video
description: Build reusable Manim explainers for technical concepts, graphs, system diagrams, and product walkthroughs, then hand off to the wider ECC video stack if needed. Use when the user wants a clean animated explainer rather than a generic talking-head script.
origin: ECC
---

# Manim 動画 (Manim Video)

モーション、構造、明瞭さが写実性より重要な技術解説には Manim を使用する。

## 有効化タイミング (When to Activate)

- ユーザーが技術解説アニメーションを求めるとき
- 概念がグラフ、ワークフロー、アーキテクチャ、メトリクス推移、システム図であるとき
- X やランディングページ向けの短いプロダクト/ローンチ解説が欲しいとき
- ビジュアルが汎用的にシネマティックではなく精密に感じられるべきとき

## ツール要件 (Tool Requirements)

- シーンレンダリング用 `manim` CLI
- 必要に応じた後処理用 `ffmpeg`
- 最終組み立てまたは仕上げ用 `video-editing`
- 最終パッケージに合成 UI、キャプション、追加モーションレイヤーが必要な場合は `remotion-video-creation`

## デフォルト出力 (Default Output)

- 短い 16:9 MP4
- サムネイルまたはポスターフレーム 1 枚
- ストーリーボードとシーンプラン

## ワークフロー (Workflow)

1. コアビジュアルテーゼを 1 文で定義する。
2. 概念を 3〜6 シーンに分割する。
3. 各シーンが何を証明するか決める。
4. Manim コードを書く前にシーンアウトラインを書く。
5. 最小の動作版を先にレンダリングする。
6. レンダリングが動いた後にタイポグラフィ、間隔、色、ペーシングを詰める。
7. 価値を加える場合のみ広い video スタックへ引き渡す。

## シーンプランningルール (Scene Planning Rules)

- 各シーンは 1 つのことを証明する
- 詰め込みすぎの図を避ける
- フルスクリーンの雑然さより段階的開示を優先する
- 画面を忙しくするためではなく状態変化を説明するモーションを使う
- タイトルカードは短く意味を込める

## ネットワークグラフデフォルト (Network Graph Default)

ソーシャルグラフとネットワーク最適化解説向け:

- 最適化グラフの前に現在のグラフを示す
- 低シグナルなフォロー雑然と高シグナルなブリッジを区別する
- ウォームパスノードとターゲットクラスターをハイライトする
- 有用ならスキルを形成した自己改善系譜を示す最終シーンを追加する

## レンダー規約 (Render Conventions)

- ユーザーが縦向きを求めない限り 16:9 横をデフォルトとする
- 低品質のスモークテストレンダーから始める
- 構成とタイミングが安定してから高品質へ
- ソーシャルサイズで読めるクリーンなサムネイルフレームを 1 枚エクスポートする

## 再利用スターター (Reusable Starter)

ネットワークグラフ解説の出発点として [assets/network_graph_scene.py](assets/network_graph_scene.py) を使用する。

スモークテスト例:

```bash
manim -ql assets/network_graph_scene.py NetworkGraphExplainer
```

## 出力形式 (Output Format)

返すもの:

- コアビジュアルテーゼ
- ストーリーボード
- シーンアウトライン
- レンダープラン
- フォローオン仕上げの推奨事項

## 関連スキル (Related Skills)

- 最終仕上げに `video-editing`
- モーション重視の後処理または合成に `remotion-video-creation`
- アニメーションが広いローンチの一部の場合は `content-engine`
