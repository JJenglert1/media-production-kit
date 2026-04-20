# Font Guide for YouTube Thumbnails

Curated Google Fonts optimized for thumbnail readability, with `@remotion/google-fonts` import paths.

## Impact / Display Fonts (Primary Headlines)

These fonts are designed for maximum visual impact at any size. Use for the main headline.

### Bebas Neue — The Thumbnail Standard
- Style: Tall, condensed, all-caps sans-serif
- Best for: Universal — works for every niche and archetype
- Import: `import { loadFont } from '@remotion/google-fonts/BebasNeue';`
- Weight: Single weight (400 renders as bold due to font design)
- Size range: 100-200px for headlines
- Notes: The #1 most-used thumbnail font. Always a safe choice.

### Anton — Maximum Bold
- Style: Ultra-bold condensed sans-serif
- Best for: Dramatic/shocking moments, high-energy content, gaming
- Import: `import { loadFont } from '@remotion/google-fonts/Anton';`
- Weight: Single weight (400)
- Size range: 100-180px for headlines
- Notes: Heavier than Bebas Neue. Great for 1-3 word headlines.

### Oswald — Modern Condensed
- Style: Modern condensed sans-serif with multiple weights
- Best for: Versatile — tech, business, educational, clean designs
- Import: `import { loadFont } from '@remotion/google-fonts/Oswald';`
- Weights: 200-700 (use `700` for headlines)
- Size range: 80-160px for headlines
- Notes: More refined than Anton. Good when you need both headline and secondary text from the same family.

### Black Ops One — Military/Gaming Stencil
- Style: Bold stencil-style display font
- Best for: Gaming, military, action, tech-aggressive content
- Import: `import { loadFont } from '@remotion/google-fonts/BlackOpsOne';`
- Weight: Single weight (400)
- Size range: 80-140px for headlines
- Notes: Very niche — only use for gaming/action content.

### Bangers — Comic / Playful
- Style: Comic book / hand-drawn display font
- Best for: Entertainment, comedy, kids content, fun/casual
- Import: `import { loadFont } from '@remotion/google-fonts/Bangers';`
- Weight: Single weight (400)
- Size range: 90-160px for headlines
- Notes: Adds personality and energy. Avoid for professional/serious content.

### Permanent Marker — Handwritten
- Style: Marker/handwriting display font
- Best for: Casual vlogs, DIY, personal stories, authentic/raw feel
- Import: `import { loadFont } from '@remotion/google-fonts/PermanentMarker';`
- Weight: Single weight (400)
- Size range: 80-140px for headlines
- Notes: Feels authentic and personal. Pair with a clean sans-serif.

### Bungee — Geometric Display
- Style: Bold geometric display, designed for vertical and rotated text
- Best for: Bold statements, urban/street, modern graphics
- Import: `import { loadFont } from '@remotion/google-fonts/Bungee';`
- Weight: Single weight (400)
- Size range: 80-140px for headlines
- Notes: Very distinctive. Works well for single words or very short phrases.

## Bold Sans-Serif Fonts (Headlines or Secondary)

Modern, clean fonts with heavy weights available. Versatile across niches.

### Montserrat — Geometric All-Rounder
- Style: Geometric sans-serif with full weight range
- Best for: Professional, modern, tech, business, education
- Import: `import { loadFont } from '@remotion/google-fonts/Montserrat';`
- Weights: 100-900 (use `800` or `900` for headlines)
- Size range: 70-140px for headlines, 36-60px for secondary
- Notes: Extremely versatile. ExtraBold (800) and Black (900) work great for headlines.

### Poppins — Rounded Geometric
- Style: Rounded geometric sans-serif
- Best for: Friendly, modern, tech, lifestyle, approachable
- Import: `import { loadFont } from '@remotion/google-fonts/Poppins';`
- Weights: 100-900 (use `800` or `900` for headlines)
- Size range: 70-140px for headlines, 36-60px for secondary
- Notes: Softer than Montserrat due to rounded terminals. Great for friendly/approachable brands.

### Inter — Clean Modern
- Style: Highly legible sans-serif designed for screens
- Best for: Tech, SaaS, professional, clean designs
- Import: `import { loadFont } from '@remotion/google-fonts/Inter';`
- Weights: 100-900 (use `800` or `900` for headlines)
- Size range: 60-120px for headlines, 30-50px for secondary
- Notes: Designed for screen readability. ExtraBold and Black weights are excellent for thumbnails.

### Raleway — Elegant Sans-Serif
- Style: Elegant, slightly condensed sans-serif
- Best for: Premium, fashion, design, art, photography
- Import: `import { loadFont } from '@remotion/google-fonts/Raleway';`
- Weights: 100-900 (use `800` or `900` for headlines)
- Size range: 70-130px for headlines, 36-56px for secondary

## Clean Sans-Serif Fonts (Secondary Text)

Legible, neutral fonts for supporting text, subtitles, and labels.

### Roboto — Google's Default
- Import: `import { loadFont } from '@remotion/google-fonts/Roboto';`
- Weights: 100-900

### Roboto Condensed — Compact Support
- Import: `import { loadFont } from '@remotion/google-fonts/RobotoCondensed';`
- Weights: 100-900

### Open Sans — Universal Legibility
- Import: `import { loadFont } from '@remotion/google-fonts/OpenSans';`
- Weights: 300-800

### Lato — Friendly Neutral
- Import: `import { loadFont } from '@remotion/google-fonts/Lato';`
- Weights: 100-900

### Barlow Condensed — Narrow Support
- Import: `import { loadFont } from '@remotion/google-fonts/BarlowCondensed';`
- Weights: 100-900

## Specialty Fonts

### Press Start 2P — Pixel/Retro Gaming
- Import: `import { loadFont } from '@remotion/google-fonts/PressStart2P';`
- Best for: Retro gaming, 8-bit aesthetic, nostalgia
- Notes: Pixel font — use at sizes divisible by 8 for crisp rendering (e.g., 32px, 48px, 64px)

### Creepster — Horror/Spooky
- Import: `import { loadFont } from '@remotion/google-fonts/Creepster';`
- Best for: Horror, Halloween, creepy content
- Notes: Very niche — adds instant horror atmosphere

### Righteous — Retro/Groovy
- Import: `import { loadFont } from '@remotion/google-fonts/Righteous';`
- Best for: Retro, 70s/80s aesthetic, music, vintage
- Notes: Rounded, retro feel without being unreadable

## Font Pairing Recommendations

| Headline Font | Secondary Font | Vibe |
|--------------|----------------|------|
| Bebas Neue | Inter | Clean, versatile, professional |
| Anton | Roboto | Bold, modern, tech |
| Oswald | Lato | Editorial, polished |
| Bangers | Poppins | Fun, energetic, youthful |
| Montserrat Black | Montserrat Regular | Cohesive, modern |
| Black Ops One | Roboto Condensed | Gaming, military, action |
| Permanent Marker | Open Sans | Authentic, personal, casual |
| Bebas Neue | Montserrat | Universal, professional |
| Bungee | Inter | Bold graphic, modern |
| Press Start 2P | Roboto | Retro gaming meets modern |

## Font Loading Pattern

```tsx
// Load at module level (outside component) — ensures render-blocking
import { loadFont as loadBebasNeue } from '@remotion/google-fonts/BebasNeue';
import { loadFont as loadInter } from '@remotion/google-fonts/Inter';

const { fontFamily: headlineFont } = loadBebasNeue();
const { fontFamily: bodyFont } = loadInter('normal', {
  weights: ['400', '700'],
  subsets: ['latin'],
});

// Use in component
export const Thumbnail: React.FC = () => (
  <AbsoluteFill>
    <h1 style={{ fontFamily: headlineFont, fontSize: 140 }}>HEADLINE</h1>
    <p style={{ fontFamily: bodyFont, fontSize: 48, fontWeight: 700 }}>Secondary text</p>
  </AbsoluteFill>
);
```

## Size Guidelines

### At 1280×720 (YouTube Thumbnail)

| Element | Font Size | Font Weight | Text Transform |
|---------|----------|-------------|----------------|
| Primary headline | 100-160px | 800-900 (or font default if single-weight) | UPPERCASE |
| Secondary headline | 50-80px | 700 | Title Case or UPPERCASE |
| Badge / label | 30-50px | 700 | UPPERCASE |
| Minimum readable | 30px | 600+ | Any |

### Dynamic Sizing with fitText

```tsx
import { fitText } from '@remotion/layout-utils';

const { fontSize } = fitText({
  text: title,
  withinWidth: 800,  // max width for text
  fontFamily: headlineFont,
  fontWeight: '400', // match the font's actual weight
});

// Cap the result
const finalSize = Math.min(Math.max(fontSize, 60), 160);
```

## Niche-Specific Font Recommendations

| Niche | Headline Font | Why |
|-------|--------------|-----|
| Tech / Programming | Inter Black or Montserrat Black | Clean, modern, professional |
| Gaming | Black Ops One or Press Start 2P | Matches gaming aesthetic |
| Cooking / Food | Poppins Black or Lato Black | Warm, friendly, approachable |
| Fitness | Anton or Bebas Neue | Bold, powerful, energetic |
| Business / Finance | Oswald or Montserrat Black | Professional, authoritative |
| Comedy / Entertainment | Bangers or Permanent Marker | Fun, expressive, casual |
| Music | Bebas Neue or Bungee | Stylish, modern, bold |
| Education | Montserrat Black or Inter Black | Clean, trustworthy |
| Beauty / Lifestyle | Raleway ExtraBold or Poppins Black | Elegant, friendly |
| Travel | Oswald or Bebas Neue | Clean, adventurous |
