# HyperFrames Starter Kit

A ready-to-use AI video production environment. Open this folder in Claude Code (or any compatible AI agent), describe the video you want to make, and your agent will build, animate, and render it to MP4 — complete with music, sound effects, voiceover, and thumbnails.

---

## What This Repo Does

HyperFrames is a framework that lets AI agents build videos as code — HTML files with animations and audio that render to `.mp4`. This starter kit comes pre-loaded with every skill your agent needs to go from idea to finished video.

You don't need to know how to code. Just open the project in Claude Code and start talking to it.

---

## Getting Started

### 1. Install Claude Code

If you haven't already: [claude.ai/code](https://claude.ai/code)

### 2. Clone this repo and open it

```bash
git clone <repo-url>
cd HyperFrames
claude .
```

### 3. Try your first prompt

```
Using /hyperframes, create a 30-second product intro video for [your product]
```

### 4. Preview and render

```bash
npx hyperframes preview    # opens a live preview in your browser
npx hyperframes render     # renders the final MP4 file
```

---

## Slash Commands (Skills)

Type any of these in Claude Code to activate that skill. Each one loads a set of instructions that tells your agent exactly how to do that task correctly.

### Video Composition

| Command | What it does |
|---------|-------------|
| `/hyperframes` | **Build a video.** Creates and edits animated HTML compositions — the core skill for making anything in this repo. Use this first for any video work. |
| `/hyperframes-cli` | **Run CLI tools.** Guides your agent through commands like lint, preview, render, and text-to-speech generation. |
| `/hyperframes-registry` | **Add pre-built blocks.** Installs ready-made components (lower thirds, titles, transitions, etc.) into your video from the HyperFrames block registry. |
| `/website-to-hyperframes` | **Turn a website into a video.** Give it a URL and it runs a full 7-step pipeline: captures the site, writes a script, creates a storyboard, generates voiceover, builds compositions, and validates the result. |
| `/gsap` | **Advanced animations.** Deep animation patterns using GSAP — tweens, timelines, easing, stagger effects, and performance optimization. |

### Audio

| Command | What it does |
|---------|-------------|
| `/create-song` | **Generate original music.** Describe a vibe, genre, or mood and your agent synthesizes a full stereo `.wav` file — complete song with sections, mastering, and all. |
| `/create-sfx` | **Generate sound effects.** Describe a sound (UI click, whoosh, impact, etc.) and get a synthesized `.wav` file back. |
| `/create-soundscape` | **Generate ambient audio.** Creates layered environmental soundscapes that evolve naturally over time — great for background atmosphere. |

### Images & Visuals

| Command | What it does |
|---------|-------------|
| `/create-thumbnail` | **Generate YouTube thumbnails.** Produces 4 distinct thumbnail variations using proven design archetypes, with A/B testing in mind. |
| `/transform-image` | **Resize and convert images.** Crops, resizes, converts formats (PNG, JPG, WebP), and generates social media kits or favicon sets from a source image. |
| `/fetch-brand-assets` | **Download brand assets.** Pulls official logos, icons, and brand assets from a company's press kit or trusted sources, saving them locally for use in your video. |

### Copy & Distribution

| Command | What it does |
|---------|-------------|
| `/create-title` | **Generate video titles.** Produces 4 title variations optimized for YouTube, Instagram, TikTok, LinkedIn, and Twitter/X — each using a different hook or formula. |
| `/create-description` | **Generate video descriptions.** Creates 4 description variations per platform with proper SEO structure, hooks, timestamps, and calls-to-action. |
| `/create-hashtags` | **Generate hashtags.** Builds platform-specific hashtag sets using a broad-to-niche pyramid strategy, with 4 variations (balanced, niche-heavy, trend-heavy, brand-focused). |

### Claude Design Bridge

| Command | What it does |
|---------|-------------|
| `/claude-design-to-hyperframes` | **Convert a Claude Design export to HyperFrames.** Point it at a Claude Design export folder (the kind with `scenes.jsx` and an `assets/` folder) and it translates the React-based animation into a HyperFrames HTML composition ready to render as MP4. Handles the full mapping: Sprite clips → HyperFrames clips, `useTime`/`useSprite` hooks → GSAP timelines, easing functions, audio, background video, and pixel effects. |

---

## Example Workflows

**"I want to make a product demo video"**
1. `/hyperframes` — build the composition and animations
2. `/create-song` — generate background music
3. `/create-thumbnail` — generate a thumbnail
4. `/create-title` + `/create-description` + `/create-hashtags` — prep for publishing

**"I want to turn our homepage into a video ad"**
1. `/website-to-hyperframes` — give it your URL, it handles everything
2. `/create-sfx` — add sound effects
3. `/create-thumbnail` — generate a thumbnail

**"I want to make a branded intro"**
1. `/fetch-brand-assets` — pull your logo and brand colors
2. `/hyperframes` — build the animated intro
3. `/create-soundscape` — add ambient audio

**"I designed an animation in Claude and want to render it as an MP4"**
1. Export your project from Claude Design (download the zip/folder)
2. `/claude-design-to-hyperframes` — point it at the export folder, it rebuilds it in HyperFrames
3. `/create-song` or `/create-sfx` — add or swap out the audio
4. `npx hyperframes render` — export to MP4

---

## What's in This Repo

```
HyperFrames/
├── index.html          ← your video composition (edit this to build your video)
├── compositions/       ← sub-scenes and reusable components
├── assets/             ← media files (images, audio, video clips)
├── hyperframes.json    ← project configuration
├── meta.json           ← project name and ID
├── CLAUDE.md           ← instructions your AI agent reads automatically
├── AGENTS.md           ← same instructions, for Cursor / Codex / other agents
└── .agents/skills/     ← all 14 installed skills (works in any AI agent)
```

---

## Re-installing Skills

If you're setting this up fresh or skills go missing:

```bash
npx skills add heygen-com/hyperframes --yes
npx skills add SecondWindAI/creator-plugins --yes
```

Then restart Claude Code.

---

## Resources

- [HyperFrames Documentation](https://hyperframes.heygen.com/introduction)
- [Prompting Guide](https://hyperframes.heygen.com/guides/prompting)
- [Block Registry](https://hyperframes.heygen.com/registry)
- [SecondWindAI Creator Plugins](https://github.com/SecondWindAI/creator-plugins)
