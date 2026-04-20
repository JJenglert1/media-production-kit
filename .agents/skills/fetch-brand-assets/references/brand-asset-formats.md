# Brand Asset File Formats

Guide to image file formats for brand assets — when to use each, quality considerations, and conversion priorities.

## Format Comparison

| Format | Type | Transparency | Scalable | Best For |
|--------|------|-------------|----------|----------|
| SVG | Vector | ✅ | ✅ Infinite | Logos, icons, wordmarks — the gold standard |
| PNG | Raster | ✅ | ❌ | Photos with transparency, detailed graphics |
| WebP | Raster | ✅ | ❌ | Web-optimized versions (smaller than PNG) |
| JPG | Raster | ❌ | ❌ | Photos, backgrounds (not logos) |
| EPS | Vector | ✅ | ✅ | Print production (legacy, use SVG instead) |
| PDF | Vector/Raster | ✅ | ✅ | Print-ready assets (often from press kits) |
| ICO | Raster | ✅ | ❌ | Favicons only (multi-resolution container) |
| AVIF | Raster | ✅ | ❌ | Next-gen web format (better compression) |

## Format Priority for Brand Assets

### Logos & Logomarks
```
SVG > PNG (≥512px) > PDF > EPS > PNG (any) > JPG
```
- SVG is always preferred — infinitely scalable, small file size, editable
- PNG acceptable at ≥512px for raster workflows
- Never accept JPG for logos (no transparency, compression artifacts on edges)

### Icons & Favicons
```
SVG > PNG (≥256px) > ICO (multi-res)
```
- Square aspect ratio required
- For favicon use cases, need multiple sizes (16, 32, 48, 180, 512)

### Wordmarks (Text-Only Marks)
```
SVG > PNG (≥1024px wide)
```
- Text rendering quality matters — SVG preserves crisp edges
- PNG must be high enough resolution that text is sharp

## Quality Indicators

### SVG Quality Checks
- File should start with `<svg` or `<?xml`
- Should contain `viewBox` attribute (enables proper scaling)
- File size typically 1KB-100KB for logos
- Should NOT contain embedded raster images (`<image>` tags with base64 data) — defeats the purpose of vector
- Watch for: SVGs that are actually traced bitmaps (poor quality curves)

### PNG Quality Checks
- Minimum useful dimensions: 256×256 for icons, 512px wide for logos
- Ideal: ≥1024px on longest side
- Should have transparency (alpha channel) for logos
- File size: 10KB-5MB typical range for logos
- Watch for: upscaled low-res images (blurry edges, pixelation artifacts)

### Red Flags (Any Format)
- File size < 1KB → likely placeholder or corrupt
- File size > 10MB → likely unoptimized or wrong format
- HTML content type → downloaded an error page, not an image
- Dimensions exactly 1×1 → tracking pixel, not a logo
- Multiple embedded fonts in SVG → may be a full webpage export, not a clean logo

## Common Brand Asset Variants

| Variant | Description | Filename Convention |
|---------|-------------|-------------------|
| Full-color | Primary brand colors | `{brand}-logo.svg` |
| Monochrome | Single color (usually black) | `{brand}-logo-mono.svg` |
| Reversed / White | For dark backgrounds | `{brand}-logo-white.svg` |
| Dark mode | Specific dark-background variant | `{brand}-logo-dark.svg` |
| Icon only | Logomark without text | `{brand}-icon.svg` |
| Wordmark only | Text without icon | `{brand}-wordmark.svg` |
| Horizontal | Wide layout (icon + text side by side) | `{brand}-logo-horizontal.svg` |
| Stacked | Vertical layout (icon above text) | `{brand}-logo-stacked.svg` |

## Conversion Notes

When the needed format isn't available from official sources:

| From → To | Method | Notes |
|-----------|--------|-------|
| SVG → PNG | Use `transform-image` skill | Specify target dimensions, Pillow + cairosvg |
| PNG → WebP | Use `transform-image` skill | Lossless mode for logos |
| PNG → JPG | Use `transform-image` skill | Only for photos, never logos (loses transparency) |
| EPS → SVG | Requires Inkscape or Illustrator | Not automated in this workflow |
| PDF → PNG | Use `transform-image` skill | Extract at high DPI (300+) |
| SVG → ICO | Use `transform-image` skill | Generate multi-resolution favicon |

For any format conversion, use the `transform-image` skill in the images plugin.
