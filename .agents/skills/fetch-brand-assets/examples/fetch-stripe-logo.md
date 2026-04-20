# Example: /fetch-brand-assets Stripe

## Request Parsing
- Brand: Stripe
- Asset types: All available (logo, icon, wordmark)
- Preferred formats: SVG, PNG
- Asset name: `stripe-assets`

## Research Findings

### Batch 1 — Official Sources
- Found: `stripe.com/press/resources` — official press page with downloadable assets
- Found: `stripe.com/newsroom` — press releases with brand usage
- Brand guidelines URL: `stripe.com/brand`

### Batch 2 — Trusted Aggregators
- Clearbit: `https://logo.clearbit.com/stripe.com` — 512×512 square logo
- GitHub: `https://github.com/stripe.png?size=512` — org avatar
- SimpleIcons: `https://simpleicons.org/icons/stripe.svg` — monochrome icon

### Batch 3 — Deep Extraction
- WebFetch on `stripe.com/press/resources`:
  - Full-color logo SVG (horizontal lockup)
  - Wordmark SVG
  - Stripe "S" logomark SVG
  - Badge/button assets
- Brand colors: #635BFF (Stripe purple), #0A2540 (dark navy)

## Downloaded Assets

```
stripe-assets/
├── logo/
│   ├── stripe-logo.svg          (horizontal lockup, full-color)
│   └── stripe-logo.png          (from Clearbit, 512×512)
├── icon/
│   ├── stripe-icon.svg          (S logomark, full-color)
│   └── stripe-icon-mono.svg     (S logomark, monochrome via SimpleIcons)
├── wordmark/
│   └── stripe-wordmark.svg      (text-only mark)
└── manifest.json
```

## Manifest

```json
{
  "brand": "Stripe",
  "fetched": "2025-01-15",
  "assets": [
    {
      "file": "logo/stripe-logo.svg",
      "type": "logo",
      "variant": "full-color",
      "format": "svg",
      "source": "https://stripe.com/press/resources",
      "dimensions": null
    },
    {
      "file": "logo/stripe-logo.png",
      "type": "logo",
      "variant": "full-color",
      "format": "png",
      "source": "https://logo.clearbit.com/stripe.com",
      "dimensions": "512x512"
    },
    {
      "file": "icon/stripe-icon.svg",
      "type": "icon",
      "variant": "full-color",
      "format": "svg",
      "source": "https://stripe.com/press/resources",
      "dimensions": null
    },
    {
      "file": "icon/stripe-icon-mono.svg",
      "type": "icon",
      "variant": "monochrome",
      "format": "svg",
      "source": "https://simpleicons.org/icons/stripe.svg",
      "dimensions": null
    },
    {
      "file": "wordmark/stripe-wordmark.svg",
      "type": "wordmark",
      "variant": "full-color",
      "format": "svg",
      "source": "https://stripe.com/press/resources",
      "dimensions": null
    }
  ],
  "brand_colors": {
    "primary": "#635BFF",
    "secondary": "#0A2540"
  },
  "usage_notes": "Assets sourced from official Stripe press page. See brand guidelines for usage restrictions.",
  "guidelines_url": "https://stripe.com/brand"
}
```

## Output

```
Brand Assets: Stripe
━━━━━━━━━━━━━━━━━━━━

Downloaded:
  ✅ logo/stripe-logo.svg (full-color, SVG, horizontal lockup)
  ✅ logo/stripe-logo.png (full-color, 512×512, Clearbit)
  ✅ icon/stripe-icon.svg (S logomark, full-color)
  ✅ icon/stripe-icon-mono.svg (S logomark, monochrome)
  ✅ wordmark/stripe-wordmark.svg (text-only, full-color)

Brand Colors: #635BFF (Stripe purple), #0A2540 (dark navy)
Guidelines: stripe.com/brand
Output: stripe-assets/

Ready for use with /transform-image for resizing or /create-thumbnail for compositions.
```
