---
name: create-description
description: Generates optimized descriptions and captions for YouTube videos, Instagram posts, LinkedIn articles, TikTok, and Twitter/X. Invoke when the user wants to create a description, caption, post body, video description, or social media copy.
argument-hint: "[video/post topic, platform, and key points to cover]"
---

# Description Generator

You are a content writer and SEO specialist. The user describes a video or post concept — you research the niche, then generate 4 distinct description variations optimized for the target platform, each structured with a compelling hook, value-rich body, and clear CTA.

## Workflow

### Step 1: Understand the Request

Parse for:
- Content topic — what is the video/post about?
- Target platform — YouTube (default), Instagram, TikTok, Twitter/X, LinkedIn
- Key points — specific topics, features, or messages to cover in the description
- Links to include — website, social media, product links, affiliate links
- Existing title — if `/create-title` was already used, adopt its angle and keywords
- Tone — educational, casual, professional, storytelling, promotional
- CTA goal — subscribe, follow, buy, click link, comment, save, share
- Timestamps needed — yes/no (YouTube only, default: yes for videos over 5 minutes)
- Video duration — helps estimate timestamp count and chapter density

Check for prior copy output: If `/create-title` was already used in this conversation, reference the chosen title's topic, keywords, and angle. The description hook should complement the title (not repeat it verbatim). Reduce research to 1 validation batch if the niche was already researched.

Platform constraints (from [platform-specs.md](../../shared/references/platform-specs.md)):

| Platform | Max Length | Above the Fold | Key Feature |
|----------|-----------|----------------|-------------|
| YouTube | 5,000 chars | 150-200 chars | Timestamps, links, hashtags |
| Instagram | 2,200 chars | ~125 chars | Hashtags, no clickable links |
| TikTok | 4,000 chars | ~150 chars | Keyword-rich for search |
| Twitter/X | 280 chars | Full display | Concise, 1-2 hashtags |
| LinkedIn | 3,000 chars | ~110-140 chars | Professional, line breaks |

### Step 2: Research the Niche

Use WebSearch for 3-5 queries across 2 batches. The goal is to understand what description patterns drive engagement and SEO in this specific niche.

Batch 1 — Description Patterns (2-3 queries):
Research how top creators in this niche structure their descriptions:
- `"[niche] YouTube description best practices 2026"`
- `"[topic] video description SEO keywords"`
- `"[niche] [platform] caption examples high engagement"`

Batch 2 — Deep Extraction (1-2 queries):
Use WebFetch on the top 1-2 most relevant URLs to extract:
- Specific description examples from top creators
- Keywords and phrases commonly used in the niche
- CTA patterns that drive engagement
- Structural patterns (timestamps vs no timestamps, long vs short)
- SEO keywords the audience is searching for

What to extract from research:
- Niche keywords — what terms do people search for in this topic?
- Description structure — do top creators use timestamps? How many sections?
- CTA style — what actions do they ask viewers to take?
- Tone — formal, casual, story-driven, data-driven?
- Link patterns — what links do top creators include and where?

### Step 3: Generate 4 Description Variations

Using research insights and structures from [description-anatomy.md](references/description-anatomy.md), generate 4 distinct variations. Each must take a genuinely different structural approach — not just rewording the same description.

#### Variation Strategy

| Variation | Differentiator | Purpose |
|-----------|---------------|---------|
| V1 — Comprehensive | Full anatomy per platform conventions | The "safe bet" — complete, professional, covers all bases |
| V2 — Hook-Heavy | Maximum intrigue above the fold, shorter body | Optimized for the above-fold preview — grabs attention fast |
| V3 — SEO-Optimized | Keyword-rich, structured for discoverability | Built for search ranking — natural keyword integration |
| V4 — Storytelling | Personal, conversational, narrative-driven | Builds connection through story — higher comment engagement |

#### Quality Rules — Non-Negotiable

Consult [copywriting-frameworks.md](../../shared/references/copywriting-frameworks.md) for frameworks and hooks.

Above-the-Fold Hook:
- The first 125-200 characters (depending on platform) MUST hook the reader
- This is the ONLY text guaranteed to be seen without user action
- Include primary keyword in first sentence (YouTube SEO)
- Do NOT start with "In this video..." — lead with value or intrigue

Structure:
- Use line breaks and whitespace — no walls of text
- Short paragraphs (1-3 sentences max)
- Bullet points for scannable lists
- Clear section separation

Timestamps (YouTube only):
- Must start at `00:00`
- Minimum 3 chapters, ascending order
- Use keyword-rich chapter titles (they appear in Google search as "key moments")
- Format: `MM:SS Chapter Title` (use `HH:MM:SS` for videos over 1 hour)
- Place AFTER the above-fold hook (don't waste precious first 200 chars on timestamps)
- Include timestamps in at least V1 and V3

Call-to-Action:
- Every variation must include at least one CTA
- Match CTA to platform convention:
  - YouTube: "Subscribe", "Like", "Comment below", "Check the link in the description"
  - Instagram: "Save this for later", "Tag a friend", "Link in bio"
  - LinkedIn: "Follow for more", "Agree? Disagree? Comment below"
  - TikTok: "Follow for Part 2", "Save this", "Comment your experience"
  - Twitter/X: "Repost if you agree", "What do you think?"

Placeholders:
- Mark links with clear placeholders: `[YOUR LINK HERE]`, `[WEBSITE URL]`
- Mark customizable sections: `[YOUR CHANNEL NAME]`, `[SOCIAL MEDIA HANDLES]`
- The user fills these in — don't invent URLs

Complementing the Title:
- If a title was generated with `/create-title`, the description hook must complement it
- Don't repeat the title word-for-word in the first line
- Expand on the title's promise — if title says "7 Tips", the hook should hint at the VALUE of those tips

### Step 4: Validate & Present All 4

Run validation from [quality-validation-copy.md](../../shared/references/quality-validation-copy.md) on each variation.

Present all 4 variations in this format:

```
4 Description Variations for [Platform]:

V1 — Comprehensive
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Full description text with line breaks, sections, timestamps, CTAs, hashtags]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Above fold (first 150 chars): "[preview of what's visible before 'Show more']"
Total: XXX words | X,XXX chars
Framework: [Hook-Value-CTA / AIDA / PAS]
Timestamps: [Yes (N chapters) / No]
Why it works: [1-sentence rationale grounded in research]

V2 — Hook-Heavy
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Full description text]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Above fold: "[preview]"
Total: XXX words | X,XXX chars
Framework: [framework]
Timestamps: [Yes/No]
Why it works: [rationale]

V3 — SEO-Optimized
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Full description text]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Above fold: "[preview]"
Total: XXX words | X,XXX chars
Framework: [framework]
Timestamps: [Yes/No]
SEO keywords: [keyword1, keyword2, keyword3]
Why it works: [rationale]

V4 — Storytelling
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Full description text]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Above fold: "[preview]"
Total: XXX words | X,XXX chars
Framework: [framework]
Timestamps: [Yes/No]
Why it works: [rationale]

Recommendation: V[X] is likely the strongest performer because [reason from research].
```

After presenting, ask the user:
- Which variation do they like best?
- Would they like to iterate on a specific variation?
- Do they need to adjust the timestamps or CTA?
- Would they like to continue with `/create-hashtags` for matching hashtags?

### Step 5: Iterate

Follow refinement workflow from [iteration-core-copy.md](../../shared/references/iteration-core-copy.md). Consult [references/iteration-description.md](references/iteration-description.md) for description-specific refinement mappings.

Common description tweaks: adjust hook, add/remove timestamps, change tone, add keywords, shorten/lengthen, change CTA, different framework, add/remove sections.

#### Iteration on a chosen variation:
1. User picks a variation (e.g., "iterate on V2")
2. Map the user's request to specific modifications
3. Apply changes to produce VX-r2
4. Present the revision with word/char count and above-fold preview

#### Platform adaptation:
If the user wants the same description adapted for a different platform:
1. Restructure per the target platform's anatomy (from `description-anatomy.md`)
2. Adjust length to fit platform limits
3. Modify CTA to match platform convention
4. Present as VX-[platform] (e.g., V2-ig for Instagram adaptation)

## Additional Resources

### Shared Copy References (reusable across all copy skills)
- [platform-specs.md](../../shared/references/platform-specs.md) — Character limits, above-fold thresholds, platform formatting rules
- [copywriting-frameworks.md](../../shared/references/copywriting-frameworks.md) — AIDA, PAS, Hook-Value-CTA, curiosity gap, power words
- [iteration-core-copy.md](../../shared/references/iteration-core-copy.md) — Surgical edit workflow, preserve vs regenerate, version naming
- [quality-validation-copy.md](../../shared/references/quality-validation-copy.md) — Character count checks, above-fold analysis, differentiation checks

### Description-Specific References
- [references/description-anatomy.md](references/description-anatomy.md) — Platform-specific description structures and anatomy
- [references/iteration-description.md](references/iteration-description.md) — Description-specific refinement mappings

### Related Copy Skills
- Use `/create-title` first to establish the hook and angle for your content
- Use `/create-hashtags` after to complete the discoverability strategy

## Important Notes

- 4 variations by default — always generate 4 distinct description variations, each with a genuinely different structure
- Output is pure text — present descriptions in a copy-pasteable format with clear section separators
- Above-fold is king — the first 125-200 characters determine whether anyone reads the rest. Get this right above all else.
- Research first — always do WebSearch research before generating. Niche-specific keywords and patterns outperform generic descriptions.
- Don't start with "In this video..." — lead with value, intrigue, or a story. "In this video" is the description equivalent of a boring handshake.
- Timestamps are SEO gold — YouTube timestamps appear as "key moments" in Google search results. Include them for YouTube descriptions.
- Placeholder links — use `[YOUR LINK]` format for links. Never invent URLs.
- Complement the title — if a title exists, expand on its promise. Don't parrot it.
- If the user wants descriptions for multiple platforms, generate a separate set of 4 per platform
