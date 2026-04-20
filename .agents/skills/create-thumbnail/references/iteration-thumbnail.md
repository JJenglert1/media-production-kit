# Thumbnail Iteration Mappings

Specific refinement mappings for YouTube thumbnail iteration. Use with [iteration-core-images.md](../../../shared/references/iteration-core-images.md).

## Text Refinements

| User Says | Code Change |
|-----------|-------------|
| "change text to X" | Update text string in JSX. If new text is significantly longer/shorter, re-run `fitText()` |
| "bigger text" | Increase `fontSize` by 20-30%, may need to reduce word count |
| "smaller text" | Decrease `fontSize` by 20-30%, add more padding |
| "bolder text" | Increase `fontWeight` (700→800→900), increase stroke width by 1-2px |
| "different font" | Change `loadFont` import, update `fontFamily`, recheck sizing with `fitText()` |
| "add subtitle" / "add secondary text" | Add new text element below headline, 40-60% of headline size, lighter weight |
| "remove text" | Remove the text JSX element |
| "all caps" / "uppercase" | Add `textTransform: 'uppercase'` |
| "move text up/down/left/right" | Adjust flexbox `justifyContent` (vertical) or `alignItems` (horizontal), or modify `padding` |
| "text is hard to read" | Increase stroke width (+2px), add/darken text shadow, add background overlay behind text |

## Color Refinements

| User Says | Code Change |
|-----------|-------------|
| "different colors" | Replace all color hex values in styles — background, text, stroke, accent |
| "darker" / "moodier" | Darken background hex by 20-40%, add dark overlay layer, reduce accent brightness |
| "brighter" / "lighter" | Lighten background, increase text color vibrancy, add glow effects |
| "more contrast" | Increase difference between bg and text — e.g., darker bg + whiter text, thicker stroke |
| "warmer" | Shift palette toward red/orange/yellow hues |
| "cooler" | Shift palette toward blue/cyan/purple hues |
| "more colorful" | Increase saturation, add gradient background, use complementary accent |
| "less colorful" / "muted" | Decrease saturation, use monochrome or analogous palette |
| "red/blue/green/etc." | Change primary or accent color to specified color, adjust complementary colors |

## Layout Refinements

| User Says | Code Change |
|-----------|-------------|
| "more space" / "breathing room" | Increase padding (add 20-40px), reduce element sizes |
| "too cramped" | Same as "more space" — increase margins between elements |
| "center everything" | Set `justifyContent: 'center'`, `alignItems: 'center'`, `textAlign: 'center'` |
| "move face/image left/right" | Change `justifyContent` or `alignItems` on the image container, or use `marginLeft`/`marginRight` |
| "swap sides" | Reverse the layout — if face-right/text-left, make it face-left/text-right |
| "split screen" | Restructure to two equal `<AbsoluteFill>` halves with a divider |
| "full bleed" | Remove padding, let background/image fill entire frame |

## Image/Subject Refinements

| User Says | Code Change |
|-----------|-------------|
| "bigger face/image" | Increase image width/height, adjust `objectFit`, may need to recrop |
| "smaller face/image" | Decrease image dimensions, add more padding |
| "add image" | Copy file to `public/`, add `<Img src={staticFile('filename')}/>` in appropriate layer |
| "remove image" | Remove `<Img>` element, may need to expand text area |
| "add outline to face/image" | Add `filter: 'drop-shadow(0 0 8px #color)'` to image style |
| "add glow" | Add `filter: 'drop-shadow(0 0 X #color) drop-shadow(0 0 Y #color)'` |
| "different image" | Replace file in `public/`, update `src` prop if filename changed |
| "blur the background image" | Add `filter: 'blur(10px)'` to background image style, add `transform: 'scale(1.1)'` to prevent edge blur |

## Effect Refinements

| User Says | Code Change |
|-----------|-------------|
| "add glow" | Add `textShadow: '0 0 Xpx #color, 0 0 Ypx #color'` for text, or `filter: drop-shadow()` for images |
| "remove glow" | Remove glow-related `textShadow` or `filter` values |
| "add border" | Add `border: '4px solid #color'` to outer container, or `outline` |
| "add shadow" | Add `textShadow` for text, `boxShadow` for containers, `filter: drop-shadow()` for images |
| "more dramatic" | Increase all effects — glow spread, shadow depth, gradient intensity, vignette darkness |
| "less effects" / "simpler" | Remove decorative `textShadow`, `filter`, gradient overlays. Keep only essential stroke |
| "add vignette" | Add an overlay `<AbsoluteFill>` with `background: radial-gradient(ellipse, transparent 50%, rgba(0,0,0,0.6) 100%)` |
| "3D text" | Add multi-layer `textShadow`: `'2px 2px 0 #000, 4px 4px 0 rgba(0,0,0,0.5)'` |

## Graphic Element Refinements

| User Says | Code Change |
|-----------|-------------|
| "add arrow" | Add SVG arrow element or a rotated rectangle div pointing at the key element |
| "add circle" | Add a `borderRadius: '50%'` div with `border` around the highlighted area |
| "add badge" / "add number" | Add a positioned div with colored background, border-radius, and large number text |
| "add VS" | Add centered "VS" text in a circle badge between two elements |
| "add divider" | Add a vertical or diagonal line element between two sections |
| "add emoji" / "add icon" | Add emoji character in JSX or import SVG icon |
| "remove arrows/circles/badges" | Remove the corresponding JSX elements |

## Archetype Changes

| User Says | Action |
|-----------|--------|
| "make it more like a reaction thumbnail" | Restructure to Reaction Face + Text layout |
| "make it a comparison" / "make it VS" | Restructure to Versus layout with split screen |
| "text only" / "no images" | Remove image elements, switch to Bold Text Only archetype |
| "add a before/after" | Restructure to Before/After split layout |
| "completely different" / "start over" | Full regeneration — redesign from scratch with new archetype |

Note: Archetype changes typically require partial or full regeneration of `Thumbnail.tsx` since the component structure changes fundamentally.

## Version Management

- Save each iteration: `{name}.png`, `{name}_v2.png`, `{name}_v3.png`
- Never overwrite — user may want to compare or go back
- If user says "go back to v1" — re-render from the original code (or restore from git)
- Present side-by-side comparison notes: "v2 changes: increased text size, swapped to blue palette"
