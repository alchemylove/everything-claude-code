---
name: blender-motion-state-inspection
description: キャラクター・リグ・ポーズ・アニメーション retargeting・接地・向き・モデルとモーションの整合を Blender で検査。スクリーンショットだけでは不十分なとき。Blender, rig, pose, animation retargeting, ground contact.
origin: ECC
tools: Read, Write, Edit, Bash, Grep, Glob
---

# Blender モーション状態検査 (Blender Motion State Inspection)

## 使用タイミング (When to Use)

- Blender キャラクターがねじれ、ミラー、潰れ、オフセット、フットスライドしているように見える。
- インポートしたアバター、アーマチュア、リターゲットモーションが期待ポーズと一致するかユーザーが尋ねる。
- レンダー証拠と、ボーン、バウンディングボックス、接地、向きベクトルなどの構造化事実を比較する必要がある。
- モデルがキャラクター、小道具、プロキシメッシュ、コントロールリグ、壊れたインポートかを判定するワークフローに依存する。

## コア原則 (Core Principle)

アニメーション 3D アセットをスクリーンショットだけで判断しない。スクリーンショットはレビュー証拠だが、軸規約、ボーン名、オブジェクトスケール、ローカル変換、親付きメッシュ、マテリアルスロット、フレームごとの接地状態を隠す。

まず構造化 Blender 状態を抽出し、その後ビューポートスクリーンショットまたはレンダーで事実が示唆することを確認する。

## 仕組み (How It Works)

1. モーションを判断する前にクリーンシーンとアセットベースラインを確立する。
2. exporter または Blender 自身のインタプリタ内の Blender Python で Blender から構造化事実を抽出する。
3. 接地、向き、スケール、リターゲティングエラーを露わにしやすいフレームをサンプルする。
4. 計測事実をユーザーの期待ポーズ、方向、地面、レンダー目標と比較する。
5. 確認された事実、想定原因、必要な修正を分けた簡潔レポートを返す。

## 検査ワークフロー (Inspection Workflow)

1. シーンを棚卸しする。
   - メッシュ、アーマチュア、empty、カメラ、ライト、モディファイア、親関係、非表示オブジェクトを列挙する。
   - アバターを判断する前にキャラクターメッシュとヘルパー/プロキシジオメトリを分離する。
   - オブジェクト空間とワールド空間のバウンディングボックスを記録する。

2. スケルトンを特定する。
   - アーマチュア名、pose bone、ボーンヘッド/テール、roll、親チェーン、constraint、rest-pose 軸を取得する。
   - hips、spine、neck、head、shoulder、elbow、hand、thigh、knee、ankle、foot などのセマンティックボーンをマップする。
   - 左右ペアの欠落と異常な命名規則をフラグする。

3. 前、上、横軸を決定する。
   - pelvis、spine、shoulder、hip、head、foot を組み合わせて使う。単一メッシュ法線だけに頼らない。
   - ローカルアーマチュア軸とワールド軸、glTF Y-up と Blender Z-up などのインポート規約を比較する。
   - 顔/頭/足の向きが root motion と矛盾する場合、ミラーまたは後ろ向きインポートの可能性をマークする。

4. アニメーションフレームをサンプルする。
   - 最初、中間、接地、空中、極端フレームを検査する。
   - root 位置、root 向き、pelvis 高さ、胴体傾き、四肢方向、足クリアランス、メッシュ bounds を記録する。
   - 長いまたは速いモーションでは、フリップ、着地、ターン、衝突、床接触周辺をより密にサンプルする。

5. リターゲティングのせいにする前にモデル整合性を確認する。
   - アニメーション適用前のクリーンベースライン形状を確認する。
   - ユーザーが明示的に修復を求めない限り、元メッシュ、マテリアル、アーマチュア、スキニングを保持する。
   - 説明のつかない球状ブロブ、巨大プロキシメッシュ、潰れたボディは、証明されるまでインポート/選択問題として扱う。

6. 接地とモーション問題を診断する。
   - 地面貫通: 各フレームで最下位の足または靴頂点と床高さを比較する。
   - フットスライド: 接地フレーム間の足ワールド位置を比較する。
   - 脚の交差: 左右大腿、knee、ankle、foot の左右順序を比較する。
   - ねじれ損傷: 四肢軸周りの roll/twist とスイング方向を別々に比較する。
   - スケールドリフト: アニメーションメッシュ bounds をクリーンベースライン bounds と比較する。

7. 意見の前に事実を報告する。
   - フレーム番号、オブジェクト名、ボーン名、ワールド座標、閾値を含める。
   - 確認された失敗と視覚的疑いを分離する。
   - 構造化状態が何を見るべきか説明した後にのみスクリーンショットを添付する。

## 推奨レポート形式 (Recommended Report Shape)

```markdown
## Blender モーション検査 (Blender Motion Inspection)

### Scene Inventory
- Character candidates:
- Armatures:
- Helper/proxy objects:
- Cameras/lights:

### Orientation
- World up:
- Character forward:
- Root heading:
- Mirrored/backwards risk:

### Baseline Integrity
- Clean mesh bounds:
- Animated mesh bounds:
- Materials/skin preserved:
- Suspicious non-character meshes:

### Frame Findings
| Frame | Finding | Evidence |
| --- | --- | --- |
| 1 | Clean baseline pose | hips/spine/feet aligned |
| 96 | Foot penetrates floor | left_foot min_z = -0.04 |

### Verdict
- Pass/fail:
- Required fix:
- Render readiness:
```

## 例 (Examples)

### フットスライドのあるウォークサイクル (Walk Cycle With Foot Sliding)

シナリオ: リターゲットキャラクターがウォークサイクル中に滑るように見えるが、正面カメラ角度では足接地が判断しづらい。

ワークフローを適用:
- シーン棚卸し: キャラクターメッシュ `HeroBody`、アーマチュア `HeroRig`、地面 `Floor`、非表示プロキシメッシュなし。
- スケルトン特定: セマンティック足は `foot.L` と `foot.R`、hips は `pelvis`、root bone は `root`。
- フレームサンプル: 接地モーメント周辺のフレーム 1、18、24、30、42、48 を検査。
- 接地・モーション診断: 接地フレーム間のワールド空間足位置を比較。

抽出事実:

| Frame | Fact | Evidence |
| --- | --- | --- |
| 18 | Left foot is planted | `foot.L min_z = 0.004`, toe and heel both near floor |
| 24 | Left foot slides while planted | `foot.L x = 0.21 -> 0.28` over six frames |
| 30 | Pelvis keeps moving forward | `pelvis y = 1.14 -> 1.31` |

判定: レンダー準備として fail。モーションには foot-lock クリーンアップまたはリターゲティング constraint レビューが必要。ボディメッシュのプロポーション変更は不要。

### 後ろ向きインポートキャラクター (Backwards Imported Character)

シナリオ: 静止フレームでは正しく見えるが、アニメーションが期待移動方向と逆に動く。

ワークフローを適用:
- 前/上/横軸の決定: 頭、胸、足、root motion を比較。
- フレームサンプル: フレーム 1 と移動経路の中間点を検査。
- 意見の前に事実: root 向きとモデル正面方向を別々に含める。

抽出事実:

| Frame | Fact | Evidence |
| --- | --- | --- |
| 1 | Character face points toward world `-Y` | head/chest vector from `neck` to `head` resolves to `-Y` |
| 72 | Root motion travels toward world `+Y` | `root y = 0.0 -> 2.8` |
| 72 | Feet remain visually forward-facing opposite travel | toe bones point `-Y` while displacement is `+Y` |

判定: 後ろ向きインポートまたはリターゲティング forward 軸不一致の可能性。アニメーションカーブ編集前にインポート/リターゲット軸マッピングを修正する。

## 実用的閾値 (Practical Thresholds)

- シーン単位スケールが別指定でない限り Blender のデフォルトメートルスケールを仮定する。
- 床が柔らかいまたは意図的に様式化されていない限り、1–2 cm 超の地面貫通は可視とみなす。
- 5% 超の急なスケール変化は rig、constraint、変換継承の問題の可能性が高い。
- 空中反転中の左右 ankle 順序反転は、後で回復しても脚交差リスクとして扱う。
- ソースモーションにスナップターンがない限り、フレームあたり 30 度超の root 向きジャンプは疑わしい。

## アンチパターン (Anti-Patterns)

- タスクが明示的なメッシュ修復でない限り、ポーズ合わせのためにボディプロポーションを変更しない。
- 記録前にクリーンベースラインを bake で消さない。
- 1 つのレンダーカメラ角度をポーズ正しさの証明にしない。
- キャラクターに含まれない理由を記録するまでヘルパーオブジェクトを削除しない。
- head、foot、胴体、root motion を一緒に確認せず、アバターが +Y、-Y、+X、-X を向いていると仮定しない。

## ツールメモ (Tooling Notes)

Blender 状態 exporter が利用可能なら、メッシュ、アーマチュア、pose bone、マテリアル、接地、バウンディングボックス、サンプルアニメーションフレームを含む JSON を優先する。exporter がなければ、`bpy` は通常の system Python では使えないため、`blender --background scene.blend --python collect_motion_state.py` のように Blender 自身経由で小さな Blender Python script を実行する。
