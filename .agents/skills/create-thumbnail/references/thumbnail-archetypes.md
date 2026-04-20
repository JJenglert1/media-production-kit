# Thumbnail Archetypes

Seven proven thumbnail templates, each with layout description, layer structure, and design guidelines. Select the archetype that best matches the video's content and goal.

---

## 1. Reaction Face + Text

The most common and highest-CTR archetype. A close-up face with an exaggerated expression paired with a short, punchy headline.

### When to Use
- Vlogs, commentary, reaction videos, storytime, reviews, unboxing
- Any content where the creator's personality drives clicks

### Layout

```
┌──────────────────────────────────────┐
│                                      │
│   "MIND          ┌──────────────┐    │
│    BLOWN"        │              │    │
│                  │    FACE      │    │
│   (headline      │   (40-60%   │    │
│    left side)    │    of frame) │    │
│                  │              │    │
│                  └──────────────┘    │
│                                      │
└──────────────────────────────────────┘
```

### Layer Structure
1. Background: Dark gradient or blurred context image with color overlay
2. Subject: Face cutout (background removed), 40-60% of frame, positioned right
3. Graphics: Optional — glow/outline around face (5-10px colored drop-shadow)
4. Text: 2-4 word headline, left-aligned, vertically centered, 100-160px bold font
5. Effects: Optional — radial glow behind face, vignette edges

### Design Notes
- Face: close-up crop (head + shoulders max), eyes in upper-third
- Expression: exaggerated — wide eyes, open mouth (surprise), furrowed brows (anger), big smile (excitement)
- Text: ALL CAPS, 2-4 words only, white with black stroke
- Colors: dark background contrasts with bright text and warm face tones
- Font: Bebas Neue, Anton, or Montserrat Black

### Example Props
```json
{
  "title": "MIND BLOWN",
  "faceImage": "face.png",
  "backgroundColor": "#1a1a2e",
  "accentColor": "#e94560",
  "fontFamily": "Bebas Neue"
}
```

---

## 2. Before / After

Shows a transformation. Split-screen or side-by-side comparison highlighting a dramatic change.

### When to Use
- Tutorials, makeovers, renovations, skill progression, editing results
- Any content showing a clear transformation

### Layout

```
┌────────────────┬─────────────────┐
│                │                 │
│    BEFORE      │     AFTER       │
│   (muted,      │   (vibrant,    │
│    less        │    polished,   │
│    appealing)  │    impressive) │
│                │                 │
│  "BEFORE" ───→ │ ←── "AFTER"   │
│                │                 │
└────────────────┴─────────────────┘
```

### Layer Structure
1. Background: Split into two halves — left (muted/desaturated) and right (vibrant/enhanced)
2. Subject: Before image on left, after image on right
3. Graphics: Vertical divider line (2-4px, white or accent color), optional arrow pointing left→right
4. Text: "BEFORE" label (left), "AFTER" label (right), optional headline across the top
5. Effects: Left side may have slight desaturation filter, right side may have subtle glow

### Design Notes
- The "after" side should be dramatically more appealing
- Use a clear visual divider (line, diagonal slash, or lightning bolt shape)
- Labels are optional if the transformation is obvious
- Diagonal splits (from top-left to bottom-right) feel more dynamic than vertical
- Colors: muted/cool on before side, vibrant/warm on after side
- Font: Clean sans-serif (Montserrat, Oswald) — this archetype is image-driven

---

## 3. Listicle / Number

A large, prominent number signals structured content. The number is the visual anchor, immediately communicating the format.

### When to Use
- Top-10 lists, "X ways to...", tips collections, rankings, "best of" compilations
- Any content with a countable structure

### Layout

```
┌──────────────────────────────────────┐
│                                      │
│      ┌────┐                          │
│      │ 10 │   "BEST TOOLS           │
│      └────┘    FOR 2026"            │
│    (oversized                        │
│     number)   (supporting text)      │
│                                      │
│              [product/topic images]  │
│                                      │
└──────────────────────────────────────┘
```

### Layer Structure
1. Background: Solid color or subtle gradient matching the topic's niche
2. Subject: Optional — small product images, icons, or topic visuals arranged in a grid or row
3. Graphics: Circle/badge behind the number, optional ranking markers
4. Text: Large number (200-300px, accent color), topic headline (80-120px, white)
5. Effects: Glow or shadow behind the number badge

### Design Notes
- Number should be 2-3× larger than other text
- Place number in a colored circle, square, or badge shape
- Number color should contrast with badge background AND image background
- Supporting text explains what the list is about
- Can include small thumbnail images of listed items
- Font: Numbers in a bold display font (Anton, Bebas Neue), text in clean sans-serif

---

## 4. Tutorial / How-To

Clean, instructional design. Shows the end result or tool/process with clear, professional text.

### When to Use
- Educational content, tech tutorials, cooking recipes, DIY, software walkthroughs
- Any content teaching a skill or process

### Layout

```
┌──────────────────────────────────────┐
│                                      │
│   "HOW TO BUILD               ┌────┐│
│    A REST API"                │LOGO││
│                               └────┘│
│   ┌─────────────────────────┐       │
│   │                         │       │
│   │   [screenshot / result  │       │
│   │    / product image]     │       │
│   │                         │       │
│   └─────────────────────────┘       │
│                                      │
└──────────────────────────────────────┘
```

### Layer Structure
1. Background: Clean solid color or very subtle gradient (dark for tech, light for lifestyle)
2. Subject: Screenshot, product image, or result preview (40-50% of frame)
3. Graphics: Tool/software logos (small, corner placement), step indicators, subtle grid lines
4. Text: "How to..." or topic headline (80-120px), clean font, left-aligned
5. Effects: Minimal — clean design. Maybe a subtle drop shadow on the subject image

### Design Notes
- Show the end result, not the process — viewers want to see what they'll achieve
- Keep it clean and professional — less is more
- Include recognizable logos/icons for tools mentioned (small, not dominant)
- Colors: niche-appropriate (tech=dark+blue, cooking=warm, design=clean+modern)
- Font: Montserrat, Inter, or Poppins in Bold/ExtraBold — professional but readable

---

## 5. Versus / Comparison

Two subjects face off. Creates tension and curiosity about which option wins.

### When to Use
- Product comparisons, debates, challenge videos, "A vs B" content
- Any content comparing two options or perspectives

### Layout

```
┌────────────────┬─────────────────┐
│                │                 │
│   [Subject A]  │  [Subject B]   │
│                │                 │
│        ┌───────┴───────┐        │
│        │     VS        │        │
│        └───────┬───────┘        │
│                │                 │
│   "OPTION A"   │  "OPTION B"   │
│                │                 │
└────────────────┴─────────────────┘
```

### Layer Structure
1. Background: Split background — each side tinted with its own color (e.g., blue vs red)
2. Subject: Two competing images/products/faces, one per side, equal visual weight
3. Graphics: "VS" badge in center (large, circular, accent color), divider line, optional lightning bolt
4. Text: Names/labels for each side (60-80px), optional headline across top
5. Effects: Color tint on each side, glow behind VS badge

### Design Notes
- Both sides must have equal visual weight — neither should dominate
- "VS" text should be 80-120px in a contrasting badge (circle or diamond shape)
- Each side can have its own color tint (e.g., blue wash on left, red wash on right)
- Divider can be straight vertical, diagonal, or lightning bolt shape
- If there's a winner, add a subtle checkmark or crown on the winning side
- Font: Bold display for "VS", clean sans-serif for labels

---

## 6. Curiosity Gap

Partially reveals information to create intrigue. The viewer must watch to get the full story.

### When to Use
- Mystery content, reveals, "you won't believe", storytelling, experiment results
- Any content where the payoff requires watching

### Layout

```
┌──────────────────────────────────────┐
│                                      │
│   "I FOUND                           │
│    SOMETHING                         │
│    INSANE"                           │
│                                      │
│            ┌──────────┐              │
│            │ ???????? │              │
│            │ (blurred │              │
│            │  /hidden)│              │
│            └──────────┘              │
│                    ▲                 │
│                  arrow               │
└──────────────────────────────────────┘
```

### Layer Structure
1. Background: Dark or dramatic gradient
2. Subject: Partially obscured image — blurred, pixelated, or covered with "?" marks
3. Graphics: Red arrows or circles pointing at the hidden element, question marks, emoji-style indicators
4. Text: Hook/question text (100-140px), designed to create curiosity without revealing the answer
5. Effects: Blur filter on hidden element, dramatic vignette, optional glow on arrows

### Design Notes
- The hidden/teased element should be recognizable enough to spark curiosity but not reveal the answer
- Use blur (`filter: blur(20px)`), pixelation, or strategic cropping to conceal
- Red circles and arrows are cliché but still effective
- Text should pose a question or make a claim that demands verification
- Colors: dark/dramatic backgrounds with bright accent elements (red, yellow)
- Font: Anton or Bebas Neue for dramatic impact

---

## 7. Bold Text Only

Typography-forward design with minimal or no imagery. The text itself is the visual.

### When to Use
- Motivational content, news/commentary, abstract topics, podcast highlights
- When no compelling image exists or text alone is more powerful

### Layout

```
┌──────────────────────────────────────┐
│                                      │
│                                      │
│         "STOP DOING                  │
│          THIS NOW"                   │
│                                      │
│         (secondary text              │
│          if needed)                  │
│                                      │
│                                      │
└──────────────────────────────────────┘
```

### Layer Structure
1. Background: Bold gradient, solid color, or subtle texture/pattern
2. Subject: None (or very subtle background texture)
3. Graphics: Optional — geometric shapes, lines, or decorative elements framing the text
4. Text: Large headline (120-200px) centered, optional smaller secondary text
5. Effects: Text glow, gradient text fill, or 3D text shadow for depth

### Design Notes
- Text must be LARGE — it's the only visual element
- Use maximum contrast between text and background
- Gradient text fills or multi-color words can add visual interest
- Consider word-by-word color alternation for emphasis (e.g., "STOP" in red, "DOING" in white, "THIS" in yellow)
- Geometric shapes (circles, lines, rectangles) can frame text without competing
- White text on dark background is the safest choice
- Font: Bebas Neue, Anton, or Bangers at 140-200px

---

## Archetype Selection Guide

| Video Type | Primary Archetype | Alternative |
|-----------|-------------------|-------------|
| Vlog / Commentary | Reaction Face + Text | Bold Text Only |
| Tutorial / How-To | Tutorial | Listicle (if numbered steps) |
| Product Review | Reaction Face + Text | Versus (if comparing) |
| Top-10 / Tips | Listicle / Number | Tutorial |
| Transformation | Before / After | Reaction Face + Text |
| Comparison | Versus | Before / After |
| Storytelling / Mystery | Curiosity Gap | Reaction Face + Text |
| Motivational / Podcast | Bold Text Only | Reaction Face + Text |
| News / Breaking | Bold Text Only | Curiosity Gap |
| Challenge | Reaction Face + Text | Versus |
| Cooking / Recipe | Tutorial | Before / After |
| Gaming | Reaction Face + Text | Curiosity Gap |
| Music | Bold Text Only | Curiosity Gap |
| Fitness | Before / After | Tutorial |
