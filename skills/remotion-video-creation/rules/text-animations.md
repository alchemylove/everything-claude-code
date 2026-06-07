---
name: text-animations
description: Typography and text animation patterns for Remotion.
metadata:
  tags: typography, text, typewriter, highlighter ken
---

## テキストアニメーション (Text animations)

`useCurrentFrame()` に基づき、文字列を 1 文字ずつ減らして typewriter effect を作る。

## タイプライター効果 (Typewriter Effect)

点滅カーソルと最初の文の後の pause 付き高度な例は [Typewriter](assets/text-animations-typewriter.tsx) を参照。

typewriter effect では常に string slicing を使う。文字ごとの opacity は使わない。

## 単語ハイライト (Word Highlight)

蛍光ペンのような単語ハイライトの animate 例は [Word Highlight](assets/text-animations-word-highlight.tsx) を参照。
