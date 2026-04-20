# Trusted Brand Asset Sources

Curated list of reputable sources for official brand logos, icons, and visual identity assets. Sources are ranked by authority and reliability.

## Tier 1 — Official Brand Sources (Highest Authority)

These are first-party sources maintained by the brand itself. Always check these first.

### Common URL Patterns
Most companies publish brand assets at predictable paths on their own domain:

| Pattern | Example |
|---------|---------|
| `brand.com/press` | stripe.com/press |
| `brand.com/brand` | figma.com/brand |
| `brand.com/media` | spotify.com/media |
| `brand.com/about/press` | github.com/about/press |
| `brand.com/brand-guidelines` | uber.com/brand-guidelines |
| `brand.com/newsroom` | apple.com/newsroom |
| `brand.com/logos` | slack.com/media-kit |
| `brand.com/identity` | mozilla.org/identity |
| `brand.com/media-kit` | twitch.tv/p/press-center |

### What to Look For
- Press kit page — often has a "Download Assets" or "Media Kit" ZIP
- Brand guidelines PDF — sometimes embeds or links to logo files
- Developer/partner portal — API brands often provide logos for integration partners
- GitHub repository — some brands maintain a `brand-assets` or `press-kit` repo

## Tier 2 — Official Adjacent Sources (High Authority)

Verified sources closely associated with the brand.

### GitHub Organization Avatars
```
https://github.com/{org}.png?size=512
```
- Returns the organization's official avatar as a square PNG
- Sizes: append `?size=` with 64, 128, 256, 512
- Example: `https://github.com/stripe.png?size=512`

### Wikimedia Commons
```
https://commons.wikimedia.org/wiki/File:{Brand}_logo.svg
```
- Official logos uploaded under fair use or by brand representatives
- Check the file description for licensing info
- Prefer files categorized under the brand's official Wikimedia category
- Download the original file, not thumbnails

### NPM / Package Registry Avatars
- Some packages include official logos in their README or repo

## Tier 3 — Curated Aggregators (Acceptable)

Third-party sources that curate and verify brand assets.

### Clearbit Logo API
```
https://logo.clearbit.com/{domain}
```
- Returns a square logo (usually 128x128 or 256x256 PNG)
- Input the brand's primary domain (e.g., `stripe.com`, `github.com`)
- Good fallback when official press kit isn't available
- Free, no API key required for basic usage

### Brandfetch
```
https://brandfetch.com/{brand}
```
- Aggregates brand logos, colors, fonts, and guidelines
- Good for discovering brand color palettes
- Some assets require account/API access

### Curated Vector Collections
| Source | URL | Notes |
|--------|-----|-------|
| SVGPorn | svgporn.com | Developer/tech brand SVGs, community curated |
| WorldVectorLogo | worldvectorlogo.com | Large collection, search by brand name |
| SimpleIcons | simpleicons.org | 3000+ brand icons as SVGs, consistent style |
| Gilbarbara Logos | github.com/gilbarbara/logos | SVG collection of popular brand logos |

### SimpleIcons (Special Case)
```
https://simpleicons.org/icons/{brand-slug}.svg
```
- Monochrome SVG icons for 3000+ brands
- Consistent 24x24 viewBox, single-color
- Great for UI icons, not for full brand representation
- Slug format: lowercase, no spaces (e.g., `visualstudiocode`, `github`)

## Source Selection Strategy

```
1. Search for official press kit / brand page (Tier 1)
   ├── Found → Download from official source
   └── Not found ↓

2. Check GitHub org avatar + Wikimedia Commons (Tier 2)
   ├── Found → Download, note as Tier 2 source
   └── Not found ↓

3. Use Clearbit API + curated collections (Tier 3)
   ├── Found → Download, note as Tier 3 source
   └── Not found ↓

4. Report to user — no official assets found
   └── Suggest: screenshot from website, or manual download
```

## Sources to NEVER Use

| Source Type | Why |
|-------------|-----|
| Google Images | Unreliable quality, unknown licensing, often fan-made |
| Pinterest | Re-uploaded, compressed, often modified |
| Random blogs | May be outdated, wrong colors, or unofficial |
| Stock photo sites | Watermarked, paid, or generic |
| AI-generated logos | Not the actual brand asset |
| Favicon extractors | Too low-res (16x16 or 32x32) for most use cases |
| Social media profile pics | Compressed, cropped, may be outdated |
