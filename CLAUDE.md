# HyperFrames Project

## Skills — USE THESE FIRST

**Always invoke the relevant skill before writing or modifying compositions.** Skills encode framework-specific patterns (e.g., `window.__timelines` registration, `data-*` attribute semantics, shader-compatible CSS rules) that are NOT in generic web docs. Skipping them produces broken compositions.

| Skill                      | Command                   | When to use                                                                                       |
| -------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------- |
| **hyperframes**            | `/hyperframes`            | Creating or editing HTML compositions, captions, TTS, audio-reactive animation, marker highlights |
| **hyperframes-cli**        | `/hyperframes-cli`        | CLI commands: init, lint, preview, render, transcribe, tts                                        |
| **hyperframes-registry**   | `/hyperframes-registry`   | Installing blocks and components via `hyperframes add`                                            |
| **website-to-hyperframes** | `/website-to-hyperframes` | Capturing a URL and turning it into a video — full website-to-video pipeline                      |
| **gsap**                   | `/gsap`                   | GSAP animations for HyperFrames — tweens, timelines, easing, performance                          |

> **Skills not installed?** Run `npx skills add heygen-com/hyperframes --yes` and restart your agent session.

## Commands

\`\`\`bash
npx hyperframes preview          # preview in browser (studio editor)
npx hyperframes render           # render to MP4
npx hyperframes lint             # validate compositions (errors + warnings)
npx hyperframes lint --verbose   # include info-level findings
npx hyperframes lint --json      # machine-readable output for CI
npx hyperframes docs <topic>     # reference docs in terminal
\`\`\`

## Documentation

**Quick reference** (no network required):
\`\`\`bash
npx hyperframes docs <topic>
\`\`\`
Topics: \`data-attributes\`, \`gsap\`, \`compositions\`, \`rendering\`, \`examples\`, \`troubleshooting\`

**Full documentation** — use the machine-readable index, do NOT guess URLs:
\`\`\`
https://hyperframes.heygen.com/llms.txt
\`\`\`

## Project Structure

\`\`\`
.
├── index.html          # root composition (entry point)
├── compositions/       # sub-compositions (data-composition-src)
│   └── components/     # reusable components
├── assets/             # media files (video, audio, images)
├── meta.json           # project metadata (id, name)
├── hyperframes.json    # project config + registry
└── transcript.json     # word-level transcript (generated, gitignored)
\`\`\`

## Linting — ALWAYS RUN AFTER CHANGES

After creating or editing any \`.html\` composition, **always** run the linter before considering the task complete:

\`\`\`bash
npx hyperframes lint
\`\`\`

Fix all **errors** before presenting the result. Warnings are informational and usually safe to ignore.

## Key Rules

1. Every timed element needs \`data-start\`, \`data-duration\`, and \`data-track-index\`
2. Visible timed elements **MUST** have \`class="clip"\` — the framework uses this for visibility control
3. GSAP timelines must be paused and registered on \`window.__timelines\`:
   \`\`\`js
   window.__timelines = window.__timelines || {};
   window.__timelines["composition-id"] = gsap.timeline({ paused: true });
   \`\`\`
4. Videos use \`muted\` with a separate \`<audio>\` element for the audio track
5. Sub-compositions use \`data-composition-src="compositions/file.html"\` to reference other HTML files
6. Only deterministic logic — no \`Date.now()\`, no \`Math.random()\`, no network fetches
7. Default canvas is 1920×1080; set \`data-width\` and \`data-height\` on the root div to change it

## Workflow Best Practices

1. **Start with a skill** — \`/hyperframes\` for any composition work
2. **Lint early and often** — catch issues before they compound
3. **Preview before render** — \`npx hyperframes preview\` opens the studio editor
4. **Add blocks from the registry** — \`npx hyperframes add <block>\` rather than writing from scratch
5. **Keep compositions modular** — break complex videos into sub-compositions under \`compositions/\`
6. **Commit assets separately** — rendered \`.mp4\` files are gitignored; commit source HTML only
