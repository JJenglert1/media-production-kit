---
name: create-song
description: Generates original music as a .wav audio file from a vibe, genre, or mood description. Invoke when the user wants to create music, make a beat, compose a track, produce a song, write a melody, or generate a musical piece. Handles everything from lo-fi hip hop to metal to classical to EDM.
argument-hint: "[description of the music you want]"
---

# Beat Generator

You are a music producer and audio synthesis expert. The user describes a vibe, genre, mood, or style — you research it, then generate a complete Python script that synthesizes that music as a stereo .wav audio file.

## Workflow

### Step 1: Understand the Request

Parse the user's description for:
- Genre/style (e.g., lo-fi hip hop, synthwave, trap, ambient, jazz, classical, metal, trance)
- Mood/emotion (e.g., reflective, energetic, melancholic, upbeat, dark, hopeful)
- Specific references (e.g., "like The XX Intro", "Daft Punk vibes", "sounds like rain") — when given, these become priority research targets in Step 2 Batch 4. Look up the exact track on WhoSampled, Hooktheory, and production breakdown sites to extract its musical DNA
- Lyrics requested — if the user wants lyrics/vocals, consult [references/lyric-writing.md](references/lyric-writing.md) for rhyme schemes, meter, and songwriting techniques
- Track name — derive a kebab-case slug from the description (e.g., "dreamy lo-fi beat" → `dreamy-lofi-beat`, "dark synthwave" → `dark-synthwave`). If the user specifies a name, use that
- Duration preference — default to 2-3 minutes if not specified
- Reference audio file — if the user provides a .wav/.mp3 file, analyze it before proceeding. Run the analysis pipeline from [references/reference-track-analysis.md](references/reference-track-analysis.md) to extract BPM, key, spectral profile, loudness, and section structure. Use these as targets for Steps 3-4 instead of relying solely on research
- Generation mode — default to parallel clip mode for songs >= 60 seconds (faster, produces individual section clips). Use single-file mode if the user says "no split", "single file", "no parallel", or the song is under 60 seconds. Both modes produce identical output quality and share the same core code

Create the project folder immediately after parsing:
```python
import os
TRACK_NAME = '{track-name}'  # kebab-case slug
TRACK_DIR = TRACK_NAME
SCRIPTS_DIR = f'{TRACK_DIR}/scripts'
SOUNDS_DIR = f'{TRACK_DIR}/sounds'
os.makedirs(SCRIPTS_DIR, exist_ok=True)
os.makedirs(SOUNDS_DIR, exist_ok=True)
```

This produces the folder structure:
```
{track-name}/
├── scripts/          # song_config.py, clip scripts, stitch.py
├── sounds/           # Individual section clips (.wav)
└── {track-name}.wav  # Final mastered output
```

### Step 2: Research the Style

Use `WebSearch` to research 8-12 queries in 4 batches:

Batch 1 — Musical DNA (2-3 queries):
- BPM/tempo range, key signatures, time signature
- Scale/mode (Dorian, Phrygian, etc.), chord progressions (with Roman numerals AND note names)
- Melodic characteristics, bass patterns

Batch 2 — Production DNA (2-3 queries):
- Specific effects: reverb type/decay, delay times, distortion character, compression style
- Sound design: what synthesis techniques recreate the genre's instruments
- Mixing approach: frequency balance, stereo width, dynamics

Batch 3 — Arrangement & Reference (1-2 queries):
- Song structure: section order, lengths, energy flow
- Reference tracks: what specific production details make them work

Batch 4 — Composition Research & Sampling Context (2-4 queries):

Deep-dive into the musical DNA of the requested style using music composition and analysis resources. Search these sites and databases for concrete details:

- WhoSampled (`site:whosampled.com`) — find what samples, interpolations, and musical elements define the genre or reference track. Look for: original sources sampled, common sample chains (e.g., Amen break lineage), producer techniques, how iconic sounds were constructed
- Splice / sample pack blogs — what loops, one-shots, and textures are standard in the genre. What drum machines (808, 909, Linndrum, SP-1200) define the sound
- Song analysis sites (`site:hooktheory.com`, `site:musicnotes.com`, Genius annotations) — chord-by-chord and section-by-section breakdowns of reference tracks. Hooktheory's Theorytab has crowd-sourced chord progressions for thousands of songs — use these for authentic progressions
- Production breakdown videos/articles — search for "how [reference track] was made", "deconstructed", or "[genre] production breakdown". These reveal specific synth patches, effect chains, arrangement tricks
- Music theory forums (`site:reddit.com/r/musictheory`, `site:reddit.com/r/WeAreTheMusicMakers`) — real producer discussions about what makes a genre tick, common mistakes, arrangement secrets
- Songwriting databases (`site:songwriteruniverse.com`, `site:soundonsound.com`) — interviews with producers/composers revealing their process, go-to chord moves, and mixing philosophy

What to extract from composition research:
- Specific chord progressions used in hit songs of the genre (not generic — actual songs)
- Sample sources and how they were chopped/processed (informs texture and layering decisions)
- Signature production moves (e.g., sidechain pumping in French house, half-time snare in trap, vinyl crackle in lo-fi)
- Arrangement patterns: how many bars per section, where the drop/chorus hits, what elements enter/exit where
- Sound palette: which specific synths/instruments/drum machines define the genre's identity
- Harmonic clichés to use (and which to avoid for freshness)

Deep-dive with WebFetch: After WebSearch returns results, use `WebFetch` on the top 2-3 most relevant URLs (especially Hooktheory analyses, SoundOnSound breakdowns, and Reddit production threads) to extract full page content. Search snippets truncate the specific chord progressions, BPM, and production details you need — always read the full page.

Focus on actionable production details — not history or culture. You need numbers: BPM ranges, frequency ranges, filter cutoffs, reverb decay times, specific chord progressions.

### Step 3: Design the Energy Map

Before writing ANY code, design the full energy map. This is the blueprint that determines whether the track engages listeners or puts them to sleep. See [energy-framework.md](../../shared/references/energy-framework.md) for the core system and [references/energy-music.md](references/energy-music.md) for emotional arcs and music-specific engagement techniques.

1. Choose an emotional arc — pick one from `EMOTIONAL_ARCS` (hero_journey, night_drive, tension_release, melancholic_beauty, party_energy, cinematic_epic) or design a custom one that matches the user's vibe
2. Define sections — name each section, assign bar count, and set 5 energy dimensions per section:
   - Intensity (0-10): overall power, volume, compression
   - Density (0-10): number of active layers (sparse→full)
   - Rhythm (0-10): rhythmic complexity (simple→polyrhythmic)
   - Harmonic (0-10): chord richness (triads→extensions)
   - Brightness (0-10): spectral energy (dark/filtered→full/airy)
3. Plan tension/release cycles — mark where pulls (risers, builds, filter closes) and pushes (drops, impacts, filter opens) occur. Every pull needs a matching push
4. Set positive/negative styles per section — composition plan style: what should be present AND what should be absent in each section
5. Check the contrast ratio — peaks and valleys should differ by 4+ intensity points. If everything is 6-8, the track will feel flat

Output the energy map to console when the script runs so the user can see the arc.

### Step 4: Apply Music Science (Production Quality)

Before writing code, apply research-backed principles from the science references:

1. Psychoacoustic voicing check — consult [psychoacoustics.md](../../shared/references/psychoacoustics.md):
   - Verify chord voicings respect critical bandwidth (no 3rds below 200Hz, spread bass voicings wide)
   - Plan bass strategy: real sub (30-60Hz) + harmonic layer (100-300Hz) for translation to all speakers
   - Select mood EQ profile (warm/cold/dark/bright/ethereal) matching the requested vibe

2. Melody design — consult [references/music-science.md](references/music-science.md):
   - Target ~2.5 bits/note entropy (balance predictability and surprise)
   - Use gap-fill principle (reverse direction after leaps >5 semitones, 80% probability)
   - 70% stepwise intervals, 30% rests, arch-shaped contour with peak at ~65% through phrase
   - Design a memorable hook: 2-4 bars, 80% stepwise with one distinctive skip, repeat 3x then vary
   - Apply appoggiatura at key emotional moments (stressed dissonance resolving = chills trigger)

3. Groove design — target 30-40% syncopation for peak groove (Witek 2014). Apply genre-appropriate microtiming offsets (5-15ms for groovy feel)

4. Studio production plan — consult [studio-production.md](../../shared/references/studio-production.md):
   - Assign each instrument a frequency slot (no two instruments competing in same band)
   - Plan saturation type: tube (even harmonics = warm) for pads/bass, tape for mix bus, transistor for aggressive elements
   - Plan stereo field: mono bass below 200Hz, frequency-dependent width, complementary panning
   - Select reverb type and pre-delay per element (vocals 50-100ms, drums 0-20ms)
   - Target LUFS: -14 for streaming, with genre-appropriate crest factor

5. Synthesis selection — consult [advanced-synthesis-dsp.md](../../shared/references/advanced-synthesis-dsp.md):
   - Choose appropriate oscillator quality (PolyBLEP for most, MinBLEP for high-fidelity leads)
   - For analog warmth: use Moog ladder or ZDF SVF filters instead of basic butter
   - For realistic instruments: consider physical modeling (Karplus-Strong for plucked, waveguide for strings)
   - For rich pads: wavetable morphing or additive synthesis with evolving partials

6. Automation & modulation plan — consult [automation-core.md](../../shared/references/automation-core.md) and [references/automation-music.md](references/automation-music.md):
   - Plan within-section parameter movement (filter sweeps, LFO assignments, pan automation)
   - Assign LFO rates per instrument (slow for pads: 0.1-0.5Hz, medium for leads: 1-4Hz, fast for FX: 4-12Hz)
   - Map automation curves to energy dimensions (brightness → filter cutoff sweep, intensity → drive amount)
   - Distinguish section-level energy (Step 3) from phrase-level and micro-level modulation (this step)

### Step 5: Generate Song Config (`song_config.py`)

Generate `{track-name}/scripts/song_config.py` — the shared foundation that ALL rendering uses. Both parallel and single-file modes import this file. It contains everything needed to render any section of the song.

- Core dependencies: `numpy` and `scipy` (always), plus `pedalboard`, `soundfile`, `pretty-midi`, `midiutil` (for enhanced quality)
- All scripts run from the project root (not from inside the folder): `uv run --with numpy --with scipy --with pedalboard --with soundfile --with pretty-midi --with midiutil python3 {track-name}/scripts/<script>.py`
- Generates stereo `.wav` clips at 44100 Hz sample rate (24-bit via soundfile preferred, 16-bit fallback)
- Prints the energy map at import time so the user can see the arc

#### `song_config.py` Architecture

```
1. Path constants (TRACK_NAME, TRACK_DIR, SCRIPTS_DIR, SOUNDS_DIR, OUTPUT_FILE)
2. Audio constants (SR, BPM, BEAT_DUR, BAR_DUR, KEY_ROOT, SCALE, TOTAL_BARS)
3. os.makedirs(SOUNDS_DIR, exist_ok=True)
4. SECTIONS array (all sections with bars, 5-D energy, positive/negative styles, transitions)
5. CHORD_PROGRESSION dict (pre-computed voice-led chords per bar — guarantees harmonic continuity across clips)
6. DSP primitives (PolyBLEP oscillators, sosfilt filters, ADSR envelopes, place(), helpers)
7. Effect processors (Freeverb, delay, chorus, phaser, compressor, limiter)
8. Instrument/sound synthesis functions (build_kick, build_snare, build_pad, build_bass, etc.)
9. Music theory (scales, chord types, voice_lead, generate_melody, apply_swing, humanize)
10. Musical content (melody patterns, bass patterns, engagement elements per section)
11. Mix settings (PANNING dict, TRACK_DENSITY_THRESHOLD, REVERB_PARAMS, DELAY_PARAMS)
12. OVERLAP_SECONDS = 0.5 (tail rendered beyond section end for crossfading — must match CROSSFADE_MS)
13. render_clip(section_idx, start_bar, num_bars, output_path) — THE SHARED RENDER FUNCTION
```

Path constants at the top of `song_config.py`:
```python
import os
TRACK_NAME = '{track-name}'
TRACK_DIR = TRACK_NAME
SCRIPTS_DIR = f'{TRACK_DIR}/scripts'
SOUNDS_DIR = f'{TRACK_DIR}/sounds'
OUTPUT_FILE = f'{TRACK_DIR}/{TRACK_NAME}.wav'
os.makedirs(SOUNDS_DIR, exist_ok=True)
```

#### The `render_clip()` Function

This is the core DRY element — a single function that renders any section of the song to a .wav file. Both parallel and single-file modes call this exact same function. It handles:

- Building all instrument tracks for the given bar range (density-gated by energy)
- Per-track compression and effects (reverb, delay — using shared REVERB_PARAMS/DELAY_PARAMS)
- Stereo panning (using shared PANNING dict)
- Multi-dimensional energy automation with smoothstep for the given bars
- Sidechain compression
- Engagement elements for these bars (ear candy, fills, risers)
- Rendering `OVERLAP_SECONDS` (0.5s) of tail after the last bar (reverb/delay decay only, no new notes)
- Exporting to the output path as 24-bit stereo .wav (no master chain — that happens in stitch.py)
- Normalizing clip to -3 dBFS (leaves headroom for mastering)

```python
def render_clip(section_idx, start_bar, num_bars, output_path):
    """Render a section of the song to a .wav file.

    Args:
        section_idx: Index into SECTIONS array
        start_bar: First bar to render
        num_bars: Number of bars to render
        output_path: Where to save the .wav clip
    """
    # ... builds tracks, applies effects, mixes to stereo, exports
    # Does NOT apply master chain
```

#### Energy-Driven Arrangement Rules

The energy map controls everything. Each bar's 5 dimensions determine:
- Which tracks play — each instrument has a density threshold (see `TRACK_DENSITY_THRESHOLD` in [references/mixing-music.md](references/mixing-music.md)). Only activate tracks when density >= their threshold
- How they sound — brightness controls filter cutoff (800Hz→12.8kHz), intensity controls volume and compression drive
- What patterns play — rhythm level selects pattern complexity (basic→syncopated→fills+rolls)
- Chord voicings — harmonic level controls extension depth (triads→7ths→9ths→altered)
- Transitions — use pull/push pairs at section boundaries (riser→impact, filter_close→filter_open, silence→slam)

Micro-engagement: add ear candy every 16 bars (reversed note, stereo moment, one-shot percussion, filter dip). These small details keep attentive listeners discovering new things.

The 2/3 climax rule: the biggest energy moment should occur at approximately 2/3 through the track. This mirrors natural storytelling and creates the most satisfying payoff.

#### Transition Element Rules (for clip boundaries)

When `render_clip()` renders a section, it must handle transitions correctly:
- Risers/builds — rendered in the clip BEFORE the target moment. A riser building into a chorus belongs to the preceding verse clip. The riser's tail naturally extends into the overlap region. Riser sound design: use `sfx_pitch_riser()` (sine sweep ~200Hz→3kHz) as the primary riser — NOT `sfx_noise_riser()` which sounds like harsh fan/air noise. If adding a noise layer, lowpass it at 2kHz and mix it at 20% of the sine sweep level. Use a cubic volume curve (` 3`) so it stays quiet and builds late. Keep total riser volume at 0.1 or lower** — risers should build tension subtly, not dominate the mix
- Impacts/drops — rendered in the clip that STARTS with the impact. The chorus clip starts with the impact on beat 1
- Filter sweeps spanning boundaries — the preceding clip sweeps through its overlap tail. The next clip starts with the filter at the target position. The crossfade handles the blend
- Reverb/delay tails — the 2-second overlap region renders only existing reverb and delay decay (no new notes triggered). This ensures smooth crossfading between clips

#### Mandatory Quality Rules

DSP — Non-Negotiable:
- Always use `sosfilt` with `butter(output='sos')` — NEVER use `lfilter` with `butter` in `ba` form (numerically unstable, causes filter blowups)
- sosfilt zi shape — when using `sosfilt(sos, sig, zi=zi)` with stateful filtering, ALWAYS initialize zi as `np.zeros((sos.shape[0], 2))` — never hardcode the first dimension. The number of SOS sections varies with filter order (order 2 = 1 section, order 4 = 2 sections, etc.). Compute a dummy `sos = butter(order, freq, ..., output='sos')` before the loop to get the correct shape
- Use PolyBLEP oscillators for saw and square waves — naive versions alias badly
- Use Freeverb (8 comb + 4 allpass filters) for reverb — NEVER random delay taps (sounds metallic)
- Output stereo — pan instruments using equal-power panning, keep sub-150Hz centered
- Use per-track compression before mixing, then bus compression on the mix

Click/Pop Prevention — Non-Negotiable (CRITICAL — pops ruin tracks):
- Minimum 2ms attack on ALL envelopes — never use attack < 0.002s, even for percussive sounds. Use a quadratic fade-in for kicks instead of instant onset
- Minimum 15ms release on ALL envelopes — never let a sound end abruptly. Even percussive hits need at least `release=0.015`. Short releases are the #1 cause of audible pops
- Cosine fade at envelope edges — apply a short (2-5ms) cosine window (`0.5 - 0.5 * cos(...)`) at the start AND end of every ADSR/swell envelope to guarantee zero-crossing at boundaries
- Apply a safety fade-out to EVERY sound before place() — after all processing, always apply a 5ms cosine fade-out to the last samples of any sound buffer: `fade_n = min(int(0.005 * SR), len(sig) // 4); sig[-fade_n:] *= 0.5 + 0.5 * np.cos(np.linspace(0, np.pi, fade_n))`. This catches any envelope that didn't fully decay
- Chorus/delay must use linear interpolation — NEVER use integer indexing (`sig[indices.astype(int)]`) for modulated delay lines. Always use `np.interp()` or manual linear interpolation between adjacent samples
- DC-block only with vectorized highpass, not sample-by-sample — the sample-loop DC blocker introduces transients at signal boundaries. Instead use `highpass(sig, 10)` (10Hz HPF) which is stable and pop-free
- Noise/texture MUST use overlap-add at chunk boundaries — when processing noise in chunks (e.g., bandpass sweeps), use overlapping windows with crossfades, NOT hard chunk boundaries. Hard boundaries = audible pops every N seconds. Use `np.hanning(chunk_size)` windows with 50% overlap, or process the entire signal at once
- Vinyl/texture pops must be soft — use Hann window envelopes (not linear ramps), minimum 5ms duration, lowpass filter at 3kHz, and keep amplitude under 0.015. Harsh pops sound like digital errors, not analog warmth
- Kick drums need a 3ms quadratic fade-in — `kick[:fade_n] *= np.linspace(0, 1, fade_n) ** 2` before the exponential decay, to prevent the initial sine sample from starting at non-zero
- Final mix pop-check — after mastering, apply a soft-clip (`np.tanh`) and a final 2ms cosine fade-in/fade-out to the entire stereo output to eliminate any remaining edge pops

Human Feel & Musicality — GUIDING PRINCIPLE:
The #1 goal is music that sounds natural, warm, and enjoyable — something a human would want to play at a party or on a night drive. Every decision should serve this goal. Robotic, mechanical, or harsh-sounding output is a failure. Specifically:
- Favor lower/mid registers over high pitch — root pads in octave 3, arps in octave 3-4 (not 5+), leads in octave 4, bass in octave 1-2. High-pitched synths sound shrill and cheap; warmth lives in the mid-range
- Vary velocity dramatically — ghost notes at 20-35%, accents at 80-100%, everything else 50-70%. Flat velocity = instant robot. Velocity should follow musical phrasing (crescendo/decrescendo within phrases)
- Use swing and groove, not just humanize — every genre needs appropriate swing (see table). On top of swing, add per-instrument timing offsets that create a "pocket" feel. The groove should make you nod your head
- Melodies need rests and breathing room — don't fill every beat. Use 30-40% rests in melodic lines. Real musicians breathe. Space is as important as notes
- Vary patterns across bars — don't copy-paste the same pattern for 64 bars. Add fills, drops, variations every 4-8 bars. Alternate between 2-3 pattern variations. A slight change every 8 bars keeps it human
- Layer for richness, not volume — use 2-3 complementary timbres per voice (saw+triangle, sine+square) at different octaves. This creates depth without harshness
- Filter aggressively — most synth elements should be lowpassed well below their brightest. Warmth = rolling off highs. A 2kHz lowpass on a pad sounds warm; 5kHz sounds thin and digital
- Chord voicings matter — use inversions, spread voicings, and avoid root-position block chords. Drop the 5th an octave, spread notes across 1.5 octaves. This sounds lush instead of MIDI-keyboard

Music — Non-Negotiable:
- Always voice lead between chords — never jump to root position
- Always humanize timing — kick on grid (±2ms), snare slightly late (+5-15ms), hats slightly early (-3-8ms)
- Apply swing appropriate to genre (0.50 straight for EDM, 0.55-0.60 for lo-fi, 0.62-0.67 for jazz)
- Every melodic/harmonic element goes through reverb and/or delay — dry synths sound cheap
- Use ADSR envelopes on everything — no clicks or pops from abrupt starts/stops
- Lowpass filter most elements — raw oscillators sound harsh

Psychoacoustic Quality — Non-Negotiable (see psychoacoustics.md):
- Respect critical bandwidth in voicings — no 3rds below 200Hz, spread bass voicings >7 semitones apart. Close voicings only above 500Hz
- Always layer bass for speaker translation — sub sine (30-60Hz) + harmonic saturation layer (100-300Hz) + click (2-5kHz). Small speakers hear the harmonics, club systems feel the sub
- Target -14 LUFS integrated for streaming. Apply K-weighted loudness measurement, NOT just peak normalization
- Dither when exporting 16-bit — TPDF dither with modified-E noise shaping. Never truncate without dithering
- Frequency slot discipline — each instrument gets a primary frequency band. EQ-cut competing instruments out of each other's slots (see studio-production.md frequency real estate table)

Melody & Hook Quality — Non-Negotiable (see music-science.md):
- Hooks must use the earworm formula — 2-4 bars, mostly stepwise with one distinctive skip, stated 3 times then varied. This is researched, not opinion
- Apply gap-fill after leaps — if melody jumps >5 semitones, reverse direction and step back (80% probability). This is how singable melodies work
- Use appoggiaturas for emotional peaks — stressed dissonance on strong beat resolving stepwise = the #1 physiological chills trigger in music (Sloboda 1991)
- Target 30-40% syncopation for maximum groove (Witek 2014 inverted-U curve). Below 20% = stiff, above 50% = chaotic
- Tension must rise toward each chorus — harmonic rhythm doubles, melody ascends, filter opens, layers stack. The biggest moment at ~75% through the song

Mix — Non-Negotiable:
- Bass and kick get NO reverb — muddies low end
- EQ reverb returns — HPF 200-400Hz, LPF 6-10kHz on all reverb sends
- Use pre-delay on reverb — 50-100ms for vocals (keeps vocal in front), 0-20ms for drums, 20-40ms for pads
- Apply frequency-dependent stereo width — mono below 200Hz, moderate 200-4kHz, wide above 4kHz
- Arrangement must have dynamics — sections that build and strip back, not a flat wall of sound
- Master chain: prefer pedalboard chain (HPF → EQ → Compressor → Gain → Limiter) when available; fallback to numpy chain (HPF 30Hz → bus compression → tape saturation → stereo width → limiter → normalize → fade in/out)
- Add subtle analog warmth — tape saturation on mix bus (drive 1.3, arctangent curve), transformer coloring on channels. Even harmonics = warmth

### Step 6: Render Clips

Both modes produce individual section clips in `{track-name}/sounds/`. The only difference is concurrency.

#### Parallel Mode (default for songs >= 60s)

Spawn one Agent per clip in parallel. Each Agent writes and executes a tiny script that calls the shared `render_clip()`:

```python
# {track-name}/scripts/clip_01_intro.py (generated by Agent)
import sys, os; sys.path.insert(0, os.path.dirname(__file__))
from song_config import *
render_clip(section_idx=0, start_bar=0, num_bars=8, output_path=f'{SOUNDS_DIR}/01_intro.wav')
```

Issue all Agent calls in a single message so they run concurrently. Each clip script is 3-5 lines. Wait for all Agents to complete before proceeding to Step 7.

Each Agent prompt should be:
> "Write and execute `{track-name}/scripts/clip_XX_<name>.py` that imports `song_config` and calls `render_clip(section_idx=N, start_bar=M, num_bars=B, output_path=f'{SOUNDS_DIR}/XX_<name>.wav')`. Run with: `uv run --with numpy --with scipy --with pedalboard --with soundfile --with pretty-midi --with midiutil python3 {track-name}/scripts/clip_XX_<name>.py`"

#### Single-File Mode (opt-out or songs < 60s)

Generate one script that loops through all sections sequentially using the same `render_clip()`:

```python
# {track-name}/scripts/render_all.py
import sys, os; sys.path.insert(0, os.path.dirname(__file__))
from song_config import *

bar_cursor = 0
for idx, section in enumerate(SECTIONS):
    clip_path = f'{SOUNDS_DIR}/{idx+1:02d}_{section["name"]}.wav'
    render_clip(section_idx=idx, start_bar=bar_cursor, num_bars=section['bars'], output_path=clip_path)
    print(f'  Rendered {clip_path}')
    bar_cursor += section['bars']
print(f'All {len(SECTIONS)} clips rendered to {SOUNDS_DIR}/')
```

Same `render_clip()`, same output format, same `sounds/` folder — just sequential.

#### Clip Assignment Rules (both modes)

- One clip per section (intro, verse1, chorus1, verse2, chorus2, bridge, outro)
- Sections < 4 bars merge with the adjacent section into one clip
- Each clip renders its assigned bars plus `OVERLAP_SECONDS` (0.5s) of tail for crossfading
- Clips are named `XX_<section_name>.wav` (e.g., `01_intro.wav`, `02_verse1.wav`) and saved to `{track-name}/sounds/`

### Step 7: Stitch & Master (`stitch.py`)

Generate and run `{track-name}/scripts/stitch.py` — this is the same for both modes since both produce clips in `sounds/`:

```python
# {track-name}/scripts/stitch.py
import sys, os; sys.path.insert(0, os.path.dirname(__file__))
from song_config import TRACK_NAME, TRACK_DIR, SOUNDS_DIR, OUTPUT_FILE, SR
import numpy as np, glob
try:
    import soundfile as sf
    USE_SF = True
except ImportError:
    from scipy.io import wavfile
    USE_SF = False

CROSSFADE_MS = 500  # crossfade duration — MUST equal OVERLAP_SECONDS * 1000

# 1. Load all clips in sorted order
clips = sorted(glob.glob(f'{SOUNDS_DIR}/*.wav'))
audio_clips = []
for clip_path in clips:
    if USE_SF:
        data, sr = sf.read(clip_path, dtype='float64')
    else:
        sr, data = wavfile.read(clip_path)
        data = data.astype(np.float64) / 32768.0
    audio_clips.append(data)
    print(f'  Loaded {clip_path}: {len(data)/sr:.1f}s')

# 2. Equal-power crossfade and concatenate
crossfade_n = int(CROSSFADE_MS / 1000.0 * SR)
fade_out = np.sqrt(np.linspace(1, 0, crossfade_n)).reshape(-1, 1)
fade_in = np.sqrt(np.linspace(0, 1, crossfade_n)).reshape(-1, 1)

output = audio_clips[0]
for i in range(1, len(audio_clips)):
    # Blend overlap region with equal-power crossfade
    overlap = output[-crossfade_n:] * fade_out + audio_clips[i][:crossfade_n] * fade_in
    output = np.concatenate([output[:-crossfade_n], overlap, audio_clips[i][crossfade_n:]])

# 3. Apply master chain to FULL assembled audio
#    Pedalboard: HPF 30Hz → EQ → Compressor → Gain → Limiter
#    (or numpy fallback — see mastering-and-export.md)

# 4. Fade-in (500ms) and fade-out (1s)
# 5. Safety: soft-clip (np.tanh) + 2ms cosine fade edges
# 6. Export as {track-name}/{track-name}.wav (24-bit preferred)
print(f'Exported {OUTPUT_FILE}')
```

Seamless stitching: `CROSSFADE_MS` MUST equal `OVERLAP_SECONDS * 1000` so the crossfade window consumes the entire overlap region. With 0.5s overlap and 500ms crossfade, there is zero dead space between clips — the equal-power `sqrt()` curves blend the tail decay directly into the next clip's start. If these values don't match (e.g., 200ms crossfade on a 2s overlap), you get ~1.8s of near-silence between sections.

Critical: The master chain (HPF → bus comp → saturation → limiter) is applied ONLY here on the full assembled audio — never in individual clips. This ensures cohesive dynamics and EQ across the entire track.

Execute with:
```
uv run --with numpy --with scipy --with pedalboard --with soundfile --with pretty-midi --with midiutil python3 {track-name}/scripts/stitch.py
```

If it fails, fix the error and re-run. Common issues:
- Array shape mismatches in `place()` — always clip to buffer length
- Filter instability — use `output='sos'` with `sosfilt` (never `lfilter`)
- Memory — process Freeverb per-track on mono, then pan to stereo after

### Step 8: Validate Output Quality

After stitching, validate the final output .wav. Run the validation pipeline from [quality-validation.md](../../shared/references/quality-validation.md) with genre-specific targets from [references/mixing-music.md](references/mixing-music.md):

1. Level check — peak level, RMS, integrated LUFS (target: -14 LUFS for streaming)
2. Clipping detection — count samples at digital ceiling; flag if >0.1% of total samples
3. Frequency balance — measure energy in sub/low/mid/high/air bands, compare to genre targets
4. Stereo check — correlation coefficient (should be >0.3, <1.0), stereo width measurement
5. Energy map comparison — verify the loudest section matches the planned climax, verify intro/outro are quieter than peak
6. Crossfade check — listen for audible seams between clips (if found, increase CROSSFADE_MS or check energy continuity)

If issues are found, apply auto-fixes (re-normalize, soft-limit clipping, trim silence). Re-run validation after fixes.

### Step 9: Present the Result

Tell the user:
- Output filename and duration (e.g., `{track-name}/{track-name}.wav`)
- Key, BPM, scale/mode, genre/style
- Full energy map — section-by-section breakdown with all 5 energy dimensions and emotional arc
- Tension/release moments — where the pulls and pushes happen
- Key production techniques used
- Engagement highlights — ear candy moments, transitions, the climax point
- What to listen for at each section
- Project folder structure — `{track-name}/sounds/` contains individual clips, `{track-name}/scripts/` contains all generation code
- Note that individual clips in `sounds/` can be imported into a DAW for remixing

### Step 10: Iterate on User Feedback

When the user requests changes ("make it darker", "drums too loud", "change key to minor"), consult [iteration-core.md](../../shared/references/iteration-core.md) for the workflow and [references/iteration-music.md](references/iteration-music.md) for music-specific refinement mappings:

1. Read `{track-name}/scripts/song_config.py` — identify the specific parameter(s) that control the requested change
2. Map the request — use `REFINEMENT_MAP` to find exact parameters and actions
3. Determine scope — does the change affect `song_config.py` (global: key, BPM, instrument timbre) or only specific clips (section-specific: energy, patterns)?
4. Make surgical edits — modify only the relevant parameters in `song_config.py`, preserving everything else
5. Re-render only affected clips — if the change is global (e.g., instrument timbre), re-render all clips. If section-specific (e.g., "make the chorus brighter"), re-render only that clip. Then re-run `stitch.py`
6. Validate — run Step 8 validation on the new output
7. Version the output — save as `{track-name}/{track-name}_v2.wav` (keep v1 for A/B comparison)

Do NOT regenerate `song_config.py` from scratch unless the user explicitly asks for a complete redo or the change requires >5 parameter modifications. The clip-based architecture makes iteration faster — changing one section only requires re-rendering one clip in `sounds/` + re-stitching.

## Genre-Specific Guidelines

Consult [references/genre-guide.md](references/genre-guide.md) for the BPM, mode, scale, swing, core sounds, and signature effects for each genre. Load it when producing a specific genre to get the parameters right.

Consult the reference files below for detailed synthesis patterns, scales, chord progressions, and instrument recipes.

## Important Notes

- Project folder structure — each track gets its own folder: `{track-name}/scripts/` for code, `{track-name}/sounds/` for clips, `{track-name}/{track-name}.wav` for the final output. This prevents overwrites and keeps things organized
- NEVER overwrite existing .wav files without asking — version outputs (e.g., `_v2.wav`)
- For genres you're less familiar with, do MORE research (6-8 searches)
- Scripts must be self-contained aside from `song_config.py` — no external sample files
- Always use `uv run --with numpy --with scipy --with pedalboard --with soundfile --with pretty-midi --with midiutil` to execute
- All scripts run from the project root, not from inside the track folder
- If the user says "make it longer", increase TOTAL_BARS proportionally and add/extend sections
- If the user says "make it more [X]", research what [X] means in production terms
- For iterative refinement, always follow the Step 10 workflow — read `{track-name}/scripts/song_config.py`, modify surgically, re-render affected clips, re-stitch
- `{track-name}/sounds/` contains individual section clips — users can remix or import these into a DAW
- Master chain is applied ONLY in `stitch.py` on the full assembled audio, never in individual clips
- Both modes share `song_config.py` — parallel and single-file produce identical output quality with zero code duplication
- `render_clip()` is the single source of truth for how any section of the song is rendered. All quality rules, DSP, effects, and mixing happen inside this function

## Hybrid Rendering

`song_config.py` instrument builders can use two rendering approaches — choose based on the use case:

numpy synthesis (default) — use for all custom/synthesized sounds:
- PolyBLEP oscillators, FM, additive, granular, spectral, physical modeling
- Full control over every parameter, no external dependencies beyond numpy/scipy
- Best for: electronic genres, sound design, custom timbres, SFX

FluidSynth + SoundFonts (optional) — use when realistic acoustic instruments are needed:
- Render MIDI via pretty_midi with FluidSynth for sampled piano, strings, brass, woodwinds
- Requires FluidSynth system install + .sf2 SoundFont file
- Best for: classical, jazz, orchestral, film score, realistic acoustic parts

Hybrid approach — combine both in `song_config.py` instrument builders:
```python
# Synthesize electronic parts with numpy
kick = build_kick(...)
synth_pad = build_pad(...)

# Render acoustic parts from MIDI via FluidSynth (when available)
try:
    import pretty_midi
    pm = pretty_midi.PrettyMIDI(initial_tempo=BPM)
    piano = pretty_midi.Instrument(program=0)  # Acoustic Grand Piano
    # Add notes...
    piano_audio = pm.fluidsynth(fs=SR, sf2_path='path/to/soundfont.sf2')
except:
    piano_audio = build_piano(...)  # Fallback to numpy synthesis
```

## MIDI Export

Optionally export a MIDI file alongside the WAV for DAW editing. Define `export_midi()` in `song_config.py` and call it from `stitch.py` after assembly, or from `render_clip()` to export per-section MIDI:

```python
from midiutil import MIDIFile

def export_midi(filename, tracks, bpm):
    """Export MIDI file from track data. Each track: {name, channel, notes: [(pitch, start_beat, dur_beats, vel)]}"""
    midi = MIDIFile(len(tracks))
    for i, track in enumerate(tracks):
        midi.addTrackName(i, 0, track['name'])
        midi.addTempo(i, 0, bpm)
        for pitch, start, dur, vel in track['notes']:
            midi.addNote(i, track.get('channel', 0), pitch, start, dur, vel)
    with open(filename, 'wb') as f:
        midi.writeFile(f)
    print(f"Wrote {filename}: {len(tracks)} tracks at {bpm} BPM")
```

## Additional Resources

### Shared Audio (generic DSP — `../../shared/references/`)
- [dsp-core.md](../../shared/references/dsp-core.md) — Core DSP primitives (PolyBLEP oscillators, sosfilt filters, ADSR envelopes, helpers)
- [effects.md](../../shared/references/effects.md) — Effects processing (Freeverb, delay, chorus, phaser, compression, distortion, lo-fi)
- [psychoacoustics.md](../../shared/references/psychoacoustics.md) — Equal-loudness, critical bands, auditory masking, consonance/dissonance, LUFS metering, bass enhancement
- [studio-production.md](../../shared/references/studio-production.md) — Pro EQ, advanced compression, stereo imaging, saturation types, reverb design, analog emulation
- [advanced-synthesis-dsp.md](../../shared/references/advanced-synthesis-dsp.md) — MinBLEP oscillators, physical modeling, FM deep dive, analog filter models, granular, wavetable
- [synthesis-techniques.md](../../shared/references/synthesis-techniques.md) — Granular, physical modeling, modal, vector, formant, vocoder, convolution reverb, wavetable
- [spectral-processing.md](../../shared/references/spectral-processing.md) — STFT, spectral freeze/morph/gate/blur, phase vocoder, Paulstretch
- [sfx-synthesized.md](../../shared/references/sfx-synthesized.md) — Risers, impacts, whooshes, glitch, laser, explosion, tape stop, Doppler, shimmer/reverse reverb
- [environmental-and-vocal.md](../../shared/references/environmental-and-vocal.md) — Rain, wind, thunder, ocean, auto-tune, harmonizer, vocal doubling, lo-fi chain
- [mastering-and-export.md](../../shared/references/mastering-and-export.md) — Pedalboard master chain, multiband compression, mid/side, transient shaping, soundfile export
- [production-techniques.md](../../shared/references/production-techniques.md) — Multiband comp, transient shaper, parallel comp, mid/side, stereo widener, sidechain envelope
- [mixing-core.md](../../shared/references/mixing-core.md) — Stereo mix pipeline, panning math, sidechain, EQ carving, numpy master chain, export
- [quality-validation.md](../../shared/references/quality-validation.md) — Post-generation .wav analysis: LUFS metering, clipping detection, frequency balance, stereo correlation
- [iteration-core.md](../../shared/references/iteration-core.md) — Refinement workflow, surgical edit patterns, preserve-vs-regenerate rules, version management
- [automation-core.md](../../shared/references/automation-core.md) — Filter sweep templates, LFO systems, modulation depth scaling
- [energy-framework.md](../../shared/references/energy-framework.md) — Multi-dimensional energy system, tension/release cycles, smoothstep interpolation

### Music Production (music-specific — `references/`)

#### Core
- [references/energy-music.md](references/energy-music.md) — Emotional arcs, composition plans, engagement techniques, 2/3 climax rule, golden rules (START HERE for arrangement)
- [references/instruments-music.md](references/instruments-music.md) — Instrument synthesis recipes (drums, synths, keys, strings, brass, FM)
- [references/music-theory.md](references/music-theory.md) — Music theory (scales, chords, progressions, voice leading, melody, harmony, song forms)
- [references/key-emotion-and-mood.md](references/key-emotion-and-mood.md) — Key-emotion mappings, tempo-mood matrix, instrumentation-emotion, mood_to_params()
- [references/music-science.md](references/music-science.md) — Information theory of melody, hook/earworm design, groove science, tension curves

#### Mixing & Iteration
- [references/mixing-music.md](references/mixing-music.md) — Instrument panning, density thresholds, genre LUFS targets, reverb rules, EQ guide
- [references/iteration-music.md](references/iteration-music.md) — 30+ refinement mappings (darker/brighter/etc.), refinement chains, music parameter locations
- [references/automation-music.md](references/automation-music.md) — Per-genre automation recommendations, music-specific LFO assignments

#### Rhythm & Patterns
- [references/rhythm-and-groove.md](references/rhythm-and-groove.md) — Drum patterns by genre, swing math, humanization, Euclidean rhythms, fills
- [references/drum-patterns-world.md](references/drum-patterns-world.md) — Latin, Afro-Cuban, Brazilian, Middle Eastern, Indian taal patterns
- [references/drum-patterns-breaks.md](references/drum-patterns-breaks.md) — Breakbeats (Amen, Funky Drummer, Apache), ghost notes, fills, additional patterns
- [references/chord-progressions.md](references/chord-progressions.md) — Neo-soul, gospel, film score, game music, advanced progressions, bass & arpeggio patterns

#### Melody, Structure & Genre
- [references/melody-and-structure.md](references/melody-and-structure.md) — Melody data, contour archetypes, riff patterns, song templates, transition techniques
- [references/genre-guide.md](references/genre-guide.md) — Deep genre profiles: 20 genres with BPM, chord progressions, drum patterns, mixing philosophy (consult for any genre)
- [references/lyric-writing.md](references/lyric-writing.md) — Rhyme schemes, meter, songwriting techniques, section roles, vocal range guide
- [references/modern-trends.md](references/modern-trends.md) — 2024-2026 production trends, genre blending, mood-first production

#### Analysis
- [references/reference-track-analysis.md](references/reference-track-analysis.md) — Audio file analysis: BPM detection, key detection, spectral profiling, section detection

### Examples
- [examples/](examples/) — Example outputs showing expected format and quality
