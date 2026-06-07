> このファイルは [common/performance.md](../common/performance.md) を拡張し、Web 固有のパフォーマンス内容を追加する。

# Web パフォーマンスルール (Web Performance Rules)

## Core Web Vitals 目標 (Core Web Vitals Targets)

| メトリクス | 目標 |
|-----------|------|
| LCP | < 2.5s |
| INP | < 200ms |
| CLS | < 0.1 |
| FCP | < 1.5s |
| TBT | < 200ms |

## バンドルバジェット (Bundle Budget)

| ページタイプ | JS バジェット（gzip 圧縮後） | CSS バジェット |
|-------------|---------------------------|--------------|
| ランディングページ | < 150kb | < 30kb |
| アプリページ | < 300kb | < 50kb |
| マイクロサイト | < 80kb | < 15kb |

## ローディング戦略 (Loading Strategy)

1. 正当な場合、クリティカルなアバブ・ザ・フォールド CSS をインライン化する
2. ヒーロー画像とプライマリフォントのみをプリロードする
3. 非クリティカルな CSS や JS を遅延読み込みする
4. 重いライブラリを動的インポートする

```js
const gsapModule = await import('gsap');
const { ScrollTrigger } = await import('gsap/ScrollTrigger');
```

## 画像最適化 (Image Optimization)

- 明示的な `width` と `height`
- ヒーローメディアのみに `loading="eager"` と `fetchpriority="high"`
- ビロウ・ザ・フォールドのアセットには `loading="lazy"`
- フォールバック付きで AVIF または WebP を優先する
- レンダリングサイズを大幅に超えるソース画像を配信しない

## フォント読み込み (Font Loading)

- 明確な例外がない限り、フォントファミリーは最大2つ
- `font-display: swap`
- 可能な場合はサブセット化する
- 本当にクリティカルなウェイト/スタイルのみをプリロードする

## アニメーションパフォーマンス (Animation Performance)

- コンポジタフレンドリーなプロパティのみをアニメーションする
- `will-change` は狭い範囲で使用し、完了時に削除する
- シンプルなトランジションには CSS を優先する
- JS モーションには `requestAnimationFrame` または確立されたアニメーションライブラリを使用する
- スクロールハンドラの乱発を避ける。IntersectionObserver または行儀の良いライブラリを使用する

## パフォーマンスチェックリスト (Performance Checklist)

- [ ] すべての画像に明示的なサイズがある
- [ ] 意図しないレンダーブロッキングリソースがない
- [ ] 動的コンテンツによるレイアウトシフトがない
- [ ] モーションがコンポジタフレンドリーなプロパティにとどまっている
- [ ] サードパーティスクリプトが async/defer で読み込まれ、必要な場合のみ使用されている
