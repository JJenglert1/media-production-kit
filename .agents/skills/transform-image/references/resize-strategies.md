# Resize Strategies & Resampling Algorithms

Reference for image resizing techniques — algorithm selection, fit modes, and edge case handling.

## Resampling Algorithms

### Pillow Resampling Filters

| Filter | Quality | Speed | Best For |
|--------|---------|-------|----------|
| `Image.LANCZOS` | Highest | Slowest | Downscaling (always use for reduction) |
| `Image.BICUBIC` | High | Medium | Upscaling (smooth enlargement) |
| `Image.BILINEAR` | Good | Fast | Quick previews, non-critical resizes |
| `Image.NEAREST` | Lowest | Fastest | Pixel art, sharp edge preservation |
| `Image.BOX` | Good | Fast | Exact integer downscaling (e.g., 2x, 4x) |
| `Image.HAMMING` | Good | Fast | Similar to BILINEAR, slightly sharper |

### Selection Rules

```
Downscaling (making smaller):
  └── Always use LANCZOS
      Exception: pixel art → use NEAREST

Upscaling (making larger):
  └── Use BICUBIC
      Exception: pixel art → use NEAREST
      Warning: >4x upscale → quality degrades significantly

Same size (format conversion only):
  └── No resampling needed, just re-export
```

### LANCZOS vs BICUBIC

- LANCZOS (a.k.a. Lanczos3): Uses a sinc-based windowed function with 3-lobe kernel. Best for downscaling because it handles anti-aliasing well, preventing moiré patterns and aliasing artifacts. Slightly over-sharpens upscaled images.
- BICUBIC: Uses cubic interpolation. Produces smooth results for upscaling without the ringing artifacts that Lanczos can introduce when enlarging.

## Fit Modes

### Contain (Fit Within + Pad)
Resize to fit entirely within target dimensions, preserve aspect ratio, pad remaining space.

```
Source: 1000×500 (2:1)
Target: 800×800 (1:1)
Result: 800×400 image centered in 800×800 canvas

┌──────────────────┐
│     padding      │
│ ┌──────────────┐ │
│ │   800×400    │ │
│ │   (image)    │ │
│ └──────────────┘ │
│     padding      │
└──────────────────┘
```

Use when: You need exact output dimensions AND the full image must be visible (logos, icons, profile pictures).

```python
def resize_contain(img, target_w, target_h, bg_color=(255, 255, 255, 0)):
    img_copy = img.copy()
    img_copy.thumbnail((target_w, target_h), Image.LANCZOS)
    result = Image.new("RGBA", (target_w, target_h), bg_color)
    offset = ((target_w - img_copy.size[0]) // 2, (target_h - img_copy.size[1]) // 2)
    result.paste(img_copy, offset, img_copy if img_copy.mode == "RGBA" else None)
    return result
```

### Cover (Fill + Crop)
Resize to fill target dimensions completely, crop overflow from center.

```
Source: 1000×500 (2:1)
Target: 800×800 (1:1)
Result: Center 800×800 region from 1600×800 upscale

     ┌─────────────────────┐
     │  ┌───────────┐      │
     │  │  800×800  │      │
     │  │ (visible) │      │
     │  └───────────┘      │
     └─────────────────────┘
      cropped    cropped
```

Use when: You need exact output dimensions AND edge cropping is acceptable (social media posts, headers, OG images).

```python
def resize_cover(img, target_w, target_h):
    ratio = max(target_w / img.size[0], target_h / img.size[1])
    new_size = (round(img.size[0] * ratio), round(img.size[1] * ratio))
    img = img.resize(new_size, Image.LANCZOS)
    left = (img.size[0] - target_w) // 2
    top = (img.size[1] - target_h) // 2
    return img.crop((left, top, left + target_w, top + target_h))
```

### Fit (Fit Within, No Pad)
Resize to fit within target dimensions, no padding. Output may be smaller than target.

```
Source: 1000×500 (2:1)
Target: 800×800 (1:1)
Result: 800×400 (preserves aspect ratio, no padding)

┌──────────────┐
│   800×400    │
│   (image)    │
└──────────────┘
```

Use when: You want to limit maximum dimensions but don't need exact output size (web optimization, file size reduction).

```python
def resize_fit(img, max_w, max_h):
    img_copy = img.copy()
    img_copy.thumbnail((max_w, max_h), Image.LANCZOS)
    return img_copy
```

### Stretch (Force Exact)
Force image to exact target dimensions, ignoring aspect ratio.

Use when: Almost never. Only for UI elements that must be exact size regardless of distortion.

```python
def resize_stretch(img, target_w, target_h):
    return img.resize((target_w, target_h), Image.LANCZOS)
```

## Edge Cases

### Very Small Sources (< 64px)
- Warn user about quality: "Source image is only {w}×{h} — upscaling to {target} will result in visible pixelation"
- Use `Image.BICUBIC` for smoothest upscale
- Consider suggesting the user find a higher-resolution source

### Very Large Sources (> 8000px)
- Process in stages to manage memory: thumbnail first, then fine-tune
- Consider `Image.thumbnail()` which modifies in-place (memory efficient)

### Non-Square Aspect Ratios for Square Targets
- Default to contain mode (show full image, pad edges)
- For profile pictures: use cover (crop to fill, center the subject)
- Ask user if auto-detection is ambiguous

### Transparency on JPG Export
- JPG does not support transparency
- Composite onto background before saving:
  ```python
  if img.mode == "RGBA":
      bg = Image.new("RGB", img.size, bg_color)  # default: white
      bg.paste(img, mask=img.split()[3])
      img = bg
  ```
- Always warn user when transparency will be lost

### Animated Images (GIF, APNG)
- Pillow can read frames but processing is complex
- For simple operations, process each frame individually
- Consider suggesting `ffmpeg` for animated image transformations

### ICC Color Profiles
- Preserve ICC profiles when possible:
  ```python
  icc_profile = img.info.get("icc_profile")
  img.save("output.jpg", "JPEG", icc_profile=icc_profile, quality=85)
  ```
- Especially important for photos where color accuracy matters

## Batch Processing Pattern

For generating multiple sizes from one source:

```python
SIZES = [
    ("instagram_post", 1080, 1080, "contain"),
    ("og_image", 1200, 630, "cover"),
    ("twitter_header", 1500, 500, "cover"),
    ("favicon", 32, 32, "contain"),
]

img = Image.open(SOURCE)

for name, w, h, mode in SIZES:
    if mode == "contain":
        result = resize_contain(img, w, h)
    elif mode == "cover":
        result = resize_cover(img, w, h)

    output_path = OUTPUT_DIR / f"{stem}_{name}.png"
    result.save(output_path, "PNG", optimize=True)
    print(f"✅ {output_path.name} — {w}×{h}")
```
