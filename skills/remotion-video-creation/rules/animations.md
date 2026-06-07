---
name: animations
description: Fundamental animation skills for Remotion
metadata:
  tags: animations, transitions, frames, useCurrentFrame
---

すべての animation は `useCurrentFrame()` hook で駆動しなければならない。
animation は秒単位で書き、`useVideoConfig()` の `fps` 値を掛ける。

```tsx
import { useCurrentFrame } from "remotion";

export const FadeIn = () => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();

  const opacity = interpolate(frame, [0, 2 * fps], [0, 1], {
    extrapolateRight: 'clamp',
  });

  return (
    <div style={{ opacity }}>Hello World!</div>
  );
};
```

CSS transition または animation は **禁止** — 正しくレンダリングされない。
Tailwind animation クラス名も **禁止** — 正しくレンダリングされない。
