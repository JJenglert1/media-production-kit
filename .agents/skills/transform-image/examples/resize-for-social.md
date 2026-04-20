# Example: /transform-image social media kit from stripe-assets/logo/stripe-logo.png

## Request Parsing
- Source: `stripe-assets/logo/stripe-logo.png` (512×512, PNG, RGBA)
- Operation: Batch resize — social media kit
- Preset: All major platform sizes
- Fit mode: Contain (show full logo, pad with transparent background)

## Transformation Plan

```
Batch Plan: Social Media Kit
━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Source: stripe-assets/logo/stripe-logo.png (512×512, PNG)

Outputs:
  1. stripe-logo_1080x1080.png  — Instagram post (contain, transparent)
  2. stripe-logo_1200x630.png   — Open Graph / Facebook (contain, white bg)
  3. stripe-logo_1500x500.png   — Twitter/X header (contain, white bg)
  4. stripe-logo_800x800.png    — Discord avatar (contain, transparent)
  5. stripe-logo_400x400.png    — Profile picture (contain, transparent)
  6. stripe-logo_180x180.png    — Apple touch icon (contain, white bg)
  7. stripe-logo_32x32.png      — Favicon (contain)
  8. stripe-logo_16x16.png      — Favicon small (contain)
  9. stripe-logo.ico            — Multi-res favicon (16-256px)
```

## Generated Script

```python
from PIL import Image
from pathlib import Path

SOURCE = "stripe-assets/logo/stripe-logo.png"
OUTPUT_DIR = Path("stripe-assets/social-kit")
OUTPUT_DIR.mkdir(parents=True, exist_ok=True)

def resize_contain(img, target_w, target_h, bg_color=(255, 255, 255, 0)):
    img_copy = img.copy()
    img_copy.thumbnail((target_w, target_h), Image.LANCZOS)
    result = Image.new("RGBA", (target_w, target_h), bg_color)
    offset = ((target_w - img_copy.size[0]) // 2, (target_h - img_copy.size[1]) // 2)
    result.paste(img_copy, offset, img_copy if img_copy.mode == "RGBA" else None)
    return result

img = Image.open(SOURCE)
if img.mode in ("P", "PA"):
    img = img.convert("RGBA")

SIZES = [
    ("1080x1080", 1080, 1080, (255, 255, 255, 0)),    # Instagram
    ("1200x630",  1200, 630,  (255, 255, 255, 255)),   # OG / Facebook
    ("1500x500",  1500, 500,  (255, 255, 255, 255)),   # Twitter header
    ("800x800",   800,  800,  (255, 255, 255, 0)),     # Discord
    ("400x400",   400,  400,  (255, 255, 255, 0)),     # Profile pic
    ("180x180",   180,  180,  (255, 255, 255, 255)),   # Apple touch
    ("32x32",     32,   32,   (255, 255, 255, 0)),     # Favicon
    ("16x16",     16,   16,   (255, 255, 255, 0)),     # Favicon small
]

for name, w, h, bg in SIZES:
    result = resize_contain(img, w, h, bg)
    path = OUTPUT_DIR / f"stripe-logo_{name}.png"
    result.save(path, "PNG", optimize=True)
    size_kb = path.stat().st_size / 1024
    print(f"✅ stripe-logo_{name}.png — {w}×{h}, {size_kb:.1f}KB")

# Multi-res favicon
icon_img = resize_contain(img, 256, 256)
icon_path = OUTPUT_DIR / "stripe-logo.ico"
icon_img.save(icon_path, "ICO", sizes=[(16,16), (32,32), (48,48), (64,64), (128,128), (256,256)])
print(f"✅ stripe-logo.ico — multi-res (16-256px)")
```

## Execution

```bash
uv run --with Pillow python3 stripe-assets/social-kit/transform.py
```

## Output

```
Transformation Complete: Social Media Kit
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Source: stripe-assets/logo/stripe-logo.png (512×512)

Outputs:
  ✅ stripe-logo_1080x1080.png — 1080×1080, PNG, 45.2KB (Instagram)
  ✅ stripe-logo_1200x630.png  — 1200×630, PNG, 28.7KB (OG/Facebook)
  ✅ stripe-logo_1500x500.png  — 1500×500, PNG, 22.1KB (Twitter header)
  ✅ stripe-logo_800x800.png   — 800×800, PNG, 31.4KB (Discord)
  ✅ stripe-logo_400x400.png   — 400×400, PNG, 12.8KB (Profile pic)
  ✅ stripe-logo_180x180.png   — 180×180, PNG, 5.6KB (Apple touch)
  ✅ stripe-logo_32x32.png     — 32×32, PNG, 1.2KB (Favicon)
  ✅ stripe-logo_16x16.png     — 16×16, PNG, 0.8KB (Favicon small)
  ✅ stripe-logo.ico           — multi-res ICO (16-256px), 67.3KB

All files saved to: stripe-assets/social-kit/

Note: 1080x1080 and 800x800 were upscaled from 512×512 source — quality is acceptable
but a higher-resolution source would produce sharper results.
```
