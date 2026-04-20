# Music-Specific Mixing Reference

Music-specific panning, density gating, drum/chord selection, engagement elements, EQ guidelines, arrangement patterns, and genre validation targets. Extracted from shared mixing and quality-validation references for music production use.

## Instrument Panning Table

| Instrument | Pan (0=L, 0.5=C, 1=R) | Notes |
|-----------|------------------------|-------|
| Kick | 0.50 (center) | Always center |
| Bass | 0.50 (center) | Always center |
| Sub bass | 0.50 (center) | Always center |
| Snare | 0.48-0.52 | Nearly center |
| Lead/Melody | 0.45-0.55 | Near center |
| Hi-hat | 0.35-0.45 | Slightly left |
| Ride | 0.55-0.65 | Slightly right |
| Pad L | 0.25-0.35 | Left |
| Pad R | 0.65-0.75 | Right |
| Arp | 0.30-0.70 | Varies |
| Strings L | 0.20-0.30 | Left |
| Strings R | 0.70-0.80 | Right |
| FX/Perc | 0.15-0.85 | Wide spread |

Rule: keep everything below 150Hz centered (sub bass, kick fundamental). Wide stereo only for mid and high frequencies.

## Track Density Gating

```python
# DENSITY -> track gating (in the main mix loop)
TRACK_DENSITY_THRESHOLD = {
    'pad': 1, 'atmosphere': 1,
    'chord': 3, 'melody': 3, 'arp': 3,
    'kick': 4, 'bass': 4, 'hihat': 4, 'snare': 4, 'ride': 4,
    'strings': 5, 'counter_mel': 5, 'perc': 5,
    'clap': 7, 'lead': 7,
    'fx': 9, 'doubled': 9, 'harmony': 9,
}

def is_track_active(track_name, bar, bar_energy):
    """Check if track should sound in this bar based on density."""
    threshold = TRACK_DENSITY_THRESHOLD.get(track_name, 5)
    return bar_energy[bar]['density'] >= threshold
```

> Cross-reference: This dict also appears in [energy-music.md](energy-music.md) for context within the energy system.

## Pattern & Voicing Selection

```python
# RHYTHM -> pattern selection (in drum builders)
def select_drum_pattern(bar, bar_energy):
    """Select pattern complexity based on rhythm energy."""
    r = bar_energy[bar]['rhythm']
    if r <= 3:   return 'basic'       # kick + hat only
    elif r <= 6: return 'standard'    # kick + snare + hat + ride
    elif r <= 8: return 'syncopated'  # + ghost notes + syncopation
    else:        return 'complex'     # + fills + rolls + polyrhythm

# HARMONIC -> chord voicing depth (in chord builders)
def select_chord_voicing(bar, bar_energy):
    """Select chord richness based on harmonic energy."""
    h = bar_energy[bar]['harmonic']
    if h <= 3:   return 'triad'    # Root + 3rd + 5th
    elif h <= 6: return '7th'      # + 7th, inversions
    elif h <= 8: return '9th'      # + 9th, spread voicings
    else:        return 'extended'  # + 11th/13th, altered, chromatic passing
```

## Engagement Elements (Micro-Tension in the Mix)

Add these automatically based on bar position to keep listeners engaged:

```python
def add_engagement_elements(mix_left, mix_right, bar, bar_energy, bar_samples, sr=SR):
    """Add micro-tension elements at phrase boundaries."""
    start = int(bar * bar_samples)
    end = min(int((bar + 1) * bar_samples), len(mix_left))

    # Drum fill every 4-8 bars (probability increases with rhythm energy)
    if bar % 4 == 3:
        fill_prob = 0.05 + 0.25 * (bar_energy[bar]['rhythm'] / 10.0)
        if np.random.random() < fill_prob:
            pass  # Insert fill from fill builder

    # Reverse crash before section transitions
    # (check if next bar has different section energy)
    if bar + 1 < len(bar_energy):
        intensity_jump = abs(bar_energy[bar+1]['intensity'] - bar_energy[bar]['intensity'])
        if intensity_jump >= 3:
            pass  # Insert reverse crash in last beat

    # Ear candy every 16 bars (one-shot FX, stereo moment, etc.)
    if bar % 16 == 15 and bar_energy[bar]['density'] >= 5:
        pass  # Insert subtle ear candy (reversed note, ping-pong delay burst, etc.)

    return mix_left, mix_right
```

## Per-Instrument EQ Guidelines

| Instrument | Cut | Boost | Notes |
|-----------|-----|-------|-------|
| Kick | HPF 30Hz, cut 300-500Hz | Boost 60-80Hz (sub), 3-5kHz (click) | Scoop mids for clarity |
| Snare | HPF 80Hz | Boost 200Hz (body), 2-4kHz (crack) | |
| Hi-hat | HPF 300-500Hz | | Remove low-end bleed |
| Bass | HPF 30Hz, cut 200-300Hz | Boost 60-100Hz, 800Hz-1kHz (presence) | |
| Pad | HPF 200Hz, LPF 8kHz | | Stay out of bass/treble extremes |
| Piano/Keys | HPF 80Hz | Boost 2-4kHz (presence) | |
| Vocals/Lead | HPF 80-100Hz | Boost 3-5kHz (presence), 10kHz (air) | |
| Reverb return | HPF 200-400Hz, LPF 6-10kHz | | ALWAYS EQ reverb returns |

### EQ_GUIDE Dict

```python
EQ_GUIDE = {
    'kick': {
        'sub':    (30, 60,    'boost 3-6dB for sub weight'),
        'punch':  (80, 200,   'body and punch zone'),
        'mud':    (250, 500,  'cut 3-6dB — boxiness lives here'),
        'click':  (2000, 4000,'boost 2-4dB for beater attack'),
        'air':    (6000, 10000,'gentle cut — not needed on kick'),
        'hpf': 30,
    },
    'snare': {
        'body':   (150, 400,  'fundamental resonance, boost 2-3dB'),
        'honk':   (400, 800,  'cut 2-4dB if boxy or ringy'),
        'crack':  (2000, 4000,'boost 2-4dB for snap and attack'),
        'wire':   (6000, 10000,'boost 1-2dB for sizzle and wire'),
        'hpf': 80,
    },
    'hihat': {
        'hpf':    (200, 400,  'aggressive HPF — no low content in hats'),
        'body':   (400, 1000, 'not needed — cut if present'),
        'brightness': (6000, 15000, 'main energy zone'),
        'harshness':  (3000, 5000,  'cut 2-3dB if piercing'),
    },
    'bass_guitar': {
        'sub':    (40, 80,    'fundamental weight'),
        'body':   (80, 200,   'warmth and fullness'),
        'mud':    (250, 500,  'cut 2-4dB for clarity, especially 200-220Hz'),
        'attack': (1200, 1500,'finger/pick definition — boost 2dB for note readability'),
        'presence':(2000, 4000,'string noise, grind — boost sparingly'),
        'hpf': 30,
    },
    'guitar_electric': {
        'body':   (80, 250,   'low-end fullness'),
        'mud':    (300, 500,  'cut 2-4dB — boxiness'),
        'honk':   (500, 1000, 'nasal zone — often cut 2-3dB'),
        'presence':(2000, 5000,'pick attack, bite — boost for solos'),
        'air':    (6000, 12000,'shimmer, open sound'),
        'hpf': 80,
    },
    'guitar_acoustic': {
        'body':   (80, 200,   'warmth, fullness'),
        'boom':   (200, 400,  'cut if close-miked or boomy'),
        'clarity':(2000, 5000,'string detail and pick definition'),
        'air':    (8000, 14000,'sparkle and breathiness'),
        'hpf': 80,
    },
    'piano': {
        'fullness':(80, 120,  'low register weight'),
        'body':   (200, 500,  'warm midrange'),
        'clarity':(1000, 3000,'note definition'),
        'brightness':(3000, 5000,'presence and sparkle'),
        'attack': (5000, 6000,'hammer attack — boost 1-2dB for definition'),
        'hpf': 60,
    },
    'vocals': {
        'hpf':    (80, 120,   'ALWAYS HPF vocals'),
        'body':   (200, 400,  'chest resonance, warmth — boost 1-2dB for thin vocals'),
        'mud':    (250, 500,  'cut 2-3dB if muffled or muddy'),
        'intelligibility': (800, 1500, 'diction clarity zone — critical for vocals cutting through'),
        'presence':(3000, 5000,'cut-through, forward sound — boost 2-4dB'),
        'sibilance':(5000, 8000,'de-ess zone — cut if harsh with narrow Q'),
        'air':    (10000, 16000,'boost 1-3dB shelf for breathiness and openness'),
    },
    'lead_synth': {
        'body':   (200, 800,  'thickness and weight'),
        'presence':(1000, 4000,'main energy and character'),
        'harshness':(3000, 5000,'tame 2-3dB if fatiguing'),
        'air':    (8000, 14000,'brightness and shimmer'),
        'hpf': 100,
    },
    'pad_synth': {
        'body':   (300, 800,  'core warmth'),
        'mud':    (200, 400,  'cut 2-4dB to avoid masking bass'),
        'lpf':    (6000, 10000,'ALWAYS LPF pads for warmth — keeps them in the background'),
        'hpf': 200,
    },
    'strings': {
        'body':   (200, 500,  'richness and warmth'),
        'nasal':  (800, 1500, 'cut 2-3dB if thin or nasal'),
        'presence':(2000, 5000,'bow articulation and detail'),
        'air':    (8000, 14000,'high harmonic shimmer — shelf boost 1-2dB'),
        'hpf': 80,
    },
    'brass': {
        'body':   (200, 500,  'fundamental warmth'),
        'honk':   (500, 1000, 'characteristic brass tone — careful with boosts'),
        'blare':  (1000, 3000,'cut 2-3dB if too aggressive'),
        'presence':(3000, 6000,'brilliance and cut-through'),
        'hpf': 80,
    },
    'saxophone': {
        'body':   (120, 240,  'fullness and warmth'),
        'harshness':(1000, 2000,'cut if shrill'),
        'reed':   (5000, 7000,'reed noise — cut for smooth, boost for gritty'),
        'hpf': 100,
    },
}
```

## Arrangement Patterns

### Section Map (Multi-Dimensional)

Define 5-dimension energy levels per section (see [energy-music.md](energy-music.md) for full energy system):

```python
# Example: 64-bar pop arrangement with composition plan
SECTIONS = [
    {'name': 'Intro',      'bars': 8,
     'energy': {'intensity': 3, 'density': 2, 'rhythm': 2, 'harmonic': 4, 'brightness': 3},
     'positive_styles': ['atmospheric', 'spacious'],
     'negative_styles': ['full beat', 'lead melody'],
     'transition_out': 'filter_open'},
    {'name': 'Verse 1',    'bars': 16,
     'energy': {'intensity': 5, 'density': 5, 'rhythm': 5, 'harmonic': 5, 'brightness': 5},
     'transition_in': 'impact', 'transition_out': 'riser'},
    {'name': 'Chorus 1',   'bars': 16,
     'energy': {'intensity': 8, 'density': 8, 'rhythm': 7, 'harmonic': 8, 'brightness': 9},
     'tension': 'PUSH'},
    {'name': 'Breakdown',  'bars': 8,
     'energy': {'intensity': 4, 'density': 3, 'rhythm': 2, 'harmonic': 6, 'brightness': 4}},
    {'name': 'Final Chorus','bars': 12,
     'energy': {'intensity': 10, 'density': 10, 'rhythm': 8, 'harmonic': 9, 'brightness': 10},
     'tension': 'PUSH — climax at 2/3 point'},
    {'name': 'Outro',      'bars': 4,
     'energy': {'intensity': 3, 'density': 2, 'rhythm': 2, 'harmonic': 4, 'brightness': 2},
     'positive_styles': ['callback to intro']},
]

# Build per-bar lookup:
bar_energy = build_bar_energy(SECTIONS)
```

### Element Introduction by Density Level

| Density | Elements Present |
|---------|-----------------|
| 1-2 | Pad, atmosphere only — NO drums, NO bass (atmospheric intro/outro) |
| 3 | + Chords, melody, arp (harmonic content but still no drums) |
| 4-5 | + Kick, bass, hi-hat, snare, ride, strings (verse energy — beat enters) |
| 6 | + Counter-melody, percussion layers |
| 7-8 | + Lead, clap (chorus energy — full arrangement) |
| 9-10 | Everything + doubled parts, FX, harmony, widest stereo |

### Variation Rules
- Every 4 bars: small variation (hi-hat pattern change, fill, velocity shift)
- Every 8 bars: medium variation (drum pattern B, new bass note, filter dip)
- Every 16 bars: significant change (new section, element add/remove, ear candy)
- NEVER copy-paste the same pattern for more than 8 bars without variation
- Contrast ratio: peaks and valleys should differ by 4+ intensity points

## Genre Validation Targets (GENRE_TARGETS)

Loudness and dynamics targets per genre. LUFS values follow streaming platform standards (-14 LUFS normalized), with genre-specific tolerances for artistic intent.

```python
GENRE_TARGETS = {
    'electronic': {
        'lufs_target': -9,       # Club-ready, loud
        'lufs_tolerance': 2,
        'crest_factor_range': (4, 10),
        'max_peak_dbtp': -0.5,
        'spectral_profile_key': 'electronic',
    },
    'lo-fi': {
        'lufs_target': -14,
        'lufs_tolerance': 3,
        'crest_factor_range': (6, 14),
        'max_peak_dbtp': -1.0,
        'spectral_profile_key': 'lo-fi',
    },
    'acoustic': {
        'lufs_target': -16,
        'lufs_tolerance': 2,
        'crest_factor_range': (10, 20),
        'max_peak_dbtp': -1.0,
        'spectral_profile_key': 'acoustic',
    },
    'hip-hop': {
        'lufs_target': -10,
        'lufs_tolerance': 2,
        'crest_factor_range': (5, 12),
        'max_peak_dbtp': -0.5,
        'spectral_profile_key': 'hip-hop',
    },
    'metal': {
        'lufs_target': -10,
        'lufs_tolerance': 2,
        'crest_factor_range': (4, 8),
        'max_peak_dbtp': -0.3,
        'spectral_profile_key': 'metal',
    },
    'jazz': {
        'lufs_target': -16,
        'lufs_tolerance': 3,
        'crest_factor_range': (12, 22),
        'max_peak_dbtp': -1.0,
        'spectral_profile_key': 'jazz',
    },
    'classical': {
        'lufs_target': -18,
        'lufs_tolerance': 4,
        'crest_factor_range': (14, 28),
        'max_peak_dbtp': -1.0,
        'spectral_profile_key': 'classical',
    },
    'pop': {
        'lufs_target': -14,
        'lufs_tolerance': 2,
        'crest_factor_range': (6, 12),
        'max_peak_dbtp': -0.5,
        'spectral_profile_key': 'pop',
    },
    'rnb': {
        'lufs_target': -12,
        'lufs_tolerance': 2,
        'crest_factor_range': (6, 14),
        'max_peak_dbtp': -0.5,
        'spectral_profile_key': 'rnb',
    },
}
```

## Genre Spectral Targets (GENRE_SPECTRAL_TARGETS)

Relative band levels in dB (referenced to mid band = 0 dB):

```python
GENRE_SPECTRAL_TARGETS = {
    'lo-fi':      {'sub': -2, 'low': 1, 'mid': 0, 'high': -5, 'air': -10},
    'metal':      {'sub': -1, 'low': 2, 'mid': -2, 'high': 3, 'air': -1},
    'hip-hop':    {'sub': 4, 'low': 2, 'mid': 0, 'high': -2, 'air': -5},
    'jazz':       {'sub': -4, 'low': 0, 'mid': 0, 'high': -1, 'air': -3},
    'classical':  {'sub': -5, 'low': -1, 'mid': 0, 'high': -1, 'air': -3},
    'pop':        {'sub': 0, 'low': 1, 'mid': 0, 'high': 1, 'air': -2},
    'rnb':        {'sub': 3, 'low': 2, 'mid': 0, 'high': -1, 'air': -4},
    'reggaeton':  {'sub': 3, 'low': 3, 'mid': -1, 'high': 0, 'air': -3},
    'country':    {'sub': -5, 'low': 0, 'mid': 0, 'high': 1, 'air': -2},
}
```
