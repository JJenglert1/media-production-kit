# Image Transformation Iteration Mappings

When the user requests changes after the initial transformation, map their request to the appropriate action.

## Dimension Changes

| User Says | Action |
|-----------|--------|
| "make it bigger" / "larger" | Increase target dimensions (ask for specific size or use 2x) |
| "make it smaller" / "shrink it" | Decrease target dimensions (ask for specific size or use 0.5x) |
| "make it square" | Crop or pad to 1:1 aspect ratio (ask: contain or cover?) |
| "make it wider" / "landscape" | Adjust to 16:9 or specified ratio (cover mode) |
| "make it taller" / "portrait" | Adjust to 9:16 or specified ratio (cover mode) |
| "exact 500x300" | Resize to exact dimensions (ask about fit mode if aspect ratio differs) |
| "half size" / "50%" | Scale by factor: `(w//2, h//2)` |
| "double it" / "2x" | Scale by factor: `(w*2, h*2)`, warn if upscaling from small source |

## Format Changes

| User Says | Action |
|-----------|--------|
| "convert to PNG" | Re-export as PNG (lossless, preserves transparency) |
| "convert to JPG" | Convert to RGB + save as JPG (warn about transparency loss) |
| "convert to WebP" | Re-export as WebP (ask: lossless or lossy?) |
| "make it a favicon" / "ICO" | Save as ICO with standard sizes (16, 32, 48, 64, 128, 256) |
| "optimize for web" | Convert to WebP lossy (quality 85) or optimize PNG |
| "make it smaller file size" | Reduce quality/compression, or convert to more efficient format |

## Quality & Compression

| User Says | Action |
|-----------|--------|
| "too large" / "file is too big" | Reduce quality (JPG: lower quality param; PNG: use WebP instead) |
| "too blurry" / "not sharp enough" | Increase quality, apply `ImageEnhance.Sharpness(1.5)`, or use higher-res source |
| "more compression" | Lower quality param (JPG: -10, WebP: -15) |
| "less compression" / "higher quality" | Raise quality param or switch to lossless |
| "lossless" | Switch to PNG or WebP lossless mode |

## Crop & Padding

| User Says | Action |
|-----------|--------|
| "crop the edges" / "trim" | Auto-crop: detect and remove uniform borders/whitespace |
| "crop to center" | Use cover mode with target dimensions |
| "add padding" / "add border" | Use contain mode with specific background color |
| "white background" | Pad with `(255, 255, 255)` or composite RGBA onto white |
| "transparent background" | Pad with `(255, 255, 255, 0)`, export as PNG/WebP |
| "black background" | Pad with `(0, 0, 0)` |
| "remove whitespace" | Auto-trim: `img.crop(img.getbbox())` |
| "center it" | Re-pad with equal margins using contain mode |

## Enhancement

| User Says | Action |
|-----------|--------|
| "sharpen" / "make it crisp" | `ImageEnhance.Sharpness(img).enhance(1.5)` (1.0 = original, 2.0 = very sharp) |
| "soften" / "blur slightly" | `ImageEnhance.Sharpness(img).enhance(0.7)` or `img.filter(ImageFilter.GaussianBlur(1))` |
| "brighter" | `ImageEnhance.Brightness(img).enhance(1.2)` |
| "darker" | `ImageEnhance.Brightness(img).enhance(0.8)` |
| "more contrast" | `ImageEnhance.Contrast(img).enhance(1.3)` |
| "less contrast" / "flatten" | `ImageEnhance.Contrast(img).enhance(0.8)` |
| "more saturated" / "vivid" | `ImageEnhance.Color(img).enhance(1.4)` |
| "desaturate" / "muted" | `ImageEnhance.Color(img).enhance(0.6)` |
| "grayscale" / "black and white" | `img.convert("L")` then back to `RGB` if needed |

## Batch & Preset

| User Says | Action |
|-----------|--------|
| "social media kit" | Generate all platform sizes (see Social Media Presets in SKILL.md) |
| "favicon set" | Generate ICO + individual PNGs (16, 32, 48, 180, 512) |
| "retina versions" | Generate 1x and 2x versions |
| "also do [another file]" | Add file to batch, re-run script |
| "same but for all images in [folder]" | Glob for images, batch process all |
| "undo" / "go back" | Re-run with original source (source is never overwritten) |

## Fit Mode Changes

| User Says | Action |
|-----------|--------|
| "don't crop" / "show the whole image" | Switch to contain mode |
| "fill it completely" / "no borders" | Switch to cover mode |
| "don't distort" / "keep proportions" | Switch from stretch to contain or fit |
| "stretch to fit" | Switch to stretch mode (warn about distortion) |
| "crop from top" / "crop from left" | Adjust cover mode anchor point (not centered) |
