---
description: フルマーケティングキャンペーンを計画・実行する。プロダクトブリーフを受け取り、ポジショニング、ランディングページコピー、メールシーケンス、ソーシャル投稿、広告バリアント、動画スクリプト、コンテンツカレンダーを返す。既存コピーのコンバージョン品質レビューも可能。
allowed_tools: ["Read", "Grep", "Glob", "WebSearch", "WebFetch", "Write"]
---

# /marketing-campaign

ブリーフからフルコンテンツスイートまで、マーケティングキャンペーンを計画・実行する。

## 使い方 (Usage)

```
/marketing-campaign                          # ブリーフを対話的に入力
/marketing-campaign [product brief]          # インラインブリーフからフルキャンペーン
/marketing-campaign copy [type]              # 単一成果物のみ
/marketing-campaign review [file-or-brief]   # コンバージョンとブランド一貫性のコピー監査
```

## このコマンドの動作 (What It Does)

1. **リサーチ (Research)** — 執筆前にターゲットオーディエンスをプロファイルし、競合をマッピング
2. **ポジショニング (Positioning)** — キャンペーンの角度とトーンプロファイルを先に確定
3. **コピー制作 (Copy production)** — 正しい順序でフルコンテンツスイートを生成（ランディングページ → メール → ソーシャル → 広告 → 動画スクリプト → カレンダー）
4. **レビュー (Review)** — すべての出力をコンバージョンとブランド一貫性チェックリストでゲート

## モード (Modes)

### フルキャンペーンモード (Full Campaign Mode)

以下を含むプロダクトブリーフを提供:

- プロダクト名と説明
- ターゲットオーディエンス（具体的に、汎用的でない）
- プロダクトが解決するコア問題
- コアベネフィット / アウトカム
- トーンのガイダンス
- 必要なチャネル
- ローンチ目標またはタイムライン

エージェントはすべてのキャンペーン成果物を順番に返し、最後にコピーレビューサマリーを付ける。

### 単一成果物モード (Single Deliverable Mode)

```
/marketing-campaign copy landing-page
/marketing-campaign copy email-sequence
/marketing-campaign copy social-posts
/marketing-campaign copy ads
/marketing-campaign copy video-scripts
```

ポジショニングが先に定義されている必要がある。単一成果物を要求する前にフルモードを実行するか、角度を提供する。

### コピーレビューモード (Copy Review Mode)

```
/marketing-campaign review path/to/copy.md
/marketing-campaign review "paste copy here"
```

以下に対する構造化監査を返す:

- 5秒明瞭性テスト（above-fold コピー）
- CTA品質（具体的、 earned、1ピースあたり1つ）
- ブランドトーンの一貫性
- 主張の具体性と裏付け可能性
- プラットフォームネイティブな適合
- チャネル横断の一貫性

## ブリーフテンプレート (Brief Template)

```markdown
Product: [name]
Description: [1-3 sentences on what it does]
Audience: [who, specifically]
Problem: [the specific pain the product solves]
Benefit: [the outcome the user gets]
Tone: [adjectives + what to avoid]
Channels: [landing page, email, LinkedIn, X, ads, video]
Goal: [launch, waitlist, signups, awareness — and timeline]
```

## 出力先 (Output Location)

キャンペーンアセットを保存する場合の規約は `.claude/campaigns/{campaign-name}/`:

```
.claude/campaigns/product-launch/
├── positioning.md
├── landing-page.md
├── email-sequence.md
├── social-posts.md
├── ad-copy.md
├── video-scripts.md
└── content-calendar.md
```

ファイルを書き込む前に保存先を確認する。

## 例 (Examples)

```
/marketing-campaign Build a 7-day launch campaign for an AI career platform for UK university students.
```

```
/marketing-campaign copy landing-page
```

```
/marketing-campaign review .claude/campaigns/the-key/landing-page.md
```

## エージェント委任 (Agent Delegation)

このコマンドが呼び出すエージェント:

- `marketing-agent` — キャンペーン計画とコピー制作
- `brand-voice` — 複数出力でトーンを固定する必要がある場合の voice キャプチャ
- `content-engine` — プラットフォームネイティブなソーシャルコンテンツ制作
- `crosspost` — マルチプラットフォーム配信
- `market-research` — 深いオーディエンスまたは競合インテリジェンス

## 関連コマンド (Related Commands)

- `/plan` — キャンペーン前の戦略計画
- `/plan-prd` — キャンペーンをブリーフする前のプロダクト要件ドキュメント
- `/code-review` — ランディングページ実装のコードレビュー

---

*Part of [Everything Claude Code](https://github.com/affaan-m/everything-claude-code)*
