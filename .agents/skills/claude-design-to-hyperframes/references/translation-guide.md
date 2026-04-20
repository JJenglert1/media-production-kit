# Claude Design → HyperFrames Translation Guide

## Core concept shift

Claude Design renders animations by **re-running React components every frame** — components read `useTime()` and return different JSX based on the current time.

HyperFrames uses **GSAP timelines** — you define *what changes when* up front, and GSAP drives it. The HTML is static; GSAP mutates it.

---

## Pattern 1: Sprite → Clip

**Claude Design:**
```jsx
<Sprite start={1.0} end={3.9}>
  <PortraitMeta />
</Sprite>
```

**HyperFrames:**
```html
<div id="portrait-meta" class="clip"
     data-start="1.0" data-duration="2.9"
     data-track-index="2"
     data-composition-src="compositions/portrait-meta.html">
</div>
```

`data-duration = end - start`

---

## Pattern 2: Time-based opacity fade

**Claude Design:**
```jsx
function MyComponent() {
  const { localTime, duration } = useSprite();
  const enter = Math.min(1, localTime / 0.5);
  const exit = localTime > duration - 0.4
    ? 1 - (localTime - (duration - 0.4)) / 0.4
    : 1;
  return <div style={{ opacity: enter * exit }}>...</div>;
}
```

**HyperFrames:**
```js
// In the composition's <script>
const tl = gsap.timeline({ paused: true });
tl.from("#my-component", { opacity: 0, duration: 0.5, ease: "power1.out" }, 0);
tl.to("#my-component", { opacity: 0, duration: 0.4, ease: "power1.in" }, duration - 0.4);
window.__timelines["composition-id"] = tl;
```

---

## Pattern 3: Slide-in from direction

**Claude Design:**
```jsx
const enter = Easing.easeOutCubic(Math.min(1, localTime / 0.5));
const tx = (1 - enter) * 24;
<div style={{ transform: `translate(${tx}px, -50%)`, opacity: enter }}>
```

**HyperFrames:**
```js
tl.from("#element", { x: 24, opacity: 0, duration: 0.5, ease: "power2.out" }, 0);
```

---

## Pattern 4: interpolate() call

**Claude Design:**
```jsx
const sweepX = interpolate([0, 1], [-5, 105], Easing.easeInOutCubic)(progress);
```

**HyperFrames:**
```js
// interpolate(inputRange, outputRange, easing)(value) is just a GSAP tween
// from -5 to 105, over the full duration, with easeInOutCubic
tl.fromTo("#scan-bar", { left: "-5%" }, { left: "105%", ease: "power2.inOut", duration: totalDuration }, 0);
```

---

## Pattern 5: Typewriter / character-by-character reveal

**Claude Design:**
```jsx
const chars = Math.min(text.length, Math.floor(elapsed / charDur));
return <div>{text.slice(0, chars)}</div>;
```

**HyperFrames:**
```js
// Use GSAP's text plugin or a manual character-split approach
// Simple approach: set innerHTML char by char via a GSAP ticker alternative
// OR: split into individual <span> elements and stagger them in
gsap.utils.toArray(".char").forEach((el, i) => {
  tl.from(el, { opacity: 0, duration: 0.01 }, i * charDur);
});
```

Pre-split the text in HTML:
```html
<div id="boot-hud">
  <span class="char">&gt;</span>
  <span class="char"> </span>
  <span class="char">I</span>
  <!-- ... -->
</div>
```

---

## Pattern 6: Scale + rotation entry

**Claude Design:**
```jsx
const plusT = Easing.easeOutBack(Math.min(1, Math.max(0, (localTime - 1.10) / 0.45)));
<span style={{ transform: `scale(${0.2 + 0.8 * plusT}) rotate(${(1 - plusT) * 45}deg)` }}>+</span>
```

**HyperFrames:**
```js
tl.from("#plus", { scale: 0.2, rotation: 45, duration: 0.45, ease: "back.out(1.7)" }, 1.10);
```

---

## Pattern 7: Sequential line-by-line reveal

**Claude Design:**
```jsx
lines.map((line, i) => {
  const lineStart = i * lineDur;
  const opacity = localTime > lineStart ? 1 : 0;
  return <div style={{ opacity }}>{line}</div>;
});
```

**HyperFrames:**
```html
<div id="line-0" class="line" style="opacity:0">line 1</div>
<div id="line-1" class="line" style="opacity:0">line 2</div>
<div id="line-2" class="line" style="opacity:0">line 3</div>
```
```js
document.querySelectorAll(".line").forEach((el, i) => {
  tl.to(el, { opacity: 1, duration: 0.1 }, i * lineDur);
});
```

---

## Pattern 8: Background video

**Claude Design:**
```jsx
function StageVideo() {
  return (
    <video src="assets/stage-bg.mp4" autoPlay muted loop playsInline
      style={{ opacity: 0.18, filter: 'grayscale(0.6) ...' }}
    />
  );
}
```

**HyperFrames (persistent — outside any clip):**
```html
<video src="assets/stage-bg.mp4" autoplay muted loop playsinline
  style="position:absolute;inset:0;width:100%;height:100%;
         object-fit:cover;opacity:0.18;
         filter:grayscale(0.6) contrast(1.1) brightness(0.55);">
</video>
```

---

## Pattern 9: Audio track

**Claude Design:**
```jsx
<audio ref={ref} src="assets/podcast-intro.wav" preload="auto" loop={false}/>
```

**HyperFrames:**
```html
<!-- Always use muted video + separate audio element -->
<audio src="assets/podcast-intro.wav" data-audio="true" preload="auto"></audio>
```

Do NOT include volume-ducking JavaScript logic — HyperFrames controls audio at render time.

---

## Pattern 10: SVG particle / pixel grid

**Claude Design:**
```jsx
const dots = React.useMemo(() => {
  // generate stable dot positions via seeded RNG
  return arr; // [{x, y, size, phase, rate}, ...]
}, []);

return (
  <svg viewBox="0 0 1920 1080">
    {dots.map((d, i) => {
      const pulse = 0.5 + 0.5 * Math.sin(t * d.rate + d.phase);
      return <rect key={i} x={d.x} y={d.y} fill={LIME} opacity={pulse * 0.45} />;
    })}
  </svg>
);
```

**HyperFrames:**
```html
<svg id="pixel-backdrop" viewBox="0 0 1920 1080" style="position:absolute;inset:0;width:100%;height:100%;">
  <!-- dots generated by script below -->
</svg>
```
```js
// Generate dots once at load (same seeded RNG logic)
const svg = document.getElementById("pixel-backdrop");
const dots = generateDots(); // copy the useMemo logic as a plain function

// Animate each rect's opacity using GSAP
dots.forEach((d, i) => {
  const rect = document.createElementNS("http://www.w3.org/2000/svg", "rect");
  rect.setAttribute("x", d.x - d.size / 2);
  rect.setAttribute("y", d.y - d.size / 2);
  rect.setAttribute("width", d.size);
  rect.setAttribute("height", d.size);
  rect.setAttribute("fill", "#D4FF3A");
  svg.appendChild(rect);

  // Create a pulsing tween for each dot
  gsap.to(rect, {
    opacity: 0.45 * 0.8,
    duration: 1 / d.rate,
    repeat: -1,
    yoyo: true,
    ease: "sine.inOut",
    delay: d.phase / (2 * Math.PI * d.rate),
  });
});
```

---

## Pattern 11: CSS masking (WebkitMaskImage)

**Claude Design:**
```jsx
<div style={{
  background: LIME,
  WebkitMaskImage: `url(assets/jj-cutout.png)`,
  WebkitMaskSize: 'contain',
  WebkitMaskRepeat: 'no-repeat',
  opacity: 0.22,
}} />
```

**HyperFrames:**
```html
<div id="jj-ghost" style="
  position:absolute; right:40px; bottom:0; width:680px; height:920px;
  background:#D4FF3A;
  -webkit-mask-image: url(assets/jj-cutout.png);
  -webkit-mask-size: contain;
  -webkit-mask-position: bottom right;
  -webkit-mask-repeat: no-repeat;
  mask-image: url(assets/jj-cutout.png);
  mask-size: contain;
  mask-position: bottom right;
  mask-repeat: no-repeat;
">
</div>
```

---

## Pattern 12: Cross-scene scaleX underline wipe

**Claude Design:**
```jsx
<div style={{
  transformOrigin: 'left center',
  transform: `scaleX(${underlineT})`,
}} />
```

**HyperFrames:**
```js
tl.from("#underline", { scaleX: 0, transformOrigin: "left center", duration: 0.6, ease: "power2.inOut" }, underlineStart);
```

---

## Persistent components (no Sprite wrapper)

Components defined outside `<Sprite>` run for the full composition duration. In HyperFrames, implement these as:
- Persistent HTML elements at the root of the composition (no `class="clip"`)
- Animations added to the root GSAP timeline
- Background layers placed BEFORE clips in the HTML (lower z-index)
- Overlay layers placed AFTER clips (higher z-index)
