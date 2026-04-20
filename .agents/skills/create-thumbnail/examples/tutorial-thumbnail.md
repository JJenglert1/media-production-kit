# Example: Listicle / Tutorial — 4 Variations

## User Request

```
/create-thumbnail 5 javascript tips every developer needs
```

## Step 1: Parse

- Topic: JavaScript tips for developers
- Text: "5 JS TIPS" (distilled — "every developer needs" is too long for a thumbnail)
- Niche: Tech / Programming / Web Development
- Style: Listicle/Number + Tutorial hybrid (number-driven educational content)
- Images: None provided — typography-forward design with code motif
- Name: `5-js-tips`
- Project mode: New project (default)

## Step 2: Research Findings

- JavaScript tutorial thumbnails: dark/code-editor backgrounds, yellow (JS brand color) accents
- Listicle thumbnails with numbers: the number itself is the visual anchor
- Top tech educators use: clean dark backgrounds, large numbers, minimal text, subtle code imagery
- JavaScript's brand yellow: #f7df1e — highly recognizable in the developer community
- Font: Montserrat or Inter ExtraBold for modern tech feel

## Step 3: Design 4 Variations

```
Thumbnail Variations:

V1 — JS Yellow Badge (Primary)
- Archetype: Listicle/Number
- Colors: #1a1a2e (dark navy) + #f7df1e (JS yellow) + #ffffff (white)
- Font: Montserrat Black (number + headline) + Inter Bold (secondary)
- Text: "5" (280px in yellow badge) + "JS TIPS" (100px)
- Layout: Large yellow circle with "5" left, "JS TIPS" + "Every Dev Needs" right

V2 — Code Editor (Alternative Archetype)
- Archetype: Tutorial/How-To
- Colors: #1e1e1e (VS Code dark) + #f7df1e (JS yellow) + #4fc1ff (syntax blue)
- Font: Inter Black + JetBrains Mono style
- Text: "5 JS TIPS"
- Layout: Code editor-styled background with syntax highlighting motif, text centered

V3 — Neon on Black (Color/Font Swap)
- Archetype: Listicle/Number (same as V1)
- Colors: #0a0a0a (black) + #22d3ee (cyan neon) + #a855f7 (purple)
- Font: Anton (number) + Poppins Black (headline)
- Text: "5" (neon glow) + "JS TIPS"
- Layout: Same as V1 structure but cyberpunk neon aesthetic

V4 — Urgency Hook (Different Text)
- Archetype: Bold Text Only
- Colors: #1a1a2e (dark) + #ef4444 (red) + #f7df1e (JS yellow)
- Font: Bebas Neue
- Text: "STOP WRITING BAD JS"
- Layout: Full-frame text, red/yellow split emphasis, no number — provocative hook
```

## Step 4: Scaffold & Customize

```bash
# Scaffold the project
npx create-video@latest 5-js-tips --blank

# Install additional packages
cd 5-js-tips && npm install @remotion/google-fonts @remotion/layout-utils
```

Then modify `src/Root.tsx` to register 4 `<Still>` compositions and create `src/ThumbnailV1.tsx` through `src/ThumbnailV4.tsx`.

### ThumbnailV1.tsx (primary — Listicle, JS Yellow)

```tsx
import { AbsoluteFill } from 'remotion';
import { loadFont as loadMontserrat } from '@remotion/google-fonts/Montserrat';
import { loadFont as loadInter } from '@remotion/google-fonts/Inter';

const { fontFamily: headlineFont } = loadMontserrat('normal', {
  weights: ['900'], subsets: ['latin'],
});
const { fontFamily: bodyFont } = loadInter('normal', {
  weights: ['700'], subsets: ['latin'],
});

type Props = { number: string; title: string; subtitle: string };

export const ThumbnailV1: React.FC<Props> = ({ number, title, subtitle }) => (
  <AbsoluteFill>
    <AbsoluteFill style={{
      background: 'linear-gradient(135deg, #0a0a1a 0%, #1a1a2e 50%, #0f0f20 100%)',
    }} />
    <AbsoluteFill style={{
      backgroundImage: 'linear-gradient(rgba(255,255,255,0.03) 1px, transparent 1px), linear-gradient(90deg, rgba(255,255,255,0.03) 1px, transparent 1px)',
      backgroundSize: '40px 40px',
    }} />
    <AbsoluteFill style={{
      flexDirection: 'row', alignItems: 'center', padding: '48px 80px', gap: 60,
    }}>
      <div style={{
        display: 'flex', justifyContent: 'center', alignItems: 'center',
        width: 320, height: 320, borderRadius: '50%',
        background: 'linear-gradient(135deg, #f7df1e 0%, #f0c800 100%)',
        boxShadow: '0 0 40px rgba(247,223,30,0.3), 0 0 80px rgba(247,223,30,0.1)',
        flexShrink: 0,
      }}>
        <span style={{
          fontFamily: headlineFont, fontSize: 220, fontWeight: 900,
          color: '#1a1a2e', lineHeight: 1,
        }}>{number}</span>
      </div>
      <div style={{ display: 'flex', flexDirection: 'column', gap: 12 }}>
        <h1 style={{
          fontFamily: headlineFont, fontSize: 110, fontWeight: 900, color: '#ffffff',
          WebkitTextStroke: '4px rgba(0,0,0,0.8)', paintOrder: 'stroke fill',
          textShadow: '3px 3px 8px rgba(0,0,0,0.6)',
          lineHeight: 1.0, textTransform: 'uppercase', margin: 0,
        }}>{title}</h1>
        <p style={{
          fontFamily: bodyFont, fontSize: 48, fontWeight: 700, color: '#f7df1e',
          textShadow: '2px 2px 4px rgba(0,0,0,0.5)',
          margin: 0, textTransform: 'uppercase', letterSpacing: '0.05em',
        }}>{subtitle}</p>
      </div>
    </AbsoluteFill>
    <AbsoluteFill style={{
      background: 'radial-gradient(ellipse, transparent 40%, rgba(0,0,0,0.4) 100%)',
    }} />
  </AbsoluteFill>
);
```

### ThumbnailV2.tsx (Code Editor style — Tutorial archetype)

VS Code dark theme background, syntax-colored text, monospace accents.

### ThumbnailV3.tsx (Neon cyberpunk — cyan + purple)

Same listicle layout as V1 but neon glow aesthetic with Anton font.

### ThumbnailV4.tsx (Provocative hook — "STOP WRITING BAD JS")

Bold Text Only archetype, full-frame Bebas Neue, red/yellow split.

## Step 5: Render All 4

```bash
cd 5-js-tips && \
npx remotion still src/index.ts V1 --output=5-js-tips-v1.png && \
npx remotion still src/index.ts V2 --output=5-js-tips-v2.png && \
npx remotion still src/index.ts V3 --output=5-js-tips-v3.png && \
npx remotion still src/index.ts V4 --output=5-js-tips-v4.png
```

## Step 6: Output

```
4 Thumbnail Variations Generated:

V1 — JS Yellow Badge ✓
  File: 5-js-tips/5-js-tips-v1.png (312KB)
  Archetype: Listicle/Number | Colors: navy + JS yellow + white | Font: Montserrat Black
  Hook: "5 JS TIPS"
  Why it works: Number is the visual anchor, JS yellow is instantly recognizable

V2 — Code Editor ✓
  File: 5-js-tips/5-js-tips-v2.png (287KB)
  Archetype: Tutorial | Colors: VS Code dark + JS yellow + syntax blue | Font: Inter Black
  Hook: "5 JS TIPS"
  Why it works: Code editor aesthetic resonates with developers, feels authentic

V3 — Neon Cyberpunk ✓
  File: 5-js-tips/5-js-tips-v3.png (341KB)
  Archetype: Listicle/Number | Colors: black + cyan + purple | Font: Anton
  Hook: "5 JS TIPS"
  Why it works: Neon stands out in a sea of muted thumbnails, eye-catching in search

V4 — Provocative Hook ✓
  File: 5-js-tips/5-js-tips-v4.png (198KB)
  Archetype: Bold Text Only | Colors: dark + red + JS yellow | Font: Bebas Neue
  Hook: "STOP WRITING BAD JS"
  Why it works: Provocative statement triggers curiosity — "am I writing bad JS?"

Recommendation: V1 is the safest (proven listicle format), but V4 could significantly
outperform with its provocative hook — it creates a curiosity gap that V1's
straightforward "5 JS TIPS" doesn't.
```
