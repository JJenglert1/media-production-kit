# Claude Design Export — Anatomy

Every Claude Design export is a folder (or zip) with this structure:

```
[project-name]/
├── [project-name].html         ← standalone bundled preview (not the source)
├── [name] (standalone).html    ← alternate standalone version
├── Brand Guidelines.html       ← brand system (colors, fonts, usage rules)
├── Logo.html                   ← logo variants
│
├── intro/                      ← or named after the composition
│   ├── scenes.jsx              ← SOURCE: React components + animation logic
│   └── scenes.bundled.jsx      ← transpiled/bundled version (skip this — use scenes.jsx)
│
├── assets/                     ← media used in the composition
│   ├── *.png                   ← image cutouts, graphics
│   ├── *.jpg                   ← photos
│   ├── *.mp4                   ← video clips, background loops
│   ├── *.wav / *.mp3           ← music, voiceover, SFX
│   └── *.svg                   ← vector graphics
│
├── uploads/                    ← original user-uploaded files (raw inputs)
│   └── ...                     
│
├── site-assets/                ← processed/generated assets
│   └── ...
│
└── screenshots/                ← preview frames (ignore these)
    └── *.png / *.jpg
```

## The scenes.jsx file

This is the only file you need for the animation logic. It exports React components using the Claude Design Stage API:

### API surface used in scenes.jsx

```js
// Hooks
useTime()                      // → number: global playback time in seconds
useSprite()                    // → { localTime, duration, progress }
useTimeline()                  // → { time, playing, duration }

// Components
<Sprite start={0} end={2.3}>   // timed clip — children only visible between start and end
  {children}
</Sprite>

// Animation utilities
interpolate([inputA, inputB], [outputA, outputB], easingFn)(value)
// → maps value from input range to output range

Easing.easeOutCubic            // and other Easing.* functions (see easing-map.md)
```

### What to extract from scenes.jsx

1. **All `<Sprite start={N} end={N}>` blocks** → these map directly to HyperFrames clips
2. **Components defined OUTSIDE Sprites** → persistent layers (always visible)
3. **Color constants** at the top (`const INK = '#0A0A1F'`) → your palette
4. **Font families** (`const SG = "'Space Grotesk', sans-serif"`) → load via Google Fonts
5. **Asset `src` references** → inventory every file needed

## Brand Guidelines.html

Contains:
- CSS custom properties in `:root {}` — copy these directly as CSS variables
- Font stack used across the brand
- Color names and hex values
- Logo usage rules

Extract the `:root {}` block and paste it into your HyperFrames composition's `<style>` tag.

## What to ignore

- `scenes.bundled.jsx` — transpiled output, redundant
- `screenshots/` — preview frames only
- `uploads/` — raw source files, already processed into `assets/`
- The standalone `.html` files — these are Claude Design's bundled previews, not source
