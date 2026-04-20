# Melody Data & Song Structure Templates

Melody generation data, contour archetypes, riff patterns, song structure templates, transition techniques, and JSON schemas.

## Interval Weights (for probabilistic generation)

```python
MELODY_INTERVAL_WEIGHTS = {
    # interval_semitones: (probability, direction_bias)
    # direction_bias: 0.5=equal, >0.5=favor up, <0.5=favor down
    0:  (0.10, 0.50),   # unison
    1:  (0.13, 0.52),   # minor 2nd
    2:  (0.17, 0.53),   # major 2nd (most common melodic motion)
    3:  (0.07, 0.50),   # minor 3rd
    4:  (0.05, 0.50),   # major 3rd
    5:  (0.04, 0.50),   # perfect 4th
    7:  (0.03, 0.50),   # perfect 5th
    8:  (0.01, 0.45),   # minor 6th (usually down)
    9:  (0.01, 0.45),   # major 6th (usually down)
    12: (0.01, 0.55),   # octave
}

POST_SKIP_RULES = {
    'ascending_skip_gt4':  {'descend_stepwise': 0.85, 'continue': 0.15},
    'descending_skip_gt4': {'ascend_stepwise': 0.80, 'continue': 0.20},
    'skip_skip':           {'change_direction': 0.70, 'continue': 0.30},
}
```

## Melody Contour Archetypes

```python
MELODY_CONTOURS = {
    # Values 0.0-1.0 representing pitch height over 8 time positions
    'arch':           [0.3, 0.5, 0.7, 0.9, 1.0, 0.8, 0.5, 0.3],  # Rise then fall
    'inverted_arch':  [0.8, 0.5, 0.3, 0.2, 0.1, 0.3, 0.6, 0.8],  # Dip then recover
    'ascending':      [0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9],  # Steady climb
    'descending':     [0.9, 0.8, 0.7, 0.6, 0.5, 0.4, 0.3, 0.2],  # Steady fall
    'plateau':        [0.3, 0.5, 0.8, 0.8, 0.8, 0.8, 0.5, 0.3],  # Hold at peak
    'zigzag':         [0.3, 0.7, 0.4, 0.8, 0.3, 0.9, 0.5, 0.3],  # Oscillating
    'delayed_climax': [0.3, 0.3, 0.4, 0.5, 0.5, 0.8, 1.0, 0.4],  # Peak at 2/3
    'question':       [0.3, 0.5, 0.8, 0.6],  # Half phrase, ends up (tension)
    'answer':         [0.7, 0.5, 0.3, 0.2],  # Half phrase, ends down (resolution)
}
```

## Riff Patterns (semitone offsets from root)

```python
RIFF_PATTERNS = {
    'blues':       [0, 0, 3, 5, 6, 5, 3, 0],      # Blue note (b5) approach
    'rock_power':  [0, 0, 7, 7, 5, 5, 0, 0],       # Power chord roots
    'funk':        [0, 0, 10, 0, 7, 0, 5, 3],       # Syncopated, b7 emphasis
    'metal_gallop':[0, 0, 0, 0, 0, 0, 7, 5],        # Gallop rhythm, power intervals
    'edm_lead':    [0, 3, 5, 7, 5, 3, 0, -2],       # Minor scale up-down
    'surf':        [0, 4, 7, 12, 7, 4, 0, -5],      # Arpeggiated, reverb-drenched
    'middle_east': [0, 1, 4, 5, 7, 8, 11, 12],      # Phrygian dominant ascending
    'jazz_bebop':  [0, 2, 4, 5, 7, 9, 10, 11],      # Bebop dominant scale run
}
```

## Cross-Cultural Pentatonic Patterns

```python
PENTATONIC_SCALES = {
    'blues_box':       [0, 3, 5, 7, 10],        # Minor pentatonic (universal blues)
    'major_pent':      [0, 2, 4, 7, 9],          # Major pentatonic (folk, country)
    'chinese_gong':    [0, 2, 4, 7, 9],          # Same intervals, different context
    'japanese_miyako': [0, 1, 5, 7, 8],          # In-sen scale variant, dark
    'japanese_yo':     [0, 2, 5, 7, 9],          # Bright Japanese folk
    'west_african':    [0, 2, 4, 7, 9],          # Widespread in West African music
    'celtic':          [0, 2, 4, 7, 9],          # Irish/Scottish folk
    'andean':          [0, 3, 5, 7, 10],         # South American folk
    'indian_bhupali':  [0, 2, 4, 7, 9],          # Raga Bhupali (Carnatic: Mohanam)
}
```

## Call and Response

```python
CALL_RESPONSE_TYPES = {
    'exact_repeat': {
        'description': 'Response = exact copy of call',
        'transform': lambda notes: notes,
    },
    'transposed': {
        'description': 'Response = call transposed up a 4th (5 semitones)',
        'transform': lambda notes: [(n + 5, d, v) for n, d, v in notes],
    },
    'inverted': {
        'description': 'Response = call with intervals mirrored around axis',
        'transform': lambda notes: [(notes[0][0] - (n - notes[0][0]), d, v) for n, d, v in notes],
    },
    'complementary': {
        'description': 'Call ends on tension (non-tonic), response ends on resolution (tonic/5th)',
        'call_end': 'scale_degree_2_or_7',
        'response_end': 'scale_degree_1_or_5',
    },
}
```

## Motif Development Techniques

```python
MOTIF_DEVELOPMENT = {
    'augmentation':     'Double all note durations',
    'diminution':       'Halve all note durations',
    'inversion':        'Mirror intervals: up becomes down, down becomes up',
    'retrograde':       'Play notes in reverse order',
    'retrograde_inv':   'Retrograde + inversion combined',
    'sequence_real':    'Repeat motif at different pitch, preserving exact intervals',
    'sequence_tonal':   'Repeat motif at different pitch within the key (intervals adjust)',
    'fragmentation':    'Use only the first 2-3 notes of the motif',
    'extension':        'Add 1-2 notes to the end of the motif',
    'rhythmic_displace':'Shift motif start by 1 beat (or half beat)',
    'ornamentation':    'Add passing tones, neighbor tones, or turns between motif notes',
}
```

## Song Structure Templates

Templates use 5-dimension energy maps (intensity/density/rhythm/harmonic/brightness) for precise control. See [energy-and-engagement.md](energy-and-engagement.md) for how each dimension maps to production parameters.

```python
SONG_TEMPLATES = {
    'pop_standard': {
        'total_bars': 64,
        'emotional_arc': 'hero_journey',
        'sections': [
            {'name': 'Intro',      'bars': 4,  'energy': {'intensity': 3, 'density': 2, 'rhythm': 2, 'harmonic': 4, 'brightness': 3},
             'elements': ['pad', 'light_drums'],
             'positive_styles': ['atmospheric', 'spacious', 'gentle filter sweep'],
             'negative_styles': ['full beat', 'lead melody'],
             'transition_out': 'filter_open',
             'engagement': 'Establish mood — memorable texture or motif that hooks the listener'},
            {'name': 'Verse 1',    'bars': 8,  'energy': {'intensity': 5, 'density': 5, 'rhythm': 5, 'harmonic': 5, 'brightness': 5},
             'elements': ['drums', 'bass', 'pad', 'melody'],
             'positive_styles': ['groove locked in', 'warm bass', 'head-nodding beat'],
             'negative_styles': ['peak energy', 'full chorus layers'],
             'transition_in': 'impact', 'transition_out': 'riser',
             'engagement': 'Establish the groove — listener should be nodding by bar 4'},
            {'name': 'Pre-Chorus', 'bars': 4,  'energy': {'intensity': 6, 'density': 6, 'rhythm': 6, 'harmonic': 6, 'brightness': 7},
             'elements': ['drums', 'bass', 'chords', 'melody', 'riser'],
             'positive_styles': ['building anticipation', 'filter opening', 'rising energy'],
             'negative_styles': ['resolution', 'relaxation'],
             'tension': 'PULL — snare build + filter open + riser noise',
             'engagement': 'Anticipation builds — something big is coming'},
            {'name': 'Chorus 1',   'bars': 8,  'energy': {'intensity': 8, 'density': 8, 'rhythm': 7, 'harmonic': 8, 'brightness': 9},
             'elements': ['full_drums', 'bass', 'chords', 'lead', 'arp'],
             'positive_styles': ['full energy', 'wide stereo', 'bright and open', 'sidechain pump'],
             'negative_styles': ['sparse', 'filtered', 'breakdown'],
             'tension': 'PUSH — impact + crash + full mix slam',
             'engagement': 'Payoff — this is what the listener waited for'},
            {'name': 'Verse 2',    'bars': 8,  'energy': {'intensity': 5, 'density': 5, 'rhythm': 5, 'harmonic': 5, 'brightness': 5},
             'elements': ['drums', 'bass', 'pad', 'melody'],
             'engagement': 'New variation — different melody or added element vs Verse 1'},
            {'name': 'Pre-Chorus', 'bars': 4,  'energy': {'intensity': 7, 'density': 7, 'rhythm': 7, 'harmonic': 6, 'brightness': 8},
             'elements': ['drums', 'bass', 'chords', 'melody', 'riser'],
             'tension': 'PULL — bigger build than first pre-chorus',
             'engagement': 'Escalated anticipation — listener knows what is coming and wants it more'},
            {'name': 'Chorus 2',   'bars': 8,  'energy': {'intensity': 9, 'density': 9, 'rhythm': 7, 'harmonic': 8, 'brightness': 9},
             'elements': ['full_drums', 'bass', 'chords', 'lead', 'arp', 'strings'],
             'tension': 'PUSH — bigger than Chorus 1 (added strings/layer)',
             'engagement': 'Elevated payoff — same hook but richer'},
            {'name': 'Bridge',     'bars': 8,  'energy': {'intensity': 5, 'density': 4, 'rhythm': 4, 'harmonic': 7, 'brightness': 5},
             'elements': ['light_drums', 'bass', 'pad', 'melody'],
             'positive_styles': ['contrast', 'different harmonic color', 'breathing room'],
             'negative_styles': ['same energy as chorus', 'repetition'],
             'engagement': 'Contrast — different mood before the final climax'},
            {'name': 'Chorus 3',   'bars': 8,  'energy': {'intensity': 10, 'density': 10, 'rhythm': 8, 'harmonic': 9, 'brightness': 10},
             'elements': ['full_drums', 'bass', 'chords', 'lead', 'arp', 'strings', 'fx'],
             'tension': 'PUSH — maximum energy, all layers, widest stereo',
             'engagement': 'THE CLIMAX at 2/3 — everything hits at once, the biggest moment'},
            {'name': 'Outro',      'bars': 4,  'energy': {'intensity': 4, 'density': 2, 'rhythm': 2, 'harmonic': 4, 'brightness': 3},
             'elements': ['pad', 'light_drums', 'melody'],
             'positive_styles': ['callback to intro', 'gentle resolution', 'reverb tail'],
             'engagement': 'Closure — reference the intro motif, resolve with intent'},
        ],
    },
    'edm_drop': {
        'total_bars': 88,
        'emotional_arc': 'tension_release',
        'sections': [
            {'name': 'Intro',       'bars': 8,  'energy': {'intensity': 3, 'density': 2, 'rhythm': 2, 'harmonic': 4, 'brightness': 3},
             'elements': ['pad', 'atmosphere'],
             'engagement': 'Set the sonic world — atmospheric texture that hints at genre'},
            {'name': 'Build 1',     'bars': 8,  'energy': {'intensity': 5, 'density': 5, 'rhythm': 5, 'harmonic': 5, 'brightness': 5},
             'elements': ['drums', 'bass', 'arp', 'riser'],
             'tension': 'PULL — groove establishes, riser begins',
             'engagement': 'Groove locks in — subtle riser starts building'},
            {'name': 'Breakdown 1', 'bars': 8,  'energy': {'intensity': 4, 'density': 3, 'rhythm': 2, 'harmonic': 6, 'brightness': 4},
             'elements': ['pad', 'melody', 'filter_sweep'],
             'engagement': 'Melody reveal — this is the melodic hook stripped bare'},
            {'name': 'Build 2',     'bars': 8,  'energy': {'intensity': 7, 'density': 7, 'rhythm': 8, 'harmonic': 5, 'brightness': 8},
             'elements': ['drums', 'bass', 'arp', 'riser', 'snare_roll'],
             'tension': 'PULL — snare roll accelerating, filter opening, pitch riser, 1-beat silence at end',
             'engagement': 'Unbearable anticipation — everything screams something big is next'},
            {'name': 'Drop 1',      'bars': 16, 'energy': {'intensity': 10, 'density': 9, 'rhythm': 9, 'harmonic': 6, 'brightness': 10},
             'elements': ['full_drums', 'bass', 'lead', 'chords', 'sidechain'],
             'tension': 'PUSH — sub boom + impact + crash + full beat + heavy sidechain',
             'engagement': 'THE DROP — maximum impact. New elements introduced every 4 bars to sustain energy'},
            {'name': 'Breakdown 2', 'bars': 8,  'energy': {'intensity': 3, 'density': 2, 'rhythm': 1, 'harmonic': 5, 'brightness': 3},
             'elements': ['pad', 'atmosphere', 'vocal_chop'],
             'engagement': 'Breathing room — contrast makes next drop hit harder'},
            {'name': 'Build 3',     'bars': 8,  'energy': {'intensity': 8, 'density': 7, 'rhythm': 9, 'harmonic': 5, 'brightness': 9},
             'elements': ['drums', 'bass', 'riser', 'snare_roll', 'pitch_riser'],
             'tension': 'PULL — bigger than Build 2 (added pitch riser, faster acceleration)',
             'engagement': 'Even bigger anticipation — listener knows what is coming'},
            {'name': 'Drop 2',      'bars': 16, 'energy': {'intensity': 10, 'density': 10, 'rhythm': 10, 'harmonic': 7, 'brightness': 10},
             'elements': ['full_drums', 'bass', 'lead', 'chords', 'sidechain', 'fx'],
             'tension': 'PUSH — even bigger than Drop 1 (added FX layers, wider stereo)',
             'engagement': 'THE CLIMAX — everything plus new elements not heard before'},
            {'name': 'Outro',       'bars': 8,  'energy': {'intensity': 4, 'density': 3, 'rhythm': 3, 'harmonic': 4, 'brightness': 3},
             'elements': ['pad', 'atmosphere', 'filter_sweep'],
             'engagement': 'Wind down — filter closing, callback to intro atmosphere'},
        ],
    },
    'hip_hop': {
        'total_bars': 72,
        'emotional_arc': 'night_drive',
        'sections': [
            {'name': 'Intro',    'bars': 4,  'energy': {'intensity': 3, 'density': 2, 'rhythm': 2, 'harmonic': 4, 'brightness': 3},
             'elements': ['sample', 'atmosphere'],
             'engagement': 'Mood setter — vinyl texture, filtered sample, or atmospheric element'},
            {'name': 'Verse 1',  'bars': 16, 'energy': {'intensity': 6, 'density': 5, 'rhythm': 6, 'harmonic': 5, 'brightness': 5},
             'elements': ['drums', 'bass', 'sample', 'pad'],
             'engagement': 'The pocket — groove must make you nod. Vary pattern every 4 bars (fill, hat change)'},
            {'name': 'Hook',     'bars': 8,  'energy': {'intensity': 8, 'density': 7, 'rhythm': 7, 'harmonic': 7, 'brightness': 7},
             'elements': ['full_drums', 'bass', 'melody', 'chords'],
             'tension': 'PUSH — melodic hook enters, energy lifts',
             'engagement': 'The hook — memorable melodic phrase that sticks'},
            {'name': 'Verse 2',  'bars': 16, 'energy': {'intensity': 6, 'density': 6, 'rhythm': 6, 'harmonic': 5, 'brightness': 5},
             'elements': ['drums', 'bass', 'sample', 'pad'],
             'engagement': 'Evolution — add a new subtle element not in Verse 1 (counter-melody, perc)'},
            {'name': 'Hook',     'bars': 8,  'energy': {'intensity': 8, 'density': 8, 'rhythm': 7, 'harmonic': 7, 'brightness': 7},
             'elements': ['full_drums', 'bass', 'melody', 'chords'],
             'engagement': 'Familiar payoff — same hook with subtle added layer'},
            {'name': 'Bridge',   'bars': 8,  'energy': {'intensity': 5, 'density': 4, 'rhythm': 4, 'harmonic': 6, 'brightness': 4},
             'elements': ['light_drums', 'bass', 'pad'],
             'engagement': 'Contrast — different harmonic color, stripped back'},
            {'name': 'Hook',     'bars': 8,  'energy': {'intensity': 9, 'density': 9, 'rhythm': 8, 'harmonic': 8, 'brightness': 8},
             'elements': ['full_drums', 'bass', 'melody', 'chords', 'fx'],
             'engagement': 'Final hook — biggest version with FX ear candy'},
            {'name': 'Outro',    'bars': 4,  'energy': {'intensity': 3, 'density': 2, 'rhythm': 2, 'harmonic': 4, 'brightness': 2},
             'elements': ['pad', 'atmosphere'],
             'engagement': 'Fade with intent — filter close or reverb tail, not abrupt stop'},
        ],
    },
    'jazz_aaba': {
        'total_bars': 128,
        'emotional_arc': 'melancholic_beauty',
        'sections': [
            {'name': 'Head A1',   'bars': 8,  'energy': {'intensity': 5, 'density': 5, 'rhythm': 5, 'harmonic': 7, 'brightness': 5},
             'elements': ['piano', 'bass', 'drums', 'melody'],
             'engagement': 'State the theme — clear melody over walking bass and brushes'},
            {'name': 'Head A2',   'bars': 8,  'energy': {'intensity': 5, 'density': 5, 'rhythm': 5, 'harmonic': 7, 'brightness': 5},
             'elements': ['piano', 'bass', 'drums', 'melody'],
             'engagement': 'Restate — slight variation in comping and dynamics'},
            {'name': 'Head B',    'bars': 8,  'energy': {'intensity': 6, 'density': 6, 'rhythm': 6, 'harmonic': 8, 'brightness': 6},
             'elements': ['piano', 'bass', 'drums', 'melody'],
             'engagement': 'Bridge section — contrast key center or mode, lift energy slightly'},
            {'name': 'Head A3',   'bars': 8,  'energy': {'intensity': 5, 'density': 5, 'rhythm': 5, 'harmonic': 7, 'brightness': 5},
             'elements': ['piano', 'bass', 'drums', 'melody'],
             'engagement': 'Return — resolve back to home key with turnaround'},
            {'name': 'Solo 1',    'bars': 32, 'energy': {'intensity': 7, 'density': 6, 'rhythm': 7, 'harmonic': 8, 'brightness': 7},
             'elements': ['piano', 'bass', 'drums', 'solo'],
             'engagement': 'Exploration — gradually increasing intensity over 32 bars, building from sparse to dense'},
            {'name': 'Solo 2',    'bars': 32, 'energy': {'intensity': 8, 'density': 7, 'rhythm': 8, 'harmonic': 9, 'brightness': 8},
             'elements': ['piano', 'bass', 'drums', 'solo'],
             'engagement': 'Peak improvisation — most harmonically adventurous, climax near 2/3 through'},
            {'name': 'Head Out',  'bars': 32, 'energy': {'intensity': 6, 'density': 5, 'rhythm': 5, 'harmonic': 7, 'brightness': 5},
             'elements': ['piano', 'bass', 'drums', 'melody'],
             'engagement': 'Return to theme — familiar melody resolves the journey, ritardando at end'},
        ],
    },
    'ambient': {
        'total_bars': 64,
        'emotional_arc': 'melancholic_beauty',
        'sections': [
            {'name': 'Emergence',    'bars': 16, 'energy': {'intensity': 2, 'density': 2, 'rhythm': 1, 'harmonic': 3, 'brightness': 2},
             'elements': ['drone', 'texture', 'atmosphere'],
             'engagement': 'Slow birth — sounds emerge from silence, new micro-detail every 8 bars'},
            {'name': 'Development',  'bars': 16, 'energy': {'intensity': 4, 'density': 4, 'rhythm': 2, 'harmonic': 5, 'brightness': 4},
             'elements': ['drone', 'pad', 'melody', 'texture'],
             'engagement': 'Gentle melody enters — spatial movement (pan, reverb shifts) substitutes for rhythmic energy'},
            {'name': 'Plateau',      'bars': 16, 'energy': {'intensity': 5, 'density': 5, 'rhythm': 3, 'harmonic': 6, 'brightness': 5},
             'elements': ['drone', 'pad', 'melody', 'arp', 'texture'],
             'engagement': 'Richest moment — most layers, widest stereo, harmonic peak'},
            {'name': 'Dissolution',  'bars': 16, 'energy': {'intensity': 2, 'density': 2, 'rhythm': 1, 'harmonic': 3, 'brightness': 2},
             'elements': ['drone', 'texture'],
             'engagement': 'Fade to silence — layers peel away one at a time over 16 bars'},
        ],
    },
    'metal': {
        'total_bars': 68,
        'emotional_arc': 'hero_journey',
        'sections': [
            {'name': 'Intro Riff',  'bars': 4,  'energy': {'intensity': 7, 'density': 5, 'rhythm': 7, 'harmonic': 4, 'brightness': 7},
             'elements': ['guitar', 'bass', 'drums'],
             'engagement': 'Statement of intent — the main riff, raw and immediate'},
            {'name': 'Verse',       'bars': 8,  'energy': {'intensity': 6, 'density': 5, 'rhythm': 6, 'harmonic': 4, 'brightness': 6},
             'elements': ['guitar', 'bass', 'drums', 'vocal'],
             'engagement': 'Pull back — palm mutes, restrained dynamics, building anticipation'},
            {'name': 'Pre-Chorus',  'bars': 4,  'energy': {'intensity': 7, 'density': 6, 'rhythm': 7, 'harmonic': 5, 'brightness': 7},
             'elements': ['guitar', 'bass', 'drums', 'vocal'],
             'tension': 'PULL — drum fill build, open chords replacing palm mutes'},
            {'name': 'Chorus',      'bars': 8,  'energy': {'intensity': 9, 'density': 8, 'rhythm': 8, 'harmonic': 6, 'brightness': 9},
             'elements': ['guitar', 'bass', 'full_drums', 'vocal', 'harmony'],
             'tension': 'PUSH — full power chords, double-tracked, harmonized vocals',
             'engagement': 'Anthemic — singalong hook with maximum guitar width'},
            {'name': 'Verse 2',     'bars': 8,  'energy': {'intensity': 6, 'density': 6, 'rhythm': 6, 'harmonic': 4, 'brightness': 6},
             'elements': ['guitar', 'bass', 'drums', 'vocal'],
             'engagement': 'Variation — different riff variation or added melodic element'},
            {'name': 'Chorus 2',    'bars': 8,  'energy': {'intensity': 9, 'density': 9, 'rhythm': 8, 'harmonic': 6, 'brightness': 9},
             'elements': ['guitar', 'bass', 'full_drums', 'vocal', 'harmony'],
             'engagement': 'Elevated repeat — added guitar layer or harmony'},
            {'name': 'Breakdown',   'bars': 8,  'energy': {'intensity': 8, 'density': 6, 'rhythm': 5, 'harmonic': 3, 'brightness': 6},
             'elements': ['guitar', 'bass', 'half_time_drums'],
             'engagement': 'Half-time heaviness — slow grinding riff, maximum weight'},
            {'name': 'Solo',        'bars': 8,  'energy': {'intensity': 10, 'density': 8, 'rhythm': 9, 'harmonic': 7, 'brightness': 10},
             'elements': ['guitar_solo', 'bass', 'full_drums', 'harmony'],
             'engagement': 'THE CLIMAX — virtuosic lead over full band, climax at 2/3'},
            {'name': 'Chorus 3',    'bars': 8,  'energy': {'intensity': 10, 'density': 10, 'rhythm': 8, 'harmonic': 7, 'brightness': 10},
             'elements': ['guitar', 'bass', 'full_drums', 'vocal', 'harmony', 'fx'],
             'engagement': 'Final chorus — every layer, key change up optional'},
            {'name': 'Outro',       'bars': 4,  'energy': {'intensity': 5, 'density': 4, 'rhythm': 4, 'harmonic': 4, 'brightness': 4},
             'elements': ['guitar', 'bass', 'drums'],
             'engagement': 'Callback — return to intro riff, decisive ending on power chord + feedback'},
        ],
    },
    'synthwave': {
        'total_bars': 72,
        'emotional_arc': 'night_drive',
        'sections': [
            {'name': 'Neon Fade In', 'bars': 8,  'energy': {'intensity': 2, 'density': 2, 'rhythm': 1, 'harmonic': 5, 'brightness': 3},
             'elements': ['pad', 'arp_filtered', 'atmosphere'],
             'engagement': 'Cinematic opening — pad swell + filtered arp emerging from darkness'},
            {'name': 'Drive',        'bars': 16, 'energy': {'intensity': 6, 'density': 6, 'rhythm': 5, 'harmonic': 6, 'brightness': 6},
             'elements': ['drums', 'bass', 'pad', 'arp', 'lead'],
             'engagement': 'Cruise mode — steady groove, sidechain pump, the vibe is locked'},
            {'name': 'Horizon',      'bars': 8,  'energy': {'intensity': 4, 'density': 3, 'rhythm': 3, 'harmonic': 7, 'brightness': 4},
             'elements': ['pad', 'bass_sustained', 'atmosphere'],
             'tension': 'PULL — strip to pad + sub, build anticipation with riser',
             'engagement': 'Breathing space — lets the reverb tails ring out'},
            {'name': 'Accelerate',   'bars': 8,  'energy': {'intensity': 7, 'density': 7, 'rhythm': 7, 'harmonic': 6, 'brightness': 8},
             'elements': ['drums', 'bass', 'arp', 'riser', 'lead'],
             'tension': 'PULL → PUSH transition — builds into peak',
             'engagement': 'Energy ramp — new arp pattern, drums get busier'},
            {'name': 'Midnight',     'bars': 16, 'energy': {'intensity': 9, 'density': 9, 'rhythm': 7, 'harmonic': 8, 'brightness': 9},
             'elements': ['full_drums', 'bass', 'supersaw', 'lead', 'arp', 'strings'],
             'tension': 'PUSH — supersaw chorus, maximum width',
             'engagement': 'THE PEAK — supersaw wall, lead melody soars, widest stereo field'},
            {'name': 'Coast',        'bars': 8,  'energy': {'intensity': 6, 'density': 5, 'rhythm': 5, 'harmonic': 6, 'brightness': 5},
             'elements': ['drums', 'bass', 'pad', 'arp'],
             'engagement': 'Settle back — familiar groove from Drive, sense of coming home'},
            {'name': 'Sunset',       'bars': 8,  'energy': {'intensity': 3, 'density': 2, 'rhythm': 2, 'harmonic': 5, 'brightness': 2},
             'elements': ['pad', 'atmosphere', 'arp_filtered'],
             'engagement': 'Mirror of intro — filter closing, lights dimming, satisfying bookend'},
        ],
    },
    'trap': {
        'total_bars': 80,
        'emotional_arc': 'party_energy',
        'sections': [
            {'name': 'Tag',         'bars': 4,  'energy': {'intensity': 3, 'density': 2, 'rhythm': 2, 'harmonic': 3, 'brightness': 3},
             'elements': ['atmosphere', 'fx'],
             'engagement': 'Producer tag + dark atmosphere — set the mood fast'},
            {'name': 'Verse 1',     'bars': 16, 'energy': {'intensity': 6, 'density': 5, 'rhythm': 7, 'harmonic': 4, 'brightness': 5},
             'elements': ['drums', '808_bass', 'melody', 'pad'],
             'engagement': 'The bounce — 808 patterns carry the groove, hi-hat rolls every 4 bars'},
            {'name': 'Hook',        'bars': 8,  'energy': {'intensity': 8, 'density': 7, 'rhythm': 8, 'harmonic': 5, 'brightness': 7},
             'elements': ['full_drums', '808_bass', 'melody', 'chords', 'ad_libs'],
             'tension': 'PUSH — 808 hits harder, melody doubles, crash on 1',
             'engagement': 'Catchy hook — repetitive melodic phrase that sticks'},
            {'name': 'Verse 2',     'bars': 16, 'energy': {'intensity': 6, 'density': 6, 'rhythm': 7, 'harmonic': 4, 'brightness': 5},
             'elements': ['drums', '808_bass', 'melody', 'pad', 'counter_mel'],
             'engagement': 'New flavor — added counter-melody or varied hi-hat pattern'},
            {'name': 'Hook',        'bars': 8,  'energy': {'intensity': 9, 'density': 8, 'rhythm': 9, 'harmonic': 5, 'brightness': 8},
             'elements': ['full_drums', '808_bass', 'melody', 'chords', 'ad_libs', 'fx'],
             'engagement': 'Elevated hook — hi-hat triplet rolls, pitched 808 slides'},
            {'name': 'Bridge',      'bars': 8,  'energy': {'intensity': 4, 'density': 3, 'rhythm': 3, 'harmonic': 5, 'brightness': 3},
             'elements': ['pad', '808_bass_sustained', 'atmosphere'],
             'engagement': 'Dark break — stripped to atmosphere, 808 sustains, eerie contrast'},
            {'name': 'Drop',        'bars': 8,  'energy': {'intensity': 10, 'density': 10, 'rhythm': 10, 'harmonic': 5, 'brightness': 9},
             'elements': ['full_drums', '808_bass', 'melody', 'chords', 'fx', 'perc'],
             'tension': 'PUSH — maximum 808 distortion, fastest hi-hats, all layers',
             'engagement': 'CLIMAX — hardest hitting section, everything turned up'},
            {'name': 'Outro',       'bars': 4,  'energy': {'intensity': 3, 'density': 2, 'rhythm': 2, 'harmonic': 3, 'brightness': 2},
             'elements': ['808_bass', 'atmosphere'],
             'engagement': 'Quick fade — 808 tail rings out into silence'},
        ],
    },
    'house_deep': {
        'total_bars': 80,
        'emotional_arc': 'party_energy',
        'sections': [
            {'name': 'Intro',         'bars': 8,  'energy': {'intensity': 3, 'density': 2, 'rhythm': 3, 'harmonic': 3, 'brightness': 3},
             'elements': ['kick', 'hihat', 'atmosphere'],
             'engagement': 'DJ-friendly intro — kick + hat establish tempo, filtered pad underneath'},
            {'name': 'Layer Up',      'bars': 8,  'energy': {'intensity': 5, 'density': 5, 'rhythm': 5, 'harmonic': 5, 'brightness': 5},
             'elements': ['drums', 'bass', 'pad', 'perc'],
             'tension': 'PULL — one new element every 2 bars',
             'engagement': 'Building — bass enters, then pad, then percussion, sense of arrival'},
            {'name': 'Main Groove',   'bars': 16, 'energy': {'intensity': 7, 'density': 7, 'rhythm': 7, 'harmonic': 7, 'brightness': 7},
             'elements': ['full_drums', 'bass', 'pad', 'chord_stab', 'perc'],
             'engagement': 'The groove — heavy sidechain, off-beat chords, hypnotic repetition with micro-variations'},
            {'name': 'Breakdown',     'bars': 8,  'energy': {'intensity': 4, 'density': 3, 'rhythm': 2, 'harmonic': 7, 'brightness': 4},
             'elements': ['pad', 'melody', 'atmosphere'],
             'tension': 'PULL — kick drops out, filter closes, melody revealed in the space',
             'engagement': 'The reveal — main melodic idea exposed without the beat'},
            {'name': 'Build',         'bars': 8,  'energy': {'intensity': 7, 'density': 6, 'rhythm': 8, 'harmonic': 5, 'brightness': 8},
             'elements': ['drums', 'riser', 'snare_build', 'filter_sweep'],
             'tension': 'PULL — snare roll + filter opening + riser noise, 1 beat silence',
             'engagement': 'Anticipation — crowd knows the drop is coming'},
            {'name': 'Peak Groove',   'bars': 16, 'energy': {'intensity': 9, 'density': 9, 'rhythm': 8, 'harmonic': 8, 'brightness': 9},
             'elements': ['full_drums', 'bass', 'pad', 'chord_stab', 'lead', 'perc', 'fx'],
             'tension': 'PUSH — impact + crash + full groove with added lead melody',
             'engagement': 'THE PEAK — all layers, lead melody on top, sidechain pumping, ear candy every 4 bars'},
            {'name': 'Strip Back',    'bars': 8,  'energy': {'intensity': 5, 'density': 4, 'rhythm': 5, 'harmonic': 5, 'brightness': 4},
             'elements': ['drums', 'bass', 'pad'],
             'engagement': 'Ease down — remove layers one at a time, filter closing gradually'},
            {'name': 'Outro',         'bars': 8,  'energy': {'intensity': 3, 'density': 2, 'rhythm': 3, 'harmonic': 3, 'brightness': 2},
             'elements': ['kick', 'hihat', 'pad'],
             'engagement': 'DJ-friendly outro — mirror of intro for clean mix-out'},
        ],
    },
}
```

## Transition Techniques

```python
TRANSITIONS = {
    'fill':           {'description': 'Drum fill (1-4 beats)', 'use': 'Every 4-8 bars'},
    'riser':          {'description': 'Sine pitch sweep (~200Hz→3kHz) + optional subtle LP noise, volume 0.1 max, cubic curve', 'use': 'Before drops/choruses'},
    'downlifter':     {'description': 'Reverse of riser, sine sweep descending 3kHz→200Hz', 'use': 'After drops'},
    'impact':         {'description': 'Kick + crash + sub boom on beat 1', 'use': 'Section starts'},
    'silence':        {'description': '1-2 beats of silence before impact', 'use': 'Before big moments'},
    'filter_sweep':   {'description': 'LPF opening 200->20kHz over 4-8 bars', 'use': 'Intro, builds'},
    'reverse_reverb': {'description': 'Reversed heavy reverb tail', 'use': 'Before vocal/lead entry'},
    'pitch_drop':     {'description': 'Pitch bend down 12-24 semitones', 'use': 'End of sections'},
    'stutter':        {'description': 'Buffer repeat getting faster', 'use': 'Before drops, builds'},
}

TRANSITIONS_EXTENDED = {
    'washout': {
        'description': 'Heavy reverb/delay increasing over 2-4 bars, washing out existing elements',
        'implementation': 'Gradually increase reverb wet from 0.3 to 0.9, delay feedback from 0.3 to 0.8',
        'use': 'Before breakdowns, end of sections — blurs the boundary between sections',
    },
    'reversed_reverb': {
        'description': 'Record reverb tail of a note, reverse it. Creates a swell INTO the next note.',
        'implementation': 'heavy_verb = freeverb(note, room=0.95, wet=0.8); reversed = heavy_verb[::-1]',
        'use': 'Before vocal/lead entry — magical/ethereal "whoosh" into the sound',
    },
    'tape_stop': {
        'description': 'Pitch drops while tempo slows, mimicking a tape deck stopping',
        'implementation': 'Resample signal with increasing read step (slower playback = lower pitch)',
        'use': 'End of sections, comedic/dramatic effect, DJ transitions',
    },
    'beat_repeat': {
        'description': 'Loop last 1/8 or 1/16 note, accelerating the repeat rate',
        'implementation': 'Copy last N samples, repeat at halving intervals (1/8 -> 1/16 -> 1/32)',
        'use': 'Before drops and builds, glitch transitions, EDM/electronic',
    },
    'cross_fade': {
        'description': 'Overlap outgoing and incoming sections with volume crossfade',
        'implementation': '1-4 bars overlap, outgoing fades out (cosine), incoming fades in',
        'use': 'Smooth section changes, ambient transitions, cinematic scoring',
    },
    'drum_fake_out': {
        'description': 'Stop drums for 1 beat then slam back in with full energy',
        'implementation': 'Zero out drum bus for last beat of bar, full hit on next bar beat 1 + crash',
        'use': 'Before choruses — creates anticipation in just 1 beat of silence',
    },
}

# Note: A SWEEP is a filter moving on existing content (LPF opening 200->20kHz).
# A RISER is a new synthesized element — use sfx_pitch_riser() (sine sweep), NOT noise risers (sound like fan noise).
# Sweeps reveal what's already playing. Risers add new energy on top. Keep riser volume <= 0.1.
```

### Practical Singing Range

Most melodies should stay within 1 to 1.5 octaves for singability.

- Verse melodies: use the lower portion of the range (intimate, conversational)
- Chorus melodies: push higher (emotional peak, energy lift)
- Bridge: can briefly exceed the range for dramatic effect
- Rap/spoken: keep within 1 octave centered around speaking pitch
- Default safe range for pop: A3–D5 (covers most untrained singers)

### Modern Hip-Hop 12-Bar Verse

Modern hip-hop increasingly uses 12-bar verses (instead of traditional 16) for tighter energy:

- Bars 1-4: Establish the flow/cadence, set the rhythmic pattern
- Bars 5-8: Develop the idea, switch up the rhythm, add complexity
- Bars 9-12: Punchline section, most memorable bars, highest impact
- This leaves more room for 8-bar hooks in the same song length
- Two 12-bar verses + two 8-bar hooks = 40 bars (vs 48 with 16-bar verses)

## JSON Schemas

### Drum Pattern Schema

```json
{
    "name": "string",
    "genre": "string",
    "bpm": {"min": "number", "max": "number"},
    "time_signature": "string (default '4/4')",
    "steps": "integer (default 16)",
    "swing": "number (0.5-0.75, default 0.5)",
    "tracks": {
        "track_name": {
            "pattern": [0, 1, 0, 1, "..."],
            "velocity": [0, 100, 0, 90, "..."],
            "midi_note": "integer",
            "probability": "number (0.0-1.0, default 1.0)"
        }
    }
}
```

### Chord Progression Schema

```json
{
    "name": "string",
    "key": "string",
    "mode": "string",
    "bpm": {"min": "number", "max": "number"},
    "chords": [
        {
            "symbol": "string (e.g. 'Cmaj7')",
            "roman": "string (e.g. 'Imaj7')",
            "root_midi": "integer",
            "intervals": [0, 4, 7, 11],
            "voicing_midi": [48, 55, 64, 67, 71],
            "duration_bars": "number (default 1)"
        }
    ]
}
```

### Song Template Schema

```json
{
    "genre": "string",
    "bpm": "number",
    "key": "string",
    "scale": "string",
    "swing": "number",
    "total_bars": "integer",
    "sections": [
        {
            "name": "string",
            "start_bar": "integer",
            "length_bars": "integer",
            "energy": "integer (0-10)",
            "chord_progression": "string (reference key)",
            "drum_pattern": "string (reference key)",
            "elements": ["string"],
            "transition_in": "string (reference key)",
            "transition_out": "string (reference key)"
        }
    ]
}
```
