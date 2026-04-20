# YouTube Thumbnail Specifications

Technical requirements and platform constraints for YouTube thumbnails.

## Image Dimensions

| Property | Value |
|----------|-------|
| Recommended size | 1280 × 720 pixels |
| Minimum width | 640 pixels |
| Aspect ratio | 16:9 |
| Maximum file size | 2 MB |
| Color space | sRGB |

## Supported File Formats

| Format | Extension | Best For |
|--------|-----------|----------|
| PNG | `.png` | Text-heavy thumbnails, graphics, sharp edges (recommended) |
| JPEG | `.jpg` / `.jpeg` | Photo-heavy thumbnails, smaller file size |
| GIF | `.gif` | Simple graphics (not animated — YouTube uses first frame) |
| BMP | `.bmp` | Uncompressed (avoid — large file size) |

Recommendation: Always use PNG for thumbnails with text. JPEG compression creates artifacts around text edges that reduce readability at small sizes.

## Display Sizes Across Devices

Thumbnails are displayed at drastically different sizes. Design must work at ALL of these:

| Context | Display Size | Notes |
|---------|-------------|-------|
| Desktop search results | ~360 × 202 px | Primary discovery surface |
| Mobile search results | ~168 × 94 px | Critical — smallest common size |
| Desktop home feed | ~320 × 180 px | Recommendation cards |
| Mobile home feed | ~168 × 94 px | Same as mobile search |
| Desktop sidebar (suggested) | ~168 × 94 px | Very small |
| TV / Smart TV | ~640 × 360 px | Largest display context |
| Embedded player (end screen) | ~160 × 90 px | Smallest display context |
| Playlist sidebar | ~160 × 90 px | Very small, partially obscured |

Design rule: If the thumbnail doesn't read at 160 × 90 pixels, it will fail on mobile and suggested videos — where the majority of views come from.

## Safe Zones

### Bottom-Right: Duration Timestamp

YouTube overlays the video duration in the bottom-right corner:

```
┌─────────────────────────────────────────┐
│                                         │
│                                         │
│                                         │
│                                         │
│                                         │
│                                 ┌──────┐│
│                                 │ 12:34││
│                                 └──────┘│
└─────────────────────────────────────────┘
```

- Size: ~44-62px wide × 20px tall (varies by duration length)
- Position: 4px from right edge, 4px from bottom edge
- Background: Semi-transparent dark rounded rectangle
- Text: White, small font

Rule: Never place critical text, faces, or small details in the bottom-right ~150 × 50px area.

### Bottom Edge: Title Bar & Progress Bar

On many surfaces, the video title text appears directly below or overlapping the thumbnail bottom. The red progress bar (for partially watched videos) also appears at the very bottom.

Rule: Keep critical elements out of the bottom ~10% (72px at 720px height).

### Playlist Overlay

When thumbnails appear in playlists, a semi-transparent overlay covers the right ~30% of the thumbnail showing playlist info.

Rule: For thumbnails likely to appear in playlists, keep critical content in the left 70%.

### Mobile Crop

Some mobile views may slightly crop the edges. Keep critical content within:
- Horizontal safe zone: 64px from left/right edges (5% each side)
- Vertical safe zone: 36px from top, 72px from bottom

## Safe Zone Summary — Pixel Values at 1280×720

```
┌── 64px ──┬──── SAFE ZONE (1152 × 612) ────┬── 64px ──┐
│          │                                  │          │
│  36px    │     Place all critical content   │  36px    │
│  top     │     within this rectangle        │  top     │
│          │                                  │          │
│          │                                  │          │
│          │                                  │          │
│          │                          ┌───────┤          │
│  72px    │                          │AVOID  │  72px    │
│  bottom  │                          │150×50 │  bottom  │
│          │                          └───────┤          │
└──────────┴──────────────────────────────────┴──────────┘
```

### In Remotion

```tsx
// Safe zone container
<AbsoluteFill style={{
  paddingTop: 36,
  paddingBottom: 72,
  paddingLeft: 64,
  paddingRight: 64,
}}>
  {/* All critical text and visual elements here */}
</AbsoluteFill>
```

## Rendering in Remotion

```tsx
// Root.tsx — register the still composition
import { Still } from 'remotion';
import { Thumbnail } from './Thumbnail';

export const RemotionRoot: React.FC = () => (
  <>
    <Still
      id="Thumbnail"
      component={Thumbnail}
      width={1280}
      height={720}
      defaultProps={{
        title: 'DEFAULT TITLE',
      }}
    />
  </>
);
```

```bash
# Render command
cd {project-dir} && npx remotion still src/index.ts Thumbnail --output={name}.png

# With custom props
npx remotion still src/index.ts Thumbnail --output={name}.png --props='{"title":"MY TITLE"}'
```

## Optimization Tips

- File size too large? Switch from PNG to JPEG (`--image-format=jpeg --jpeg-quality=85`)
- Want higher resolution? Use `--scale=2` for 2560×1440 output (note: YouTube will downscale, but it looks sharper on 4K displays)
- Quick iteration? Use `--image-format=jpeg --jpeg-quality=70` for fast preview renders, then switch to PNG for final
