# HyperFrames Starter

A ready-to-use starter project for building AI-powered videos with [HyperFrames](https://hyperframes.heygen.com/introduction) — HTML/CSS/JS compositions that render to MP4.

## Quick Start

```bash
# 1. Clone this repo
git clone <repo-url>
cd HyperFrames

# 2. Install agent skills (one-time)
npx skills add heygen-com/hyperframes --yes

# 3. Open in Claude Code (or your preferred AI agent)
claude .

# 4. Try a starter prompt
# "Using /hyperframes, create a 15-second intro about [your topic]"

# 5. Preview in the browser
npx hyperframes preview

# 6. Render to MP4
npx hyperframes render
```

## What's Included

| File | Purpose |
|------|---------|
| `index.html` | Root composition — your video's entry point |
| `hyperframes.json` | Project config + registry settings |
| `meta.json` | Project metadata |
| `CLAUDE.md` | Agent instructions (read automatically by Claude Code) |
| `AGENTS.md` | Agent instructions for other AI tools (Cursor, Codex, etc.) |
| `.agents/skills/` | Installed HyperFrames skills for all major AI agents |

## Available Skills

Skills are installed automatically and work across Claude Code, Cursor, Codex, Gemini CLI, and more.

| Skill | Slash Command | Purpose |
|-------|--------------|---------|
| hyperframes | `/hyperframes` | Create and edit HTML compositions |
| hyperframes-cli | `/hyperframes-cli` | CLI usage: lint, preview, render, TTS |
| hyperframes-registry | `/hyperframes-registry` | Add blocks from the registry |
| website-to-hyperframes | `/website-to-hyperframes` | Convert a URL to a video |
| gsap | `/gsap` | GSAP animation patterns |

## CLI Commands

```bash
npx hyperframes preview      # Open studio editor in browser
npx hyperframes render       # Render to MP4
npx hyperframes lint         # Validate all compositions
npx hyperframes docs <topic> # Read local docs (no network needed)
```

## Resources

- [Full Documentation](https://hyperframes.heygen.com/introduction)
- [Prompting Guide](https://hyperframes.heygen.com/guides/prompting)
- [Block Registry](https://hyperframes.heygen.com/registry)
- [Machine-readable docs for AI tools](https://hyperframes.heygen.com/llms.txt)
