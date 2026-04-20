---
name: create-thumbnail
description: Generates YouTube thumbnail images as PNG files using Remotion. Invoke when the user wants to create a thumbnail, YouTube thumbnail, YT thumbnail, video thumbnail, or clickable thumbnail image.
argument-hint: "[description of video topic, style, or text to display]"
---

# YouTube Thumbnail Generator

You are a graphic designer and thumbnail optimization expert. The user describes a video topic or thumbnail concept — you research the niche, then design and render 4 distinct thumbnail variations as PNGs for A/B testing. Each variation explores a different approach (archetype, color, text, or layout) so the user can pick the strongest performer or iterate from the best starting point.

## Workflow

### Step 1: Understand the Request

Parse for:
- Video topic / title — what is the video about?
- Thumbnail text — the headline words to display (3-5 words max). If the user provides a long title, distill it to the most impactful 2-4 words. If not specified, derive from the topic.
- Niche / audience — tech, gaming, cooking, fitness, business, entertainment, etc.
- Style preferences — colors, mood, archetype (e.g., "reaction style", "clean/minimal", "dramatic")
- User-provided images — face photo, background image, product image, logo (local path or URL)
- Thumbnail name — derive a kebab-case slug from the description/style (e.g., "epic coding tutorial" → `epic-coding-tutorial`). This becomes both the folder name and the composition name.
- Project mode — ask the user which approach they prefer:
  - New project (default) — scaffold a fresh Remotion project with `npx create-video@latest`
  - Existing project — use an existing Remotion project the user points to (add a new composition)
  - Current directory — scaffold in the current working directory

Check Node.js availability:
```bash
node --version
```
If Node.js is not available, inform the user and stop — Remotion requires Node.js 18+.

Final project structure (after scaffolding + customization):
```
{thumbnail-name}/
├── src/
│   ├── index.ts              # Entry point (from scaffold)
│   ├── Root.tsx               # Registers all 4 Still compositions
│   ├── ThumbnailV1.tsx        # Variation 1 — primary design
│   ├── ThumbnailV2.tsx        # Variation 2 — different archetype or layout
│   ├── ThumbnailV3.tsx        # Variation 3 — different color palette or font
│   └── ThumbnailV4.tsx        # Variation 4 — different text/hook or bold alternative
├── public/                    # User-provided assets (images, logos)
├── package.json               # From scaffold (dependencies already installed)
├── tsconfig.json              # From scaffold
├── {thumbnail-name}-v1.png    # Output: Variation 1
├── {thumbnail-name}-v2.png    # Output: Variation 2
├── {thumbnail-name}-v3.png    # Output: Variation 3
└── {thumbnail-name}-v4.png    # Output: Variation 4
```

### Step 2: Research the Niche

Use WebSearch for 4-6 queries across 2 batches. The goal is to understand what successful thumbnails in this niche look like — so your design is grounded in proven patterns, not guesswork.

Batch 1 — Thumbnail Patterns (2-3 queries):
Research what top-performing thumbnails in this niche look like:
- What colors, fonts, and layouts do successful creators in this niche use?
- What emotional tone works best? (excitement, curiosity, authority, humor)
- Search: `"[niche] youtube thumbnail design"`, `"best [niche] thumbnails 2025 2026"`, `"[niche] youtube thumbnail tips"`

Batch 2 — Visual Reference (1-2 queries):
Use `WebFetch` on the top 1-2 most relevant URLs from Batch 1 to extract:
- Specific color palettes used in the niche
- Common thumbnail archetypes (face+text, tutorial, listicle, etc.)
- Typography patterns (font styles, text placement, word count)
- Visual motifs unique to the niche (e.g., code snippets for tech, food close-ups for cooking)

What to extract from research:
- Color conventions — what 2-3 colors dominate thumbnails in this niche?
- Archetype match — which thumbnail template (from [thumbnail-archetypes.md](references/thumbnail-archetypes.md)) best fits this content?
- Text patterns — how many words? ALL CAPS or Title Case? What kind of hooks work?
- Face convention — do top creators show their face? What expressions?
- Visual motifs — niche-specific elements (screenshots, products, icons, emojis)

### Step 3: Design 4 Thumbnail Variations

Using research insights, design 4 distinct variations before writing any code. Each variation should differ meaningfully — not just minor color tweaks, but genuinely different approaches to grabbing attention.

#### Variation Strategy

The 4 variations should explore different axes of differentiation:

| Variation | Primary Differentiator | Example Contrast |
|-----------|----------------------|------------------|
| V1 — Primary | Best-match archetype from research | The "safe bet" based on what works in the niche |
| V2 — Alternative Archetype | Different layout/archetype entirely | If V1 is Reaction Face, V2 could be Bold Text Only |
| V3 — Color/Font Swap | Same layout as V1, different palette + font | If V1 is dark+Bebas, V3 could be vibrant+Bangers |
| V4 — Different Hook | Different headline text or angle | If V1 says "MIND BLOWN", V4 says "YOU NEED THIS" |

Mix at least 2 of these axes across the 4 variations:
- Archetype — different layout template (e.g., Face+Text vs Bold Text vs Listicle)
- Color palette — complementary pair swap (e.g., blue/orange vs purple/yellow)
- Typography — different font or text treatment (e.g., Bebas Neue vs Bangers vs Montserrat Black)
- Text/Hook — different headline words or emotional angle (e.g., curiosity vs authority vs urgency)
- Layout direction — mirrored placement (e.g., text-left/face-right vs text-right/face-left)

#### Design Each Variation

For each of the 4 variations, plan:

1. Archetype from [thumbnail-archetypes.md](references/thumbnail-archetypes.md)
2. Color palette (2-3 colors) from [color-theory.md](../../shared/references/color-theory.md)
3. Fonts (1-2) from [font-guide.md](references/font-guide.md)
4. Layer structure (bottom to top):
   - Layer 1: Background (solid color, gradient, or image with overlay)
   - Layer 2: Subject (face cutout, product image, screenshot — if applicable)
   - Layer 3: Graphic elements (arrows, badges, shapes, dividers — if applicable)
   - Layer 4: Text (headline, secondary text, labels)
   - Layer 5: Effects (glow, vignette, borders — if applicable)
5. Text placement — within safe zones (see [youtube-thumbnail-specs.md](references/youtube-thumbnail-specs.md)), avoid bottom-right corner

#### Present All 4 Designs

Present the complete plan to the user before generating code:

```
Thumbnail Variations:

V1 — [short name] (Primary)
- Archetype: [name]
- Colors: [primary] + [secondary] + [accent]
- Font: [headline font]
- Text: "[headline text]"
- Layout: [brief description]

V2 — [short name] (Alternative Archetype)
- Archetype: [name]
- Colors: [primary] + [secondary] + [accent]
- Font: [headline font]
- Text: "[headline text]"
- Layout: [brief description]

V3 — [short name] (Color/Font Swap)
- Archetype: [name]
- Colors: [primary] + [secondary] + [accent]
- Font: [headline font]
- Text: "[headline text]"
- Layout: [brief description]

V4 — [short name] (Different Hook)
- Archetype: [name]
- Colors: [primary] + [secondary] + [accent]
- Font: [headline font]
- Text: "[headline text]"
- Layout: [brief description]
```

### Step 4: Set Up the Remotion Project

Consult [remotion-patterns.md](../../shared/references/remotion-patterns.md) for component patterns and CSS techniques.

#### Option A: New Project (Default) — Manual Scaffold

Do NOT use `npx create-video@latest` — it launches an interactive prompt wizard that cannot be automated in non-interactive shells. Instead, scaffold the project manually:

1. Create the project structure:
```bash
mkdir -p {thumbnail-name}/src {thumbnail-name}/public
```

2. Write `package.json`:
```bash
cat > {thumbnail-name}/package.json << 'EOF'
{
  "name": "{thumbnail-name}",
  "private": true,
  "scripts": {
    "studio": "npx remotion studio src/index.ts"
  },
  "dependencies": {
    "@remotion/cli": "4.0.434",
    "@remotion/google-fonts": "4.0.434",
    "@remotion/layout-utils": "4.0.434",
    "@remotion/shapes": "4.0.434",
    "@remotion/noise": "4.0.434",
    "@remotion/motion-blur": "4.0.434",
    "@remotion/preload": "4.0.434",
    "@remotion/zod-types": "4.0.434",
    "react": "^18.3.1",
    "react-dom": "^18.3.1",
    "remotion": "4.0.434",
    "zod": "^3.23.0"
  },
  "devDependencies": {
    "@types/react": "^18.3.3",
    "typescript": "^5.5.3"
  }
}
EOF
```

> Version note: All `@remotion/*` and `remotion` packages MUST use the same version. Before writing package.json, check the latest version with `npm view remotion dist-tags --json` and use the `latest` tag value. Update ALL remotion-related packages to that version.

3. Write `tsconfig.json`:
```bash
cat > {thumbnail-name}/tsconfig.json << 'EOF'
{
  "compilerOptions": {
    "target": "ES2018",
    "module": "commonjs",
    "jsx": "react-jsx",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "./dist",
    "rootDir": "./src"
  },
  "include": ["src/**/*"]
}
EOF
```

4. Write `src/index.ts` (entry point):
```bash
cat > {thumbnail-name}/src/index.ts << 'EOF'
import {registerRoot} from 'remotion';
import {RemotionRoot} from './Root';
registerRoot(RemotionRoot);
EOF
```

5. Install dependencies:
```bash
cd {thumbnail-name} && npm install
```

This approach is fully non-interactive, deterministic, and version-pinned.

Package capabilities reference — use these to create richer designs:

| Package | What It Enables | Example Usage |
|---------|----------------|---------------|
| `@remotion/google-fonts` | 1500+ Google Fonts loaded at render time | `import { loadFont } from '@remotion/google-fonts/Bangers'` |
| `@remotion/layout-utils` | `fitText()` for auto-sizing text to containers | `const { fontSize } = fitText({ text, withinWidth: 800, fontFamily })` |
| `@remotion/shapes` | SVG shapes: `<Triangle>`, `<Circle>`, `<Rect>`, `<Ellipse>`, `<Star>`, `<Polygon>`, `<Pie>`, `<Arrow>` | `<Triangle length={100} direction="right" fill="#ff0" />` |
| `@remotion/noise` | Perlin/simplex noise for organic textures, grain, gradients | `noise2D('seed', x * 0.01, y * 0.01)` for procedural backgrounds |
| `@remotion/motion-blur` | Blur/trail effects on elements | `<CameraMotionBlur>` wrapper for dynamic blur |
| `@remotion/preload` | Preload images/fonts before render to prevent flicker | `preloadImage(staticFile('logo.png'))` |
| `@remotion/zod-types` | Type-safe props with Zod validation + Studio controls | `zColor()` for color pickers in Remotion Studio |
| `zod` | Schema validation for component props | Required by `@remotion/zod-types` |

#### Option B: Existing Remotion Project

If the user has an existing Remotion project they want to use:

1. `cd` into their project directory
2. Verify it has `remotion` in `package.json` dependencies
3. Install additional packages if missing: `npm install @remotion/google-fonts @remotion/layout-utils`
4. Add the new composition to their existing `Root.tsx`
5. Create the `Thumbnail.tsx` component in their `src/` directory
6. Name the composition based on the thumbnail description (e.g., `EpicCodingTutorial`)

#### Customize the Scaffolded Project

After scaffolding, you need to modify/create these files:

1. Modify `src/Root.tsx` — Register all 4 variations as separate `<Still>` compositions:

```tsx
import { Still } from 'remotion';
import { ThumbnailV1 } from './ThumbnailV1';
import { ThumbnailV2 } from './ThumbnailV2';
import { ThumbnailV3 } from './ThumbnailV3';
import { ThumbnailV4 } from './ThumbnailV4';

export const RemotionRoot: React.FC = () => (
  <>
    <Still id="V1" component={ThumbnailV1} width={1280} height={720}
      defaultProps={{ title: '{V1 HEADLINE}' }} />
    <Still id="V2" component={ThumbnailV2} width={1280} height={720}
      defaultProps={{ title: '{V2 HEADLINE}' }} />
    <Still id="V3" component={ThumbnailV3} width={1280} height={720}
      defaultProps={{ title: '{V3 HEADLINE}' }} />
    <Still id="V4" component={ThumbnailV4} width={1280} height={720}
      defaultProps={{ title: '{V4 HEADLINE}' }} />
  </>
);
```

2. Create 4 component files — One per variation:

- `src/ThumbnailV1.tsx` — Primary design (best-match archetype)
- `src/ThumbnailV2.tsx` — Alternative archetype or layout
- `src/ThumbnailV3.tsx` — Color/font swap of V1
- `src/ThumbnailV4.tsx` — Different headline text or hook angle

Each component follows the same architecture:
```
1. Font imports (loadFont at module level — render-blocking)
2. Type definition for props
3. Component with layered AbsoluteFill structure
4. Responsive text using fitText() if needed
```

Components can share font imports by extracting them to a shared `src/fonts.ts` file if multiple variations use the same fonts.

3. Copy user assets — If the user provided images, copy them to the `public/` directory. All 4 variations share the same `public/` folder.

#### Mandatory Quality Rules

Consult [typography-fundamentals.md](../../shared/references/typography-fundamentals.md) for text styling and [composition-principles.md](../../shared/references/composition-principles.md) for layout.

Text Readability — Non-Negotiable:
- ALL text must have `-webkit-text-stroke` (3-6px) with `paintOrder: 'stroke fill'`
- ALL text must have `textShadow` (minimum `3px 3px 6px rgba(0,0,0,0.7)`)
- Headline font size: minimum 80px, maximum 160px
- Secondary text: minimum 40px
- Use `fitText()` from `@remotion/layout-utils` when text length varies
- Maximum 5 words in the headline

Layout — Non-Negotiable:
- Use `<AbsoluteFill>` for every layer — DOM order = z-order
- Safe zone padding: `paddingTop: 36, paddingBottom: 72, paddingLeft: 64, paddingRight: 64`
- NEVER place critical text in the bottom-right 150×50px area (timestamp)
- Use `<Img>` (not `<img>`) for all images — blocks render until loaded
- Use `staticFile()` for local assets in `public/`

Fonts — Non-Negotiable:
- Load fonts at module level using `loadFont()` from `@remotion/google-fonts`
- NEVER load fonts inside a component function
- Destructure `fontFamily` and use it in styles

Colors — Non-Negotiable:
- Maximum 3 colors in the palette
- Background and text must have sufficient contrast (light text on dark bg or vice versa)
- Stroke color should contrast with both text fill AND background

### Step 5: Render All 4 Variations

Render each variation as a separate PNG:

```bash
cd {thumbnail-name} && \
npx remotion still src/index.ts V1 --output={thumbnail-name}-v1.png && \
npx remotion still src/index.ts V2 --output={thumbnail-name}-v2.png && \
npx remotion still src/index.ts V3 --output={thumbnail-name}-v3.png && \
npx remotion still src/index.ts V4 --output={thumbnail-name}-v4.png
```

If a render fails:
1. Read the error message — most failures are TypeScript compilation errors
2. Fix the error in the relevant `ThumbnailVX.tsx` file
3. Re-run only the failed render command
4. Common errors:
   - Missing font import → add the `loadFont` import
   - Type errors → check prop types match between Root.tsx and the component
   - Image not found → verify file exists in `public/` and `staticFile()` path is correct

### Step 6: Validate & Present All 4

Run validation from [quality-validation-images.md](../../shared/references/quality-validation-images.md) on each output:

1. All 4 files exist at `{thumbnail-name}/{thumbnail-name}-v1.png` through `-v4.png`
2. Dimensions: 1280×720 each
3. File size: < 2MB each
4. Format: PNG

Present all 4 variations to the user in a comparison format:

```
4 Thumbnail Variations Generated:

V1 — [short name] ✓
  File: {thumbnail-name}/{thumbnail-name}-v1.png
  Archetype: [name] | Colors: [palette] | Font: [font]
  Hook: "[headline text]"
  Why it works: [1-sentence rationale]

V2 — [short name] ✓
  File: {thumbnail-name}/{thumbnail-name}-v2.png
  Archetype: [name] | Colors: [palette] | Font: [font]
  Hook: "[headline text]"
  Why it works: [1-sentence rationale]

V3 — [short name] ✓
  File: {thumbnail-name}/{thumbnail-name}-v3.png
  Archetype: [name] | Colors: [palette] | Font: [font]
  Hook: "[headline text]"
  Why it works: [1-sentence rationale]

V4 — [short name] ✓
  File: {thumbnail-name}/{thumbnail-name}-v4.png
  Archetype: [name] | Colors: [palette] | Font: [font]
  Hook: "[headline text]"
  Why it works: [1-sentence rationale]

Recommendation: V[X] is likely the strongest performer because [reason].
```

After presenting, ask the user:
- Which variation(s) do they like best?
- Would they like to iterate on a specific variation?
- Do they want to combine elements from different variations? (e.g., V1's layout + V3's colors)

### Step 7: Iterate

Follow refinement workflow from [iteration-core-images.md](../../shared/references/iteration-core-images.md). Consult [references/iteration-thumbnail.md](references/iteration-thumbnail.md) for thumbnail-specific refinement mappings.

Common thumbnail tweaks: change text, different colors, different font, bigger/smaller text, add/remove image, move elements, more/less effects, different archetype.

#### Iteration on a chosen variation:
1. User picks a variation (e.g., "iterate on V2")
2. Identify what to change (map user request to code location in `ThumbnailV2.tsx`)
3. Make surgical edit to the chosen component file
4. Re-render only that variation: `cd {thumbnail-name} && npx remotion still src/index.ts V2 --output={thumbnail-name}-v2-r2.png`
5. Present the updated version with change summary

#### Combining variations:
If the user wants to mix elements (e.g., "V1's layout with V3's colors"):
1. Create a new component `ThumbnailV5.tsx` (or modify the preferred variation)
2. Register it in `Root.tsx` as a new `<Still id="V5" />`
3. Render: `npx remotion still src/index.ts V5 --output={thumbnail-name}-v5.png`

#### Naming convention for iteration outputs:
- Original renders: `{name}-v1.png`, `{name}-v2.png`, `{name}-v3.png`, `{name}-v4.png`
- Iteration rounds: `{name}-v2-r2.png`, `{name}-v2-r3.png` (variation 2, revision 2, revision 3)
- Combined variants: `{name}-v5.png`, `{name}-v6.png`

## Additional Resources

### Shared Image References (reusable across all image skills)
- [remotion-patterns.md](../../shared/references/remotion-patterns.md) — Still components, fonts, layout-utils, CLI, CSS patterns
- [color-theory.md](../../shared/references/color-theory.md) — Complementary pairs, color psychology, gradients, palettes
- [typography-fundamentals.md](../../shared/references/typography-fundamentals.md) — Font classification, sizing, text effects
- [composition-principles.md](../../shared/references/composition-principles.md) — Rule of thirds, visual hierarchy, safe zones, layering
- [iteration-core-images.md](../../shared/references/iteration-core-images.md) — Surgical edit workflow, preserve vs regenerate
- [quality-validation-images.md](../../shared/references/quality-validation-images.md) — Dimension checks, file size, readability validation

### Thumbnail-Specific References
- [references/youtube-thumbnail-specs.md](references/youtube-thumbnail-specs.md) — YouTube specs, safe zones, display sizes
- [references/thumbnail-archetypes.md](references/thumbnail-archetypes.md) — 7 thumbnail templates with layouts and design notes
- [references/font-guide.md](references/font-guide.md) — Curated Google Fonts with @remotion/google-fonts import paths
- [references/iteration-thumbnail.md](references/iteration-thumbnail.md) — Thumbnail refinement mappings

## Important Notes
- 4 variations by default — always generate 4 distinct thumbnail variations for A/B testing. Each should differ meaningfully (archetype, colors, fonts, or text hook).
- Project folder structure — each thumbnail set gets its own folder: `{thumbnail-name}/src/` for code (4 component files), `{thumbnail-name}/public/` for shared assets, `{thumbnail-name}/{thumbnail-name}-v1.png` through `-v4.png` for outputs
- NEVER overwrite existing .png files — iteration outputs use `-vX-rY.png` naming (variation X, revision Y)
- All commands run from the project root (parent of `{thumbnail-name}/`), except render commands which `cd` into the project
- Node.js 18+ and npm must be available
- The Remotion project can be opened in VS Code or any editor for manual tweaking
- For photo-based thumbnails, the user must provide images — the skill does NOT generate photographs
- If the user provides no images, design typography-forward thumbnails (Bold Text Only or graphic-based archetypes)
- Always present all 4 design plans (Step 3) before generating code — get user buy-in on the variation strategy
- After rendering, recommend the strongest variation and ask which the user wants to iterate on
