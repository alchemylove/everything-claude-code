---
name: tailwind
description: Using TailwindCSS in Remotion.
metadata:
---

プロジェクトに TailwindCSS がインストールされていれば、Remotion で使用してよい（推奨）。

`transition-*` または `animate-*` クラスは使わない — 常に `useCurrentFrame()` hook で animate する。

Remotion プロジェクトでは先に Tailwind をインストール・有効化する必要がある — 手順は WebFetch で <https://www.remotion.dev/docs/tailwind> を参照。
