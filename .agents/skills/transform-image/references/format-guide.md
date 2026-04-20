# Image Format Guide

Detailed reference for image formats — when to use each, compression characteristics, and Pillow export parameters.

## Format Deep Dive

### PNG (Portable Network Graphics)
- Type: Raster, lossless compression
- Transparency: Full alpha channel (256 levels)
- Best for: Logos, icons, screenshots, graphics with sharp edges or text
- Not for: Photographs (file size too large)
- Max colors: 16.7M (24-bit) or 280T (48-bit)
- Pillow export:
  ```python
  img.save("output.png", "PNG", optimize=True)
  # optimize=True: enables Zlib compression optimization
  ```
- Typical sizes: 10KB-5MB for logos, 500KB-20MB for screenshots

### JPG / JPEG (Joint Photographic Experts Group)
- Type: Raster, lossy compression
- Transparency: None (no alpha channel)
- Best for: Photographs, complex imagery with gradients
- Not for: Logos (compression artifacts on sharp edges), images needing transparency
- Quality range: 1-100 (75-90 recommended for web)
- Pillow export:
  ```python
  # Must convert to RGB first (no RGBA support)
  if img.mode == "RGBA":
      bg = Image.new("RGB", img.size, (255, 255, 255))
      bg.paste(img, mask=img.split()[3])
      img = bg
  img.save("output.jpg", "JPEG", quality=85, optimize=True, progressive=True)
  # quality: 1-100 (85 is good balance)
  # optimize: smaller file via Huffman optimization
  # progressive: loads blurry-to-sharp (better UX)
  ```
- Quality guidelines:
  - 95-100: Near-lossless, large files (print)
  - 85-90: High quality, reasonable size (web hero images)
  - 75-80: Good quality, smaller files (content images)
  - 60-70: Acceptable quality, small files (thumbnails)
  - Below 60: Noticeable artifacts

### WebP
- Type: Raster, both lossy and lossless modes
- Transparency: Full alpha channel
- Best for: Web-optimized images (25-35% smaller than PNG/JPG)
- Not for: Print, legacy browser support (IE11)
- Pillow export:
  ```python
  # Lossless (for logos, icons)
  img.save("output.webp", "WEBP", lossless=True)

  # Lossy (for photos)
  img.save("output.webp", "WEBP", quality=85, method=6)
  # method: 0 (fast) to 6 (slow but smaller)
  ```
- Size savings vs PNG: ~25-35% smaller (lossless), ~60-80% smaller (lossy)

### AVIF (AV1 Image File Format)
- Type: Raster, lossy and lossless
- Transparency: Full alpha channel
- Best for: Next-gen web optimization (better compression than WebP)
- Not for: Broad compatibility (newer format)
- Pillow export: Requires `pillow-avif-plugin` or Pillow 10+
  ```python
  img.save("output.avif", "AVIF", quality=75)
  ```
- Size savings vs WebP: ~20% smaller at same quality

### ICO (Windows Icon)
- Type: Container format (multiple raster sizes)
- Transparency: Full alpha channel
- Best for: Favicons, Windows application icons
- Standard sizes: 16, 32, 48, 64, 128, 256
- Pillow export:
  ```python
  img.save("output.ico", "ICO",
           sizes=[(16,16), (32,32), (48,48), (64,64), (128,128), (256,256)])
  ```

### SVG (Scalable Vector Graphics)
- Type: Vector (XML-based)
- Transparency: Yes (via fill-opacity or CSS)
- Best for: Logos, icons, illustrations that need to scale infinitely
- Not for: Photographs, complex imagery
- Pillow support: Cannot read/write SVG natively
- Conversion: Use `cairosvg` to convert SVG → PNG at any resolution:
  ```python
  import cairosvg
  cairosvg.svg2png(url="input.svg", write_to="output.png",
                   output_width=1024, output_height=1024)
  ```

## Format Selection Decision Tree

```
Is it a photograph or complex image?
├── Yes → JPG (lossy, quality 85)
│         └── Need transparency? → WebP (lossy)
│         └── Need smallest file? → AVIF
└── No (logo, icon, graphic) →
    ├── Need infinite scaling? → Keep as SVG
    ├── Need multi-size favicon? → ICO
    ├── For web? → WebP (lossless) or PNG
    └── General use? → PNG (lossless, universal)
```

## Color Mode Reference

| Mode | Channels | Bits/Pixel | Use Case |
|------|----------|-----------|----------|
| `RGB` | 3 (Red, Green, Blue) | 24 | Photos, JPG output |
| `RGBA` | 4 (RGB + Alpha) | 32 | Transparent images, PNG/WebP |
| `L` | 1 (Luminance) | 8 | Grayscale |
| `LA` | 2 (Luminance + Alpha) | 16 | Grayscale with transparency |
| `P` | 1 (Palette index) | 8 | Indexed color (GIF, some PNG) |
| `CMYK` | 4 (Cyan, Magenta, Yellow, Key) | 32 | Print (convert to RGB for web) |
| `1` | 1 (Binary) | 1 | Black and white |
