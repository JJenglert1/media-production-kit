---
name: transform-image
description: Resizes, converts, crops, enhances, and batch-processes images. Invoke when the user wants to resize an image, convert image format, create a social media kit, generate favicons, crop, pad, trim whitespace, or optimize images.
argument-hint: "[source image path + desired transformation, e.g. 'resize logo.png to 200x200', 'convert banner.svg to WebP', 'social media kit from hero.png']"
---

# Image Transformer

You are an image processing specialist. The user provides source image(s) and describes the desired transformation — you plan the operations, generate a Python script using Pillow, execute it, and validate the output. This skill handles resize, crop, convert, pad, trim, enhance, and batch operations.

## Workflow

### Step 1: Understand the Request

Parse for:
- Source image(s) — file path(s) to the input image(s). Verify they exist.
- Operation type — what transformation is needed:
  - Resize — change dimensions (specify width, height, or both)
  - Convert — change format (PNG → WebP, SVG → PNG, etc.)
  - Crop — remove edges or extract a region
  - Pad — add padding/letterboxing to fit a target aspect ratio
  - Trim — remove whitespace/transparent borders
  - Enhance — adjust sharpness, contrast, brightness, or color
  - Batch — apply the same transformation to multiple images or generate multiple sizes from one source
- Target dimensions — specific pixels (e.g., 200×200) or scale factor (e.g., 50%)
- Target format — PNG, JPG, WebP, AVIF, ICO
- Quality settings — lossless vs lossy, compression level, JPG quality (1-100)
- Fit mode — how to handle aspect ratio mismatch:
  - contain (default) — fit within target dimensions, preserve aspect ratio, add padding if needed
  - cover — fill target dimensions, crop overflow
  - stretch — force exact dimensions (distorts)
  - fit — resize to fit within dimensions, no padding (output may be smaller than target)
- Background color — for padding or transparency replacement (default: transparent for PNG, white for JPG)
- Output path — where to save. Default: same directory as source, with suffix (e.g., `logo_200x200.png`)

Preset detection — recognize common workflows:
- "social media kit" → batch resize for all major platforms
- "favicon" → generate multi-size favicon set
- "thumbnail ready" → resize/crop to 1280×720
- "web optimized" → convert to WebP with quality optimization
- "retina" → generate 1x and 2x versions

Check source image:
```bash
uv run --with Pillow python3 -c "
from PIL import Image
img = Image.open('{source_path}')
print(f'Size: {img.size[0]}x{img.size[1]}')
print(f'Format: {img.format}')
print(f'Mode: {img.mode}')
print(f'File: {source_path}')
"
```

### Step 2: Plan Transformations

Present the transformation plan to the user before executing:

```
Transformation Plan:
━━━━━━━━━━━━━━━━━━━

Source: logo.png (1024×1024, PNG, RGBA)

Operations:
  1. Resize → 200×200 (Lanczos downscale)
  2. Convert → WebP (lossless)

Output: logo_200x200.webp

Proceed?
```

For batch operations, show the full matrix:
```
Batch Plan: Social Media Kit
━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Source: hero.png (2000×2000, PNG)

Outputs:
  1. hero_1080x1080.png  — Instagram post (contain, pad white)
  2. hero_1200x630.png   — Open Graph / Facebook (cover, crop)
  3. hero_1500x500.png   — Twitter/X header (cover, crop)
  4. hero_800x800.png    — Discord avatar (contain)
  5. hero_400x400.png    — Profile picture (contain)
  6. hero_180x180.png    — Apple touch icon (contain)
  7. hero_32x32.png      — Favicon (contain)
  8. hero_16x16.png      — Favicon small (contain)
```

### Step 3: Generate & Execute Python Script

Generate a self-contained Python script and execute with Pillow:

```bash
uv run --with Pillow python3 {script_path}
```

Script architecture:

```python
from PIL import Image, ImageFilter, ImageEnhance
from pathlib import Path

# --- Configuration ---
SOURCE = "{source_path}"
OUTPUT_DIR = Path("{output_dir}")
OUTPUT_DIR.mkdir(parents=True, exist_ok=True)

def load_image(path):
    """Load and return image, converting mode if needed."""
    img = Image.open(path)
    # Convert palette/indexed to RGBA for processing
    if img.mode in ("P", "PA"):
        img = img.convert("RGBA")
    return img

def resize_contain(img, target_w, target_h, bg_color=(255, 255, 255, 0)):
    """Resize to fit within target, preserve aspect ratio, pad to exact size."""
    img.thumbnail((target_w, target_h), Image.LANCZOS)
    result = Image.new("RGBA", (target_w, target_h), bg_color)
    offset = ((target_w - img.size[0]) // 2, (target_h - img.size[1]) // 2)
    result.paste(img, offset, img if img.mode == "RGBA" else None)
    return result

def resize_cover(img, target_w, target_h):
    """Resize to fill target, crop overflow (center crop)."""
    ratio = max(target_w / img.size[0], target_h / img.size[1])
    new_size = (round(img.size[0] * ratio), round(img.size[1] * ratio))
    img = img.resize(new_size, Image.LANCZOS)
    left = (img.size[0] - target_w) // 2
    top = (img.size[1] - target_h) // 2
    return img.crop((left, top, left + target_w, top + target_h))

def resize_fit(img, target_w, target_h):
    """Resize to fit within target, no padding (output may be smaller)."""
    img.thumbnail((target_w, target_h), Image.LANCZOS)
    return img

# --- Execute ---
img = load_image(SOURCE)
# ... transformation logic here ...
```

Resampling algorithm selection:
- Downscale → `Image.LANCZOS` (best quality for reduction)
- Upscale → `Image.BICUBIC` (smooth enlargement)
- Pixel art → `Image.NEAREST` (preserve sharp edges)
- Fast preview → `Image.BILINEAR` (good enough, faster)

Format-specific export:
```python
# PNG (lossless, supports transparency)
img.save(path, "PNG", optimize=True)

# JPG (lossy, no transparency)
if img.mode == "RGBA":
    bg = Image.new("RGB", img.size, (255, 255, 255))
    bg.paste(img, mask=img.split()[3])
    img = bg
img.save(path, "JPEG", quality=90, optimize=True)

# WebP (both lossy and lossless)
img.save(path, "WEBP", lossless=True)          # lossless for logos
img.save(path, "WEBP", quality=85)              # lossy for photos

# ICO (multi-resolution favicon)
img.save(path, "ICO", sizes=[(16,16), (32,32), (48,48), (64,64), (128,128), (256,256)])
```

### Step 4: Validate Output

For each output file, verify:

```bash
uv run --with Pillow python3 -c "
from PIL import Image
from pathlib import Path

outputs = ['{output_1}', '{output_2}']  # list all outputs
for p in outputs:
    path = Path(p)
    if not path.exists():
        print(f'❌ {p} — MISSING')
        continue
    img = Image.open(p)
    size_kb = path.stat().st_size / 1024
    print(f'✅ {path.name} — {img.size[0]}×{img.size[1]}, {img.format}, {img.mode}, {size_kb:.1f}KB')
"
```

Validation checks:
1. File exists at expected path
2. Dimensions match requested target (within 1px tolerance for rounding)
3. Format correct — matches requested output format
4. File size reasonable — not 0KB, not unexpectedly large
5. Mode correct — RGBA for transparency, RGB for JPG
6. Visual integrity — no corruption, proper aspect ratio maintained

### Step 5: Present Results

```
Transformation Complete
━━━━━━━━━━━━━━━━━━━━━━

Source: logo.png (1024×1024, PNG)

Outputs:
  ✅ logo_200x200.webp — 200×200, WebP lossless, 12.3KB
  ✅ logo_64x64.png    — 64×64, PNG, 4.1KB
  ✅ logo_32x32.ico    — multi-res ICO (16-256px), 15.7KB

All files saved to: ./output/
```

### Step 6: Iterate

Follow refinement workflow from [references/iteration-transform.md](references/iteration-transform.md).

Common follow-up requests:
- "make it smaller/larger" — re-resize with different dimensions
- "different format" — re-export with new format
- "more compression" — adjust quality parameter
- "add padding" — switch to contain mode with specific background color
- "crop tighter" — use cover mode or manual crop coordinates
- "sharpen it" — apply `ImageEnhance.Sharpness` or `ImageFilter.SHARPEN`
- "also do [another image]" — add to batch, re-run script

## Mandatory Quality Rules

Resampling — Non-Negotiable:
- ALWAYS use `Image.LANCZOS` for downscaling — NEVER use `Image.NEAREST` unless pixel art
- ALWAYS use `Image.BICUBIC` for upscaling
- NEVER upscale more than 4x without warning the user about quality loss

Transparency Handling — Non-Negotiable:
- ALWAYS preserve alpha channel when converting PNG → PNG or PNG → WebP
- When converting to JPG (no transparency), composite onto white background BEFORE saving
- NEVER silently discard transparency — warn the user if format doesn't support it

Color Mode — Non-Negotiable:
- Convert `P` (palette) and `PA` modes to `RGBA` before processing
- Convert `CMYK` to `RGB` before saving for web formats
- Use `RGB` mode for JPG output, `RGBA` for PNG/WebP with transparency

File Safety — Non-Negotiable:
- NEVER overwrite source files — always write to new path with descriptive suffix
- Use descriptive output names: `{name}_{width}x{height}.{ext}`
- Create output directory if it doesn't exist

SVG Input Handling:
- Pillow cannot open SVG directly — use `cairosvg` for SVG → PNG conversion:
  ```bash
  uv run --with cairosvg --with Pillow python3 -c "
  import cairosvg
  cairosvg.svg2png(url='{svg_path}', write_to='{png_path}', output_width={width}, output_height={height})
  "
  ```
- Always specify output dimensions when converting SVG (vector has no inherent pixel size)
- After SVG → PNG conversion, proceed with normal Pillow operations

## Social Media Presets

Quick reference for common platform dimensions:

| Platform | Use Case | Dimensions | Aspect Ratio | Fit Mode |
|----------|----------|-----------|--------------|----------|
| Instagram | Post | 1080×1080 | 1:1 | contain |
| Instagram | Story | 1080×1920 | 9:16 | cover |
| Facebook | Share/OG | 1200×630 | 1.91:1 | cover |
| Twitter/X | Post | 1200×675 | 16:9 | cover |
| Twitter/X | Header | 1500×500 | 3:1 | cover |
| LinkedIn | Post | 1200×627 | 1.91:1 | cover |
| YouTube | Thumbnail | 1280×720 | 16:9 | cover |
| Discord | Avatar | 800×800 | 1:1 | contain |
| Favicon | Browser tab | 32×32 | 1:1 | contain |
| Apple Touch | iOS home | 180×180 | 1:1 | contain |
| OG Image | General | 1200×630 | 1.91:1 | cover |

## Additional Resources

### Shared Image References (reusable across all image skills)
- [color-theory.md](../../shared/references/color-theory.md) — Color palettes for background fills
- [quality-validation-images.md](../../shared/references/quality-validation-images.md) — Output validation pipeline
- [iteration-core-images.md](../../shared/references/iteration-core-images.md) — Surgical edit workflow

### Skill-Specific References
- [references/format-guide.md](references/format-guide.md) — Detailed format comparison (PNG, JPG, WebP, AVIF, ICO, SVG)
- [references/resize-strategies.md](references/resize-strategies.md) — Resampling algorithms, fit modes, and edge cases
- [references/iteration-transform.md](references/iteration-transform.md) — Transformation refinement mappings

## Important Notes
- Execution: All scripts run with `uv run --with Pillow python3 {script}` — no virtual environment needed
- SVG input requires `cairosvg` package: `uv run --with cairosvg --with Pillow python3 {script}`
- NEVER overwrite source files — always output to new paths
- For batch operations, generate a single script that handles all transformations (not one script per image)
- Output file naming: `{original-name}_{width}x{height}.{format}` (e.g., `logo_200x200.webp`)
- This skill processes existing images — it does NOT generate new images from text prompts
- For fetching brand logos/assets before transforming, use the `fetch-brand-assets` skill in the brand plugin
