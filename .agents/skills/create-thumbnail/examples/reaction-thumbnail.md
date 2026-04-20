# Example: Reaction Face + Text — 4 Variations

## User Request

```
/create-thumbnail epic coding tutorial that blew my mind
```

## Step 1: Parse

- Topic: Coding tutorial, mind-blowing content
- Text: "MIND BLOWN" (distilled from description)
- Niche: Tech / Programming
- Style: Reaction face + dramatic text
- Images: User provided `face.png` (creator's reaction face with surprised expression)
- Name: `mind-blown-coding`
- Project mode: New project (default)

## Step 2: Research Findings

- Tech/coding thumbnails: dark backgrounds (navy, charcoal), accent colors (electric blue, green, orange)
- Top tech YouTubers use: close-up face + 2-3 word headline + subtle code/screen visual
- Fonts: Bebas Neue and Montserrat ExtraBold dominate the niche
- Color palette: dark bg (#0f172a) with white text and blue/orange accents

## Step 3: Design 4 Variations

```
Thumbnail Variations:

V1 — Dark Dramatic (Primary)
- Archetype: Reaction Face + Text
- Colors: #0f172a (navy) + #ffffff (white) + #e94560 (red glow)
- Font: Bebas Neue
- Text: "MIND BLOWN"
- Layout: Face right 50%, headline left, dark gradient + red glow behind face

V2 — Bold Text Only (Alternative Archetype)
- Archetype: Bold Text Only
- Colors: #0a0a0a (black) + #f97316 (orange) + #ffffff (white)
- Font: Anton
- Text: "THIS CHANGES EVERYTHING"
- Layout: Centered text, dramatic gradient, no face — pure typography impact

V3 — Electric Blue (Color/Font Swap)
- Archetype: Reaction Face + Text (same as V1)
- Colors: #0f172a (navy) + #ffffff (white) + #3b82f6 (electric blue glow)
- Font: Montserrat Black
- Text: "MIND BLOWN"
- Layout: Same as V1 but blue glow, different font weight/style

V4 — Curiosity Hook (Different Text)
- Archetype: Reaction Face + Text
- Colors: #1a1a2e (dark) + #eab308 (yellow) + #ffffff (white)
- Font: Bebas Neue
- Text: "YOU NEED THIS"
- Layout: Face left, text right, yellow accent — flipped from V1, urgency-driven hook
```

## Step 4: Scaffold & Customize

```bash
# Scaffold the project
npx create-video@latest mind-blown-coding --blank

# Install additional packages
cd mind-blown-coding && npm install @remotion/google-fonts @remotion/layout-utils

# Copy user's face image to public/
cp /path/to/face.png mind-blown-coding/public/face.png
```

Then modify `src/Root.tsx` to register 4 `<Still>` compositions and create `src/ThumbnailV1.tsx` through `src/ThumbnailV4.tsx`.

### ThumbnailV1.tsx (primary — Reaction Face + Text, red glow)

```tsx
import { AbsoluteFill, Img, staticFile } from 'remotion';
import { loadFont as loadBebasNeue } from '@remotion/google-fonts/BebasNeue';

const { fontFamily: headlineFont } = loadBebasNeue();

type Props = { title: string };

export const ThumbnailV1: React.FC<Props> = ({ title }) => (
  <AbsoluteFill>
    <AbsoluteFill style={{
      background: 'linear-gradient(135deg, #0f172a 0%, #1e293b 50%, #0f172a 100%)',
    }} />
    <AbsoluteFill style={{
      background: 'radial-gradient(circle at 70% 50%, rgba(233,69,96,0.3) 0%, transparent 50%)',
    }} />
    <AbsoluteFill style={{ justifyContent: 'flex-end', alignItems: 'flex-end' }}>
      <Img src={staticFile('face.png')} style={{
        height: '100%', objectFit: 'contain', objectPosition: 'bottom right',
        filter: 'drop-shadow(0 0 15px rgba(233,69,96,0.6)) drop-shadow(0 0 30px rgba(233,69,96,0.3))',
      }} />
    </AbsoluteFill>
    <AbsoluteFill style={{
      justifyContent: 'center', alignItems: 'flex-start',
      padding: '48px 64px', paddingRight: '50%',
    }}>
      <h1 style={{
        fontFamily: headlineFont, fontSize: 140, color: '#ffffff',
        WebkitTextStroke: '5px #000000', paintOrder: 'stroke fill',
        textShadow: '4px 4px 12px rgba(0,0,0,0.8)',
        lineHeight: 1.0, textTransform: 'uppercase', margin: 0,
      }}>{title}</h1>
    </AbsoluteFill>
    <AbsoluteFill style={{
      background: 'radial-gradient(ellipse, transparent 50%, rgba(0,0,0,0.5) 100%)',
    }} />
  </AbsoluteFill>
);
```

### ThumbnailV2.tsx (Bold Text Only — no face, orange/black)

Different archetype entirely — pure typography impact.

### ThumbnailV3.tsx (Blue glow variant — Montserrat Black)

Same layout as V1 but electric blue `#3b82f6` glow and Montserrat Black font.

### ThumbnailV4.tsx (Flipped layout, yellow accent, different hook)

Face on left, "YOU NEED THIS" text on right, yellow `#eab308` accent.

## Step 5: Render All 4

```bash
cd mind-blown-coding && \
npx remotion still src/index.ts V1 --output=mind-blown-coding-v1.png && \
npx remotion still src/index.ts V2 --output=mind-blown-coding-v2.png && \
npx remotion still src/index.ts V3 --output=mind-blown-coding-v3.png && \
npx remotion still src/index.ts V4 --output=mind-blown-coding-v4.png
```

## Step 6: Output

```
4 Thumbnail Variations Generated:

V1 — Dark Dramatic ✓
  File: mind-blown-coding/mind-blown-coding-v1.png (847KB)
  Archetype: Reaction Face + Text | Colors: navy + white + red | Font: Bebas Neue
  Hook: "MIND BLOWN"
  Why it works: Classic tech thumbnail formula — dark bg, expressive face, bold text

V2 — Bold Text Only ✓
  File: mind-blown-coding/mind-blown-coding-v2.png (312KB)
  Archetype: Bold Text Only | Colors: black + orange + white | Font: Anton
  Hook: "THIS CHANGES EVERYTHING"
  Why it works: Pure curiosity-driven — no face lets the words do the work

V3 — Electric Blue ✓
  File: mind-blown-coding/mind-blown-coding-v3.png (891KB)
  Archetype: Reaction Face + Text | Colors: navy + white + blue | Font: Montserrat Black
  Hook: "MIND BLOWN"
  Why it works: Blue is the #1 tech niche color — more trustworthy/professional than red

V4 — Curiosity Hook ✓
  File: mind-blown-coding/mind-blown-coding-v4.png (823KB)
  Archetype: Reaction Face + Text | Colors: dark + yellow + white | Font: Bebas Neue
  Hook: "YOU NEED THIS"
  Why it works: Urgency-driven hook + yellow grabs attention in search results

Recommendation: V1 is the safest performer (proven formula), but V4's urgency hook
could outperform in search results where "MIND BLOWN" blends in with similar thumbnails.
```
