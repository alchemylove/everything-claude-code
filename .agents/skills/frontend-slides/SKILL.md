---
name: frontend-slides
description: Create stunning, animation-rich HTML presentations from scratch or by converting PowerPoint files. Use when the user wants to build a presentation, convert a PPT/PPTX to web, or create slides for a talk/pitch. Helps non-designers discover their aesthetic through visual exploration rather than abstract choices.
---

# フロントエンドスライド (Frontend Slides)

依存ゼロで、ブラウザ上で完全に動作するアニメーション豊富な HTML プレゼンテーションを作成する。

[zarazhangrui](https://github.com/zarazhangrui) の作品に見られる視覚的探索アプローチに着想を得ている。

## 有効化タイミング (When to Activate)

- トークデック、ピッチデック、ワークショップデック、社内プレゼンの作成
- `.ppt` または `.pptx` スライドを HTML プレゼンに変換
- 既存 HTML プレゼンのレイアウト、モーション、タイポグラフィの改善
- デザイン嗜好がまだ分からないユーザーとプレゼンスタイルを探索

## 必須要件 (Non-Negotiables)

1. **Zero dependencies**: デフォルトはインライン CSS と JS の自己完結型 HTML ファイル一つ。
2. **Viewport fit is mandatory**: すべてのスライドは内部スクロールなしで一つのビューポート内に収まること。
3. **Show, don't tell**: 抽象的なスタイルアンケートではなく視覚プレビューを使う。
4. **Distinctive design**: 汎用的な紫グラデーション、Inter-on-white、テンプレート感のあるデックを避ける。
5. **Production quality**: コードにコメントを付け、アクセシブル、レスポンシブ、パフォーマンスを維持する。

生成前に `STYLE_PRESETS.md` を読み、ビューポート安全な CSS ベース、密度制限、プリセットカタログ、CSS の落とし穴を確認すること。

## ワークフロー (Workflow)

### 1. モード検出 (1. Detect Mode)

次のいずれかのパスを選ぶ:
- **New presentation**: トピック、メモ、または完成原稿がある
- **PPT conversion**: `.ppt` または `.pptx` がある
- **Enhancement**: 既存 HTML スライドがあり改善が必要

### 2. コンテンツ把握 (2. Discover Content)

必要最小限だけ質問する:
- purpose: pitch, teaching, conference talk, internal update
- length: short (5-10), medium (10-20), long (20+)
- content state: finished copy, rough notes, topic only

ユーザーにコンテンツがある場合は、スタイリング前に貼り付けを依頼する。

### 3. スタイル把握 (3. Discover Style)

デフォルトは視覚的探索。

ユーザーが希望プリセットを既に知っている場合はプレビューをスキップし、直接適用する。

それ以外:
1. デックが生むべき感情を尋ねる: impressed, energized, focused, inspired。
2. `.ecc-design/slide-previews/` に **3 つの単一スライドプレビューファイル** を生成する。
3. 各プレビューは自己完結型で、タイポグラフィ/色/モーションを明確に示し、スライドコンテンツはおおよそ 100 行以内。
4. どのプレビューを採用するか、またはどの要素を混ぜるかをユーザーに尋ねる。

ムードとスタイルの対応は `STYLE_PRESETS.md` のプリセットガイドを使用する。

### 4. プレゼン構築 (4. Build the Presentation)

出力先:
- `presentation.html`
- `[presentation-name].html`

デックに抽出またはユーザー提供の画像が含まれる場合のみ `assets/` フォルダを使用する。

必須構成:
- セマンティックなスライドセクション
- `STYLE_PRESETS.md` 由来のビューポート安全 CSS ベース
- テーマ値用の CSS カスタムプロパティ
- キーボード、ホイール、タッチナビゲーション用のプレゼンコントローラークラス
- リビールアニメーション用の Intersection Observer
- reduced-motion サポート

### 5. ビューポート適合の強制 (5. Enforce Viewport Fit)

これはハードゲートとして扱う。

ルール:
- すべての `.slide` は `height: 100vh; height: 100dvh; overflow: hidden;` を使用
- すべてのタイポとスペーシングは `clamp()` でスケール
- コンテンツが収まらない場合は複数スライドに分割
- 読めるサイズ以下にテキストを縮小してオーバーフローを解決しない
- スライド内にスクロールバーを許可しない

密度制限と必須 CSS ブロックは `STYLE_PRESETS.md` を使用する。

### 6. 検証 (6. Validate)

完成デックを次のサイズで確認する:
- 1920x1080
- 1280x720
- 768x1024
- 375x667
- 667x375

ブラウザ自動化が利用可能な場合、スライドのオーバーフローがないこととキーボードナビゲーションが動作することを検証する。

### 7. 引き渡し (7. Deliver)

引き渡し時:
- ユーザーが残したい場合を除き、一時プレビューファイルを削除
- 有用な場合は OS に応じたオープナーでデックを開く
- ファイルパス、使用プリセット、スライド数、簡単なテーマカスタマイズポイントを要約

現在の OS に応じたオープナーを使用:
- macOS: `open file.html`
- Linux: `xdg-open file.html`
- Windows: `start "" file.html`

## PPT / PPTX 変換 (PPT / PPTX Conversion)

PowerPoint 変換時:
1. テキスト、画像、ノートの抽出には `python-pptx` 付き `python3` を優先する。
2. `python-pptx` が利用不可の場合、インストールするか手動/エクスポートベースのワークフローにフォールバックするか確認する。
3. スライド順、スピーカーノート、抽出アセットを保持する。
4. 抽出後は新規プレゼンと同じスタイル選択ワークフローを実行する。

変換はクロスプラットフォームを維持する。Python で可能な場合は macOS 専用ツールに依存しない。

## 実装要件 (Implementation Requirements)

### HTML / CSS (HTML / CSS)

- ユーザーが明示的にマルチファイルプロジェクトを望まない限り、インライン CSS と JS を使用する。
- フォントは Google Fonts または Fontshare から取得可能。
- 大気的な背景、強いタイプ階層、明確なビジュアル方向性を優先する。
- イラストではなく、抽象的な形状、グラデーション、グリッド、ノイズ、幾何学を使用する。

### JavaScript (JavaScript)

含める項目:
- keyboard navigation
- touch / swipe navigation
- mouse wheel navigation
- progress indicator or slide index
- reveal-on-enter animation triggers

### アクセシビリティ (Accessibility)

- セマンティック構造（`main`, `section`, `nav`）を使用
- コントラストを読みやすく保つ
- キーボードのみのナビゲーションをサポート
- `prefers-reduced-motion` を尊重

## コンテンツ密度制限 (Content Density Limits)

ユーザーが明示的に密度の高いスライドを求め、可読性が保たれる場合を除き、以下の上限を使用する:

| Slide type | Limit |
|------------|-------|
| Title | 1 heading + 1 subtitle + optional tagline |
| Content | 1 heading + 4-6 bullets or 2 short paragraphs |
| Feature grid | 6 cards max |
| Code | 8-10 lines max |
| Quote | 1 quote + attribution |
| Image | 1 image constrained by viewport |

## アンチパターン (Anti-Patterns)

- ビジュアルアイデンティティのない汎用スタートアップグラデーション
- 意図的にエディトリアルでない限りのシステムフォントデック
- 長い箇条書きの壁
- スクロールが必要なコードブロック
- 短い画面で壊れる固定高さコンテンツボックス
- `-clamp(...)` のような無効な否定 CSS 関数

## 関連 ECC スキル (Related ECC Skills)

- デック周辺のコンポーネントとインタラクションパターンには `frontend-patterns`
- Apple ガラス美学を意図的に借りるプレゼンには `liquid-glass-design`
- 最終デックの自動ブラウザ検証が必要な場合は `e2e-testing`

## 納品チェックリスト (Deliverable Checklist)

- プレゼンがブラウザのローカルファイルから実行できる
- すべてのスライドがスクロールなしでビューポートに収まる
- スタイルが独自で意図的
- アニメーションが意味があり、ノイズではない
- reduced motion が尊重されている
- 引き渡し時にファイルパスとカスタマイズポイントが説明されている
