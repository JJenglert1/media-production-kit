# Asset Fetching Iteration Mappings

When the user requests changes after the initial asset fetch, map their request to the appropriate action.

## Variant Requests

| User Says | Action |
|-----------|--------|
| "get the dark version" / "dark mode logo" | Search for `"[brand] logo dark mode"` or `"[brand] logo white"` on brand's press page |
| "get the monochrome version" | Search for `"[brand] logo monochrome black"` on press kit |
| "just the icon" / "without the text" | Search for `"[brand] logomark"` or `"[brand] icon"` — the symbol without wordmark |
| "just the text" / "wordmark" | Search for `"[brand] wordmark"` — text-only mark |
| "horizontal layout" | Search for `"[brand] logo horizontal"` on brand guidelines |
| "stacked layout" | Search for `"[brand] logo stacked vertical"` on brand guidelines |

## Format Requests

| User Says | Action |
|-----------|--------|
| "I need SVG" | Re-search specifically for SVG: `"[brand] logo SVG download"` |
| "I need PNG" | If SVG exists, convert with `transform-image`; otherwise re-search for PNG |
| "higher resolution" | Search for press kit ZIP (usually contains high-res), or try `?size=1024` on API sources |
| "need it as favicon" | Hand off to `transform-image` with sizes: 16, 32, 48, 180, 512 |
| "convert to WebP" | Hand off to `transform-image` for format conversion |

## Additional Brand Requests

| User Says | Action |
|-----------|--------|
| "also get [another brand]" | Run full workflow again for the new brand |
| "get their brand colors too" | Search `"[brand] brand guidelines colors"`, add to manifest.json `brand_colors` |
| "get their font" | Search `"[brand] brand font typography"` — note: fonts may not be freely downloadable |
| "what are their guidelines?" | Search `"[brand] brand guidelines"`, provide the URL |

## Size & Transformation Requests

| User Says | Action |
|-----------|--------|
| "resize for thumbnail" | Hand off to `transform-image` — resize to fit 1280×720 composition |
| "make it 200x200" | Hand off to `transform-image` with specific dimensions |
| "resize for social media" | Hand off to `transform-image` with social media presets |
| "make a social media kit" | Hand off to `transform-image` — batch resize for all platforms |

## Troubleshooting Requests

| User Says | Action |
|-----------|--------|
| "the logo looks wrong" / "wrong logo" | Verify source URL, re-search with more specific queries |
| "it's too small" / "pixelated" | Search for higher-res source (press kit ZIP, larger API size parameter) |
| "that's the old logo" | Add year to search: `"[brand] new logo 2025 2026"`, check for rebrand |
| "can't find it" | Try all tiers in order, report if unavailable, suggest Clearbit fallback |
| "the SVG is broken" | Validate XML structure, try alternative SVG source |
| "downloaded an HTML page" | Validation failed — retry with direct download URL, not the page URL |
