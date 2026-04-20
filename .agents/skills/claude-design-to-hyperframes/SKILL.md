# Skill: claude-design-to-hyperframes

Convert a Claude Design export folder into a working HyperFrames video composition.

## When to use

Invoke `/claude-design-to-hyperframes` when the user:
- Points to a Claude Design export folder or zip file
- Wants to turn a Claude Design HTML/JSX animation into an MP4
- Has a `.html` + `scenes.jsx` from Claude Design and wants it in HyperFrames

---

## Step-by-step workflow

### Step 1 — Inventory the export

Read the export folder and identify:
- `scenes.jsx` or `scenes.bundled.jsx` — the animation source of truth
- All `<Sprite start={N} end={N}>` blocks — these become HyperFrames clips
- All asset references (`src="assets/..."`) — images, video, audio
- `Brand Guidelines.html` if present — extract colors, fonts, and design tokens

### Step 2 — Extract the scene structure

From `scenes.jsx`, build a scene map:

```
Scene map (example):
  [0.0 – 2.3s]  ScanBar + BootHUD (background: persistent PixelBackdrop)
  [1.0 – 3.9s]  PixelAura + JJChorus + PortraitMeta
  [3.85 – 4.15s] InkWipe transition
  [4.0 – 10s]   Wordmark + JJGhost
  [8.6 – 10s]   FinalStamp
  [0 – 10s]     Chrome (always-on overlay)
  [0 – 10s]     StageVideo (background video)
  [0 – 10s]     Soundtrack (audio)
```

Use `start` and `end` props on every `<Sprite>` tag. Components OUTSIDE Sprites are persistent (always visible).

### Step 3 — Plan the HyperFrames project

Decide the composition layout:
- **Simple scenes** (few elements, short duration) → inline in `index.html`
- **Complex scenes** (many sub-components, > 3s) → separate file in `compositions/`
- **Shared layers** (background, audio, persistent overlays) → always in `index.html`

Assets: copy the export's `assets/` folder into the HyperFrames `assets/` folder, or reference them by relative path.

### Step 4 — Build `index.html`

Set up the root composition. Total duration = max `end` time across all Sprites.

```html
<div id="root" data-composition-id="main"
     data-start="0" data-duration="10"
     data-width="1920" data-height="1080">

  <!-- Persistent background (no class="clip" — always visible) -->
  <div style="position:absolute;inset:0;background:#0A0A1F;"></div>

  <!-- Background video (persistent) -->
  <video src="assets/stage-bg.mp4" autoplay muted loop playsinline
    style="position:absolute;inset:0;width:100%;height:100%;object-fit:cover;opacity:0.18;"></video>

  <!-- Audio (persistent, muted for renderer — see audio rules) -->
  <audio id="track" src="assets/podcast-intro.wav" data-audio="true"></audio>

  <!-- Clips (timed elements — always need class="clip") -->
  <div id="scene-1" class="clip"
       data-start="0" data-duration="2.3" data-track-index="1"
       data-composition-src="compositions/scene-scan.html"></div>

  <div id="scene-2" class="clip"
       data-start="1.0" data-duration="2.9" data-track-index="2"
       data-composition-src="compositions/scene-portrait.html"></div>

  <!-- ... more clips ... -->
</div>

<script>
  window.__timelines = window.__timelines || {};
  const tl = gsap.timeline({ paused: true });
  // Root-level GSAP tweens go here (cross-scene elements)
  window.__timelines["main"] = tl;
</script>
```

### Step 5 — Translate each component into a composition

For each scene, create a file in `compositions/` and translate the React component's visual logic into GSAP. See `references/translation-guide.md` for the full pattern library.

Key translation rules:
- `useSprite().localTime` → GSAP `tl.time()` inside the sub-timeline
- `<Sprite start={s} end={e}>` → `<div class="clip" data-start="s" data-duration="e-s">`
- CSS-in-JS computed from time → GSAP tweens (`.to()`, `.from()`, `.fromTo()`)
- `interpolate([0,1],[a,b], ease)(t)` → GSAP tween with equivalent easing (see `references/easing-map.md`)
- `Math.min(1, t / dur)` progress clamping → GSAP handles this automatically per tween
- React state → remove entirely; GSAP drives all state

### Step 6 — Lint and fix

```bash
npx hyperframes lint
```

Fix all errors before finishing. Then preview:

```bash
npx hyperframes preview
```

---

## Audio rules

HyperFrames renders with audio muted by default. Use this pattern:

```html
<!-- ✅ Correct audio pattern -->
<video id="video" src="assets/clip.mp4" muted autoplay playsinline style="..."></video>
<audio src="assets/clip-audio.wav" data-audio="true"></audio>
```

For background music tracks: add `data-audio="true"` on the `<audio>` tag.

---

## Things to watch for

- **`useTime()` in persistent components** (Chrome, StageVideo, PixelBackdrop) — these run the full duration. Model their animations as GSAP tweens added to the root timeline.
- **`Easing.easeOutBack`** — uses overshoot. GSAP equivalent: `"back.out(1.7)"`. Check visually.
- **SVG-based animations** (pixel fills, path draws) — translate directly into HTML/SVG with GSAP `drawSVG` or opacity tweens.
- **Pixel/particle systems** built with `React.useMemo` and SVG rects — reproduce in vanilla JS, called once at load time, driven by GSAP.
- **`localStorage` for mute state** — remove this entirely; HyperFrames controls audio at render time.
- **`window.Intro = Intro`** at the end of scenes.jsx — this is the Claude Design entry point, not needed in HyperFrames.

---

## Reference files

- `references/export-anatomy.md` — what's in every Claude Design export
- `references/translation-guide.md` — component-by-component translation patterns
- `references/easing-map.md` — Claude Design Easing.* → GSAP easing strings
