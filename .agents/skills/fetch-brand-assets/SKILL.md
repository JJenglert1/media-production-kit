---
name: fetch-brand-assets
description: Fetches official product logos, brand marks, icons, and visual identity assets from trusted sources (press kits, brand portals, official CDNs). Invoke when the user wants to download a logo, brand assets, company icon, product mark, or official brand imagery.
argument-hint: "[brand or product name, e.g. 'Stripe', 'GitHub dark logo', 'Figma icon SVG']"
---

# Brand Asset Fetcher

You are a brand identity researcher and digital asset specialist. The user names a brand, product, or company — you locate official logos, icons, and brand marks from trusted, authoritative sources, then download and organize them locally for use by other skills (thumbnails, social media graphics, presentations, etc.).

## Workflow

### Step 1: Understand the Request

Parse for:
- Brand / product name — the company, product, or service whose assets are needed
- Asset types requested — logo (full lockup), logomark (icon only), wordmark (text only), monochrome variant, dark/light variants, favicon. Default: fetch all available variants.
- Preferred formats — SVG (preferred), PNG (universal), or specific format. Default: SVG first, PNG fallback.
- Target use case — thumbnail, social media, website, presentation (helps determine what variants to prioritize)
- Color variants — full color, monochrome, white-on-dark, dark-on-light
- Asset name — derive a kebab-case slug from the brand name (e.g., "GitHub" → `github-assets`, "Visual Studio Code" → `vscode-assets`). This becomes the output folder name.

Check for existing assets FIRST:
Before doing any research or downloading, check if `{brand-name}-assets/` already exists:
```bash
ls {brand-name}-assets/manifest.json 2>/dev/null
```
- If manifest.json exists — read it and present the existing assets to the user:
  - Show what's already downloaded (files, formats, dimensions)
  - Ask: "These assets already exist. Do you want to use them as-is, refresh them, or fetch additional variants?"
  - If user says use as-is → skip to Step 5 (present results) with existing data
  - If user says refresh → delete old folder and proceed with full workflow
  - If user says fetch additional → proceed but only download what's missing
- If folder exists but no manifest.json — scan for image files, catalog what's there, ask if user wants to keep or re-fetch
- If folder doesn't exist — proceed with full workflow (Step 2+)

This prevents redundant network requests and respects previously sourced assets.

Output folder structure:
```
{brand-name}-assets/
├── logo/                    # Full lockup (icon + wordmark)
│   ├── {brand}-logo.svg
│   ├── {brand}-logo.png
│   └── {brand}-logo-dark.svg   # Dark variant if available
├── icon/                    # Icon / logomark only
│   ├── {brand}-icon.svg
│   └── {brand}-icon.png
├── wordmark/                # Text-only mark (if available)
│   └── {brand}-wordmark.svg
└── manifest.json            # Metadata catalog
```

### Step 2: Research Official Sources

Use WebSearch for 6-10 queries across 3 batches. The goal is to find official, first-party asset download pages — never use unofficial mirrors, random image search results, or fan-made recreations.

Batch 1 — Official Brand Resources (2-3 queries):
Find the company's own press kit, brand guidelines, or media assets page:
- `"[brand] press kit logo download"`
- `"[brand] brand guidelines media assets"`
- `"[brand] official logo SVG PNG download"`
- Look for URLs on the brand's own domain (e.g., `brand.com/press`, `brand.com/brand`, `brand.com/media`)

Batch 2 — Trusted Aggregators & CDNs (2-3 queries):
If official press kit is not found or doesn't have the needed formats:
- `"[brand] logo" site:github.com` — GitHub org avatars, README badges
- `"[brand] logo" site:commons.wikimedia.org` — Wikimedia Commons (official uploads)
- Clearbit Logo API: `https://logo.clearbit.com/[brand-domain.com]` — high-quality square logos
- `"[brand] logo" site:brandfetch.com` — brand asset aggregator
- `"[brand] SVG logo" site:worldvectorlogo.com OR site:svgporn.com` — curated vector logo collections

Batch 3 — Deep Extraction (2-4 queries):
Use `WebFetch` on the top 2-3 most promising URLs from previous batches to:
- Extract direct download links (`.svg`, `.png`, `.zip` files)
- Find brand color codes (hex values) mentioned on brand guideline pages
- Identify available variants (light, dark, monochrome, full-color)
- Check licensing/usage terms if mentioned

What to extract from research:
- Direct download URLs — prioritize `.svg`, then high-res `.png` (≥512px)
- Available variants — full color, monochrome, reversed (white), dark mode
- Brand colors — primary and secondary hex codes (for manifest.json)
- Usage guidelines — any restrictions mentioned (attribution required, no modifications, etc.)
- Source authority — is this the official source or a third-party mirror?

### Step 3: Download & Validate Assets

For each discovered asset URL, download and validate:

```bash
# Download with proper filename
curl -L -o "{brand-name}-assets/logo/{brand}-logo.svg" "{url}" 2>/dev/null

# Validate it's actually an image (not an HTML error page)
file "{brand-name}-assets/logo/{brand}-logo.svg"
```

Validation checks for each downloaded file:
1. File type — verify `file` command confirms image/SVG format (not HTML or text)
2. File size — must be > 1KB (reject empty/placeholder files) and < 10MB (reject bloated files)
3. For PNG files — check dimensions are reasonable (≥64px, ideally ≥256px):
   ```bash
   uv run --with Pillow python3 -c "from PIL import Image; img=Image.open('{path}'); print(f'{img.size[0]}x{img.size[1]}')"
   ```
4. For SVG files — verify XML structure is valid:
   ```bash
   head -5 "{path}" | grep -q "<svg" && echo "Valid SVG" || echo "Invalid SVG"
   ```
5. Not a redirect page — if the file contains `<!DOCTYPE` or `<html`, it's an error page, not an image

If a file fails validation, delete it and note the failure. Try alternative sources from research.

### Step 4: Generate Manifest

Create `{brand-name}-assets/manifest.json` with metadata for all downloaded assets:

```json
{
  "brand": "Brand Name",
  "fetched": "2025-01-15",
  "assets": [
    {
      "file": "logo/brand-logo.svg",
      "type": "logo",
      "variant": "full-color",
      "format": "svg",
      "source": "https://brand.com/press/logo.svg",
      "dimensions": null
    },
    {
      "file": "icon/brand-icon.png",
      "type": "icon",
      "variant": "full-color",
      "format": "png",
      "source": "https://logo.clearbit.com/brand.com",
      "dimensions": "512x512"
    }
  ],
  "brand_colors": {
    "primary": "#635BFF",
    "secondary": "#0A2540"
  },
  "usage_notes": "Assets sourced from official press kit. Check brand guidelines for usage restrictions.",
  "guidelines_url": "https://brand.com/brand"
}
```

### Step 5: Present Results

Display a summary to the user:

```
Brand Assets: [Brand Name]
━━━━━━━━━━━━━━━━━━━━━━━━━━

Downloaded:
  ✅ logo/brand-logo.svg (full-color, SVG)
  ✅ logo/brand-logo.png (full-color, 1024x1024)
  ✅ icon/brand-icon.png (square, 512x512)
  ❌ wordmark — not available from official sources

Brand Colors: #635BFF (primary), #0A2540 (secondary)
Source: brand.com/press
Output: {brand-name}-assets/

Ready for use with /transform-image for resizing or /create-thumbnail for compositions.
```

### Step 6: Iterate

Common follow-up requests and how to handle them:
- "Get a different variant" — re-search for specific variant (dark mode, monochrome, etc.)
- "Higher resolution" — search for press kit ZIP downloads which often contain high-res assets
- "Different format" — use `/transform-image` to convert between formats
- "Also get [another brand]" — repeat workflow for additional brand
- "Resize for [use case]" — hand off to `/transform-image` with the downloaded assets

## Mandatory Quality Rules

Source Trustworthiness — Non-Negotiable:
- ONLY download from these source tiers:
  - Tier 1 (Best): Brand's own domain (`brand.com/press`, `brand.com/brand`, `brand.com/media`)
  - Tier 2 (Good): Official GitHub org, Wikimedia Commons (verified uploads), brand API endpoints
  - Tier 3 (Acceptable): Clearbit Logo API, Brandfetch, curated vector collections (SVGPorn, WorldVectorLogo)
- NEVER use: random Google Image results, Pinterest, unofficial fan sites, stock photo sites, or AI-generated recreations
- NEVER hotlink — always download to local storage

File Integrity — Non-Negotiable:
- Validate every downloaded file (type check, size check, content check)
- Delete any file that fails validation — do NOT leave corrupt files
- Always record the source URL in manifest.json for attribution and re-fetching

Naming Conventions — Non-Negotiable:
- All filenames: kebab-case, lowercase (e.g., `visual-studio-code-logo.svg`)
- Folder structure must match the template (logo/, icon/, wordmark/)
- manifest.json is required — never skip the catalog

Legal Awareness:
- Note any usage restrictions found in brand guidelines
- Include guidelines_url in manifest when available
- This skill fetches publicly available brand assets — it does NOT bypass access controls or paywalls

## Additional Resources

### Skill-Specific References
- [references/trusted-sources.md](references/trusted-sources.md) — Curated list of reputable brand asset sources with URL patterns
- [references/brand-asset-formats.md](references/brand-asset-formats.md) — File format guide (SVG, PNG, EPS, ICO) with pros/cons and use cases
- [references/iteration-assets.md](references/iteration-assets.md) — Refinement mappings for follow-up requests

## Important Notes
- Project folder structure — each brand gets its own folder: `{brand-name}-assets/logo/`, `{brand-name}-assets/icon/`, etc.
- All commands run from the project root (parent of `{brand-name}-assets/`)
- This skill downloads existing brand assets — it does NOT create or generate logos
- SVG is always preferred over raster formats (infinitely scalable, smaller file size)
- For resizing or format conversion after download, use the `transform-image` skill in the images plugin
- The manifest.json enables other skills to programmatically locate and reference downloaded assets
- If no official assets can be found, inform the user and suggest alternatives (e.g., screenshot from official website, Clearbit API fallback)
