---
name: trimming
description: Trimming patterns for Remotion - cut the beginning or end of animations
metadata:
  tags: sequence, trim, clip, cut, offset
---

animation の先頭を trim するには、負の `from` 値を持つ `<Sequence>` を使う。

## 先頭のトリム (Trim the Beginning)

負の `from` は時間を後方にずらし、animation を途中から開始させる:

```tsx
import { Sequence, useVideoConfig } from "remotion";

const fps = useVideoConfig();

<Sequence from={-0.5 * fps}>
  <MyAnimation />
</Sequence>
```

animation は進行 15 frame 目から見える — 最初の 15 frame は trim される。
`<MyAnimation>` 内の `useCurrentFrame()` は 0 ではなく 15 から始まる。

## 末尾のトリム (Trim the End)

`durationInFrames` で指定時間後に unmount する:

```tsx

<Sequence durationInFrames={1.5 * fps}>
  <MyAnimation />
</Sequence>
```

45 frame 再生後に component が unmount する。

## トリムと遅延 (Trim and Delay)

sequence をネストして先頭 trim と表示遅延の両方を行う:

```tsx
<Sequence from={30}>
  <Sequence from={-15}>
    <MyAnimation />
  </Sequence>
</Sequence>
```

内側 sequence が先頭 15 frame を trim し、外側 sequence が結果を 30 frame 遅延させる。
