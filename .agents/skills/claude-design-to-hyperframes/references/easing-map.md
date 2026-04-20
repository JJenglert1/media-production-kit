# Easing Map: Claude Design → GSAP

## Direct equivalents

| Claude Design `Easing.*`     | GSAP string          | Notes                                      |
|------------------------------|----------------------|--------------------------------------------|
| `Easing.easeLinear`          | `"none"`             | No easing                                  |
| `Easing.easeInQuad`          | `"power1.in"`        |                                            |
| `Easing.easeOutQuad`         | `"power1.out"`       |                                            |
| `Easing.easeInOutQuad`       | `"power1.inOut"`     |                                            |
| `Easing.easeInCubic`         | `"power2.in"`        |                                            |
| `Easing.easeOutCubic`        | `"power2.out"`       | Most common in Claude Design               |
| `Easing.easeInOutCubic`      | `"power2.inOut"`     |                                            |
| `Easing.easeInQuart`         | `"power3.in"`        |                                            |
| `Easing.easeOutQuart`        | `"power3.out"`       |                                            |
| `Easing.easeInOutQuart`      | `"power3.inOut"`     |                                            |
| `Easing.easeInQuint`         | `"power4.in"`        |                                            |
| `Easing.easeOutQuint`        | `"power4.out"`       |                                            |
| `Easing.easeInOutQuint`      | `"power4.inOut"`     |                                            |
| `Easing.easeInExpo`          | `"expo.in"`          |                                            |
| `Easing.easeOutExpo`         | `"expo.out"`         |                                            |
| `Easing.easeInOutExpo`       | `"expo.inOut"`       |                                            |
| `Easing.easeInCirc`          | `"circ.in"`          |                                            |
| `Easing.easeOutCirc`         | `"circ.out"`         |                                            |
| `Easing.easeInOutCirc`       | `"circ.inOut"`       |                                            |
| `Easing.easeInBack`          | `"back.in(1.7)"`     | Default overshoot = 1.7                    |
| `Easing.easeOutBack`         | `"back.out(1.7)"`    | Uses overshoot — check visually            |
| `Easing.easeInOutBack`       | `"back.inOut(1.7)"`  |                                            |
| `Easing.easeInElastic`       | `"elastic.in(1,0.3)"`|                                            |
| `Easing.easeOutElastic`      | `"elastic.out(1,0.3)"`|                                           |
| `Easing.easeInOutElastic`    | `"elastic.inOut(1,0.3)"`|                                        |
| `Easing.easeInBounce`        | `"bounce.in"`        |                                            |
| `Easing.easeOutBounce`       | `"bounce.out"`       |                                            |
| `Easing.easeInOutBounce`     | `"bounce.inOut"`     |                                            |
| `Easing.easeInSine`          | `"sine.in"`          |                                            |
| `Easing.easeOutSine`         | `"sine.out"`         |                                            |
| `Easing.easeInOutSine`       | `"sine.inOut"`       |                                            |

## Custom easing via function

Claude Design sometimes uses inline easing:
```js
const t = Math.min(1, Math.max(0, (localTime - start) / dur));
const eased = t * t * (3 - 2 * t); // smoothstep
```

In GSAP, use `CustomEase` or approximate with a built-in:
```js
// smoothstep ≈ "sine.inOut" — visually close enough for most uses
gsap.to(el, { x: 100, ease: "sine.inOut", duration: dur }, start);
```

## Clamped progress pattern

Claude Design often clamps time manually:
```js
const t = Easing.easeOutCubic(Math.min(1, Math.max(0, (localTime - 0.5) / 0.3)));
```

This means: start animating at 0.5s, finish at 0.8s (duration 0.3s), with easeOutCubic.

In GSAP, just set the position and duration:
```js
tl.from("#el", { opacity: 0, duration: 0.3, ease: "power2.out" }, 0.5);
// GSAP handles clamping automatically — no Math.min/max needed
```
