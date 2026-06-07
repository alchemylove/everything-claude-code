# スタイルプリセット参照 (Style Presets Reference)

`frontend-slides` 向け curated visual style。

この file の用途:
- 必須 viewport-fitting CSS base
- preset 選択と mood mapping
- CSS gotcha と validation rule

抽象 shape のみ。ユーザーが明示的に求めない限り illustration は避ける。

## Viewport Fit は非交渉 (Viewport Fit Is Non-Negotiable)

各 slide は 1 viewport に完全に収まる必要がある。

### 黄金律 (Golden Rule)

```text
Each slide = exactly one viewport height.
Too much content = split into more slides.
Never scroll inside a slide.
```

### 密度上限 (Density Limits)

| Slide Type | Maximum Content |
|------------|-----------------|
| Title slide | 1 heading + 1 subtitle + optional tagline |
| Content slide | 1 heading + 4-6 bullets or 2 paragraphs |
| Feature grid | 6 cards maximum |
| Code slide | 8-10 lines maximum |
| Quote slide | 1 quote + attribution |
| Image slide | 1 image, ideally under 60vh |

## 必須 Base CSS (Mandatory Base CSS)

生成 presentation ごとにこの block をコピーし、その上で theme する。

```css
/* ===========================================
   VIEWPORT FITTING: MANDATORY BASE STYLES
   =========================================== */

html, body {
    height: 100%;
    overflow-x: hidden;
}

html {
    scroll-snap-type: y mandatory;
    scroll-behavior: smooth;
}

.slide {
    width: 100vw;
    height: 100vh;
    height: 100dvh;
    overflow: hidden;
    scroll-snap-align: start;
    display: flex;
    flex-direction: column;
    position: relative;
}

.slide-content {
    flex: 1;
    display: flex;
    flex-direction: column;
    justify-content: center;
    max-height: 100%;
    overflow: hidden;
    padding: var(--slide-padding);
}

:root {
    --title-size: clamp(1.5rem, 5vw, 4rem);
    --h2-size: clamp(1.25rem, 3.5vw, 2.5rem);
    --h3-size: clamp(1rem, 2.5vw, 1.75rem);
    --body-size: clamp(0.75rem, 1.5vw, 1.125rem);
    --small-size: clamp(0.65rem, 1vw, 0.875rem);

    --slide-padding: clamp(1rem, 4vw, 4rem);
    --content-gap: clamp(0.5rem, 2vw, 2rem);
    --element-gap: clamp(0.25rem, 1vw, 1rem);
}

.card, .container, .content-box {
    max-width: min(90vw, 1000px);
    max-height: min(80vh, 700px);
}

.feature-list, .bullet-list {
    gap: clamp(0.4rem, 1vh, 1rem);
}

.feature-list li, .bullet-list li {
    font-size: var(--body-size);
    line-height: 1.4;
}

.grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(min(100%, 250px), 1fr));
    gap: clamp(0.5rem, 1.5vw, 1rem);
}

img, .image-container {
    max-width: 100%;
    max-height: min(50vh, 400px);
    object-fit: contain;
}

@media (max-height: 700px) {
    :root {
        --slide-padding: clamp(0.75rem, 3vw, 2rem);
        --content-gap: clamp(0.4rem, 1.5vw, 1rem);
        --title-size: clamp(1.25rem, 4.5vw, 2.5rem);
        --h2-size: clamp(1rem, 3vw, 1.75rem);
    }
}

@media (max-height: 600px) {
    :root {
        --slide-padding: clamp(0.5rem, 2.5vw, 1.5rem);
        --content-gap: clamp(0.3rem, 1vw, 0.75rem);
        --title-size: clamp(1.1rem, 4vw, 2rem);
        --body-size: clamp(0.7rem, 1.2vw, 0.95rem);
    }

    .nav-dots, .keyboard-hint, .decorative {
        display: none;
    }
}

@media (max-height: 500px) {
    :root {
        --slide-padding: clamp(0.4rem, 2vw, 1rem);
        --title-size: clamp(1rem, 3.5vw, 1.5rem);
        --h2-size: clamp(0.9rem, 2.5vw, 1.25rem);
        --body-size: clamp(0.65rem, 1vw, 0.85rem);
    }
}

@media (max-width: 600px) {
    :root {
        --title-size: clamp(1.25rem, 7vw, 2.5rem);
    }

    .grid {
        grid-template-columns: 1fr;
    }
}

@media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
        animation-duration: 0.01ms !important;
        transition-duration: 0.2s !important;
    }

    html {
        scroll-behavior: auto;
    }
}
```

## Viewport チェックリスト (Viewport Checklist)

- 各 `.slide` に `height: 100vh`、`height: 100dvh`、`overflow: hidden` がある
- 全 typography が `clamp()` を使う
- 全 spacing が `clamp()` または viewport unit
- image に `max-height` constraint がある
- grid が `auto-fit` + `minmax()` で adapt する
- short-height breakpoint が `700px`、`600px`、`500px` にある
- cramped に感じたら slide を split する

## Mood から Preset への Mapping (Mood to Preset Mapping)

| Mood | Good Presets |
|------|--------------|
| Impressed / Confident | Bold Signal, Electric Studio, Dark Botanical |
| Excited / Energized | Creative Voltage, Neon Cyber, Split Pastel |
| Calm / Focused | Notebook Tabs, Paper & Ink, Swiss Modern |
| Inspired / Moved | Dark Botanical, Vintage Editorial, Pastel Geometry |

## Preset カタログ (Preset Catalog)

### 1. Bold Signal

- Vibe: confident、high-impact、keynote-ready
- Best for: pitch deck、launch、statement
- Fonts: Archivo Black + Space Grotesk
- Palette: charcoal base、hot orange focal card、crisp white text
- Signature: oversized section numbers、dark field 上の high-contrast card

### 2. Electric Studio

- Vibe: clean、bold、agency-polished
- Best for: client presentation、strategic review
- Fonts: Manrope only
- Palette: black、white、saturated cobalt accent
- Signature: two-panel split と sharp editorial alignment

### 3. Creative Voltage

- Vibe: energetic、retro-modern、playful confidence
- Best for: creative studio、brand work、product storytelling
- Fonts: Syne + Space Mono
- Palette: electric blue、neon yellow、deep navy
- Signature: halftone texture、badge、punchy contrast

### 4. Dark Botanical

- Vibe: elegant、premium、atmospheric
- Best for: luxury brand、thoughtful narrative、premium product deck
- Fonts: Cormorant + IBM Plex Sans
- Palette: near-black、warm ivory、blush、gold、terracotta
- Signature: blurred abstract circle、fine rule、restrained motion

### 5. Notebook Tabs

- Vibe: editorial、organized、tactile
- Best for: report、review、structured storytelling
- Fonts: Bodoni Moda + DM Sans
- Palette: charcoal 上の cream paper と pastel tab
- Signature: paper sheet、colored side tab、binder detail

### 6. Pastel Geometry

- Vibe: approachable、modern、friendly
- Best for: product overview、onboarding、lighter brand deck
- Fonts: Plus Jakarta Sans only
- Palette: pale blue field、cream card、soft pink/mint/lavender accent
- Signature: vertical pill、rounded card、soft shadow

### 7. Split Pastel

- Vibe: playful、modern、creative
- Best for: agency intro、workshop、portfolio
- Fonts: Outfit only
- Palette: peach + lavender split と mint badge
- Signature: split backdrop、rounded tag、light grid overlay

### 8. Vintage Editorial

- Vibe: witty、personality-driven、magazine-inspired
- Best for: personal brand、opinionated talk、storytelling
- Fonts: Fraunces + Work Sans
- Palette: cream、charcoal、dusty warm accent
- Signature: geometric accent、bordered callout、punchy serif headline

### 9. Neon Cyber

- Vibe: futuristic、techy、kinetic
- Best for: AI、infra、dev tool、future-of-X talk
- Fonts: Clash Display + Satoshi
- Palette: midnight navy、cyan、magenta
- Signature: glow、particle、grid、data-radar energy

### 10. Terminal Green

- Vibe: developer-focused、hacker-clean
- Best for: API、CLI tool、engineering demo
- Fonts: JetBrains Mono only
- Palette: GitHub dark + terminal green
- Signature: scan line、command-line framing、precise monospace rhythm

### 11. Swiss Modern

- Vibe: minimal、precise、data-forward
- Best for: corporate、product strategy、analytics
- Fonts: Archivo + Nunito
- Palette: white、black、signal red
- Signature: visible grid、asymmetry、geometric discipline

### 12. Paper & Ink

- Vibe: literary、thoughtful、story-driven
- Best for: essay、keynote narrative、manifesto deck
- Fonts: Cormorant Garamond + Source Serif 4
- Palette: warm cream、charcoal、crimson accent
- Signature: pull quote、drop cap、elegant rule

## 直接選択 Prompt (Direct Selection Prompts)

ユーザーが既に欲しい style を知っている場合、preview 生成を強制せず上記 preset 名から直接選ばせる。

## アニメーション Feel Mapping (Animation Feel Mapping)

| Feeling | Motion Direction |
|---------|------------------|
| Dramatic / Cinematic | slow fades, parallax, large scale-ins |
| Techy / Futuristic | glow, particles, grid motion, scramble text |
| Playful / Friendly | springy easing, rounded shapes, floating motion |
| Professional / Corporate | subtle 200-300ms transitions, clean slides |
| Calm / Minimal | very restrained movement, whitespace-first |
| Editorial / Magazine | strong hierarchy, staggered text and image interplay |

## CSS Gotcha: 関数の Negation (CSS Gotcha: Negating Functions)

次は書かない:

```css
right: -clamp(28px, 3.5vw, 44px);
margin-left: -min(10vw, 100px);
```

browser は silently ignore する。

代わりに常にこう書く:

```css
right: calc(-1 * clamp(28px, 3.5vw, 44px));
margin-left: calc(-1 * min(10vw, 100px));
```

## 検証サイズ (Validation Sizes)

最低限テスト:
- Desktop: `1920x1080`, `1440x900`, `1280x720`
- Tablet: `1024x768`, `768x1024`
- Mobile: `375x667`, `414x896`
- Landscape phone: `667x375`, `896x414`

## アンチパターン (Anti-Patterns)

使わない:
- purple-on-white startup template
- ユーザーが utilitarian neutrality を明示的に望まない限り Inter / Roboto / Arial を visual voice に
- bullet wall、tiny type、scroll が必要な code block
- abstract geometry で足りるのに decorative illustration
