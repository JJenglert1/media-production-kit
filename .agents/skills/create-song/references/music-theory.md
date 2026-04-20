# Music Theory Reference

Actionable music theory for programmatic generation. All intervals in semitones from root.

## Scales

```python
SCALES = {
    # Major/Minor family
    'major':            [0, 2, 4, 5, 7, 9, 11],    # W-W-H-W-W-W-H (bright, happy)
    'natural_minor':    [0, 2, 3, 5, 7, 8, 10],    # W-H-W-W-H-W-W (sad, dark)
    'harmonic_minor':   [0, 2, 3, 5, 7, 8, 11],    # raised 7th, exotic/tense
    'melodic_minor':    [0, 2, 3, 5, 7, 9, 11],    # jazzy minor, smooth

    # Pentatonic (no semitone steps = no dissonance, almost impossible to sound bad)
    'pentatonic_major': [0, 2, 4, 7, 9],            # open, universal
    'pentatonic_minor': [0, 3, 5, 7, 10],           # bluesy, soulful

    # Blues
    'blues':            [0, 3, 5, 6, 7, 10],        # minor pent + b5 blue note
    'blues_major':      [0, 2, 3, 4, 7, 9],         # major pent + b3

    # Symmetric
    'whole_tone':       [0, 2, 4, 6, 8, 10],        # dreamy, floating (only 2 exist)
    'dim_half_whole':   [0, 1, 3, 4, 6, 7, 9, 10],  # tense, over dom7 chords
    'dim_whole_half':   [0, 2, 3, 5, 6, 8, 9, 11],  # dark, over dim7 chords

    # Modes of major scale
    'ionian':           [0, 2, 4, 5, 7, 9, 11],    # = major
    'dorian':           [0, 2, 3, 5, 7, 9, 10],    # jazzy minor, natural 6th
    'phrygian':         [0, 1, 3, 5, 7, 8, 10],    # dark, Spanish, b2
    'lydian':           [0, 2, 4, 6, 7, 9, 11],    # dreamy, ethereal, #4
    'mixolydian':       [0, 2, 4, 5, 7, 9, 10],    # bluesy major, b7
    'aeolian':          [0, 2, 3, 5, 7, 8, 10],    # = natural minor
    'locrian':          [0, 1, 3, 5, 6, 8, 10],    # unstable, b5

    # Bebop (8-note: chord tones on downbeats in eighth-note lines)
    'bebop_dominant':   [0, 2, 4, 5, 7, 9, 10, 11], # Mixolydian + maj7
    'bebop_dorian':     [0, 2, 3, 4, 5, 7, 9, 10],  # Dorian + maj3

    # Exotic
    'hungarian_minor':  [0, 2, 3, 6, 7, 8, 11],    # two augmented 2nds, dramatic
    'phrygian_dominant':[0, 1, 4, 5, 7, 8, 10],    # Spanish/Arabic, flamenco
    'double_harmonic':  [0, 1, 4, 5, 7, 8, 11],    # Byzantine/Arabic

    # Japanese
    'hirajoshi':        [0, 2, 3, 7, 8],            # ethereal, mysterious
    'in_sen':           [0, 1, 5, 7, 10],           # dark, brooding
    'yo':               [0, 2, 5, 7, 9],            # bright, pastoral
}
```

### Scale Selection by Mood

| Mood | Scale(s) | Key Feature |
|------|----------|-------------|
| Happy, bright | Major, Lydian | Major 3rd |
| Sad, dark | Natural minor, Aeolian | Minor 3rd, flat 6th |
| Jazzy, smooth | Dorian | Minor 3rd + natural 6th |
| Exotic, Spanish | Phrygian, Phrygian dominant | Flat 2nd |
| Dreamy, floating | Lydian, Whole tone | #4 / all whole steps |
| Bluesy, raw | Blues, Mixolydian | b5 blue note / b7 |
| Tense, dramatic | Harmonic minor, Hungarian minor | Augmented 2nd |
| Ethereal, Japanese | Hirajoshi, In-sen | Sparse pentatonic |
| Middle Eastern | Phrygian dominant, Double harmonic | b2 + major 3rd |

> See [key-emotion-and-mood.md](key-emotion-and-mood.md) for detailed key-emotion mappings (all 24 keys), tempo-emotion matrix, mode-emotion combinations, and the `mood_to_params()` function.

### The Seven Modes

| Mode | Degree | Character | Signature | Genre Uses |
|------|--------|-----------|-----------|------------|
| Ionian | I | Bright, happy | Natural 4th | Pop, classical |
| Dorian | ii | Smooth jazzy minor | Natural 6th | Jazz, funk, Santana |
| Phrygian | iii | Dark, Spanish | Flat 2nd | Flamenco, metal |
| Lydian | IV | Dreamy, ethereal | Sharp 4th | Film scores, prog |
| Mixolydian | V | Bluesy major | Flat 7th | Blues, rock, funk |
| Aeolian | vi | Sad, melancholic | Flat 6th | Rock, pop, metal |
| Locrian | vii | Unstable, dissonant | Flat 5th | Metal (rare), jazz |

## Chords

```python
CHORDS = {
    # Triads
    'major': [0,4,7], 'minor': [0,3,7], 'diminished': [0,3,6], 'augmented': [0,4,8],
    'sus2': [0,2,7], 'sus4': [0,5,7], 'power': [0,7],

    # Seventh chords
    'maj7': [0,4,7,11], 'min7': [0,3,7,10], 'dom7': [0,4,7,10],
    'dim7': [0,3,6,9], 'half_dim7': [0,3,6,10], 'min_maj7': [0,3,7,11],

    # Sixth chords
    'maj6': [0,4,7,9], 'min6': [0,3,7,9],

    # Extended
    'dom9': [0,4,7,10,14], 'maj9': [0,4,7,11,14], 'min9': [0,3,7,10,14],
    'dom11': [0,4,7,10,14,17], 'min11': [0,3,7,10,14,17],
    'dom13': [0,4,7,10,14,17,21], 'maj13': [0,4,7,11,14,17,21],

    # Add chords (no 7th)
    'add9': [0,4,7,14], 'add11': [0,4,7,17], 'madd9': [0,3,7,14],

    # Altered dominants
    'dom7sharp9': [0,4,7,10,15],     # Hendrix chord
    'dom7flat9': [0,4,7,10,13],
    'dom7sharp11': [0,4,7,10,14,18], # Lydian dominant
    'alt': [0,4,6,10,13,15],         # Altered dominant
}
```

### Diatonic Chords

In major key:
```
I=major  ii=minor  iii=minor  IV=major  V=major  vi=minor  vii°=dim
7ths: Imaj7  ii7=min7  iii7=min7  IVmaj7  V7=dom7  vi7=min7  vii7=half_dim7
```

In natural minor:
```
i=minor  ii°=dim  III=major  iv=minor  v=minor  VI=major  VII=major
```

In harmonic minor:
```
i=minor  ii°=dim  III+=aug  iv=minor  V=major  VI=major  vii°=dim
```

## Chord Progressions

### Pop / Rock

| Name | Roman | In C | Examples |
|------|-------|------|---------|
| Axis of Awesome | I-V-vi-IV | C-G-Am-F | Let It Be, Someone Like You |
| Melancholic pop | vi-IV-I-V | Am-F-C-G | Save Tonight, Numb |
| 50s doo-wop | I-vi-IV-V | C-Am-F-G | Stand By Me |
| Basic rock | I-IV-V-I | C-F-G-C | |
| Mixolydian vamp | I-bVII-IV | C-Bb-F | Sweet Home Alabama |
| Andalusian | i-bVII-bVI-V | Am-G-F-E | Flamenco staple |

### Jazz

| Name | Roman | In C | Notes |
|------|-------|------|-------|
| ii-V-I | ii7-V7-Imaj7 | Dm7-G7-Cmaj7 | THE jazz progression |
| Minor ii-V-i | ii7b5-V7b9-i7 | Dm7b5-G7b9-Cm7 | Minor key jazz |
| Turnaround | Imaj7-vi7-ii7-V7 | Cmaj7-Am7-Dm7-G7 | Rhythm changes |
| Backdoor | iv7-bVII7-Imaj7 | Fm7-Bb7-Cmaj7 | Surprise resolution |
| Coltrane | Through 3 keys M3 apart | B-G-Eb centers | Giant Steps |

### Blues

12-Bar Blues (basic):
```
| I7  | I7  | I7  | I7  |
| IV7 | IV7 | I7  | I7  |
| V7  | IV7 | I7  | V7  |
```

Jazz Blues (with substitutions):
```
| I7   | IV7  | I7    | I7      |
| IV7  | IV7  | I7    | Am7 D7  |
| Dm7  | G7   | I7 Am7| Dm7 G7  |
```

Minor Blues:
```
| i7  | i7  | i7  | i7  |
| iv7 | iv7 | i7  | i7  |
| bVI7| V7  | i7  | V7  |
```

### Genre-to-Progression Quick Map

| Genre | Progression | Tempo | Feel |
|-------|------------|-------|------|
| Pop | I-V-vi-IV | 100-130 | Straight 8ths |
| Rock | I-IV-V, I-bVII-IV | 110-140 | Backbeat |
| Jazz | ii-V-I | 120-200 | Swing |
| Blues | 12-bar | 70-120 | Shuffle/swing |
| EDM | i-bVI-bVII-i | 125-135 | 4-on-floor |
| Hip-hop | minor key loops | 80-115 | Straight/swung 16ths |
| Funk | I7-IV7 vamp | 100-130 | Syncopated 16ths |
| Reggae | I-IV, offbeat | 70-90 | Offbeat skank |

## Voice Leading

```python
"""
Rules for smooth chord transitions:
1. COMMON TONE: Hold shared notes in same voice
2. MINIMAL MOTION: Move each voice to nearest chord tone (prefer 1-2 semitones)
3. CONTRARY MOTION: When bass moves up, inner voices move down
4. RESOLVE TENDENCY TONES: Leading tone -> tonic, 7th of chord -> down by step
"""

def voice_lead(current_midi, next_intervals, next_root):
    """Nearest-note voice leading between chords."""
    candidates = []
    for oct in [-12, 0, 12]:
        for interval in next_intervals:
            candidates.append(next_root + interval + oct)
    result = []
    used = set()
    for note in current_midi:
        best = min((abs(c - note), c) for c in candidates if c not in used)
        result.append(best[1])
        used.add(best[1])
    return sorted(result)
```

### Chord Voicing Tips
- Use inversions and spread voicings (1.5 octave spread sounds lush)
- Drop the 5th an octave for open voicings
- Avoid root-position block chords (sounds MIDI-keyboard)
- In jazz: omit the root (bass plays it), voice 3rd-7th-9th-13th

## Melody Generation

### Interval Statistics
~70% of melodic intervals are stepwise (1-2 semitones). Distribution:

| Interval | Probability | Direction |
|----------|------------|-----------|
| Unison (0) | 10% | - |
| Half step (1) | 12-13% | Up slightly favored |
| Whole step (2) | 15-17% | Up slightly favored |
| Minor 3rd (3) | 6-7% | Either |
| Major 3rd (4) | 4% | Either |
| Perfect 4th (5) | 3-4% | Either |
| Perfect 5th (7) | 2-3% | Either |
| Larger | <2% | Usually compensated |

### Melody Rules
1. After a large leap UP, step DOWN (gap fill principle)
2. After a large leap DOWN, step UP
3. Arch shape: rise then fall over a phrase
4. Climax (highest note) around 2/3 through the phrase
5. Start and end on chord tones (root, 3rd, or 5th)
6. 30-40% rests -- real musicians breathe
7. Strong beats (1, 3): prefer chord tones
8. Weak beats: scale tones, passing tones OK

### Algorithmic Approaches

Rule-based (recommended for generation):
```python
def generate_melody(scale, chord_prog, bars=4, notes_per_bar=8, root_midi=60):
    """Musical rule-based melody generation."""
    melody = []
    idx = 2  # start on 3rd degree
    for bar in range(bars):
        chord_root, chord_type = chord_prog[bar % len(chord_prog)]
        for note in range(notes_per_bar):
            is_strong = (note % (notes_per_bar // 2) == 0)
            if is_strong:
                # Move to nearest chord tone
                idx = find_nearest_chord_tone(idx, chord_root, chord_type, scale)
            else:
                # Stepwise motion with arch bias
                bias = [0.2, 0.2, 0.6] if bar < bars/2 else [0.6, 0.2, 0.2]
                direction = np.random.choice([-1, 0, 1], p=bias)
                idx = max(0, min(len(scale)*2 - 1, idx + direction))
            octave = idx // len(scale)
            degree = idx % len(scale)
            midi = root_midi + scale[degree] + 12 * octave
            melody.append(max(48, min(84, midi)))  # keep in range
    melody[-1] = root_midi  # end on root
    return melody
```

Markov chain: Build transition matrix from interval sequences, generate probabilistically.

Weighted random: Use INTERVAL_WEIGHTS distribution with constraint filtering.

### Call and Response
- Call (2-4 beats): ends on tension note (not tonic)
- Response (2-4 beats): ends on resolution (tonic/5th)
- Response mirrors/inverts call's rhythmic shape
- Types: exact repeat, transposed, inverted, complementary

## Harmonic Concepts

### Cadences

| Type | Chords | Strength | Character |
|------|--------|----------|-----------|
| Perfect authentic | V7 -> I | Strongest | "The End." |
| Plagal | IV -> I | Medium | "Amen" |
| Half | * -> V | Weak | "More to come..." |
| Deceptive | V -> vi | Surprise | "Not what you expected" |
| Backdoor | bVII -> I | Jazz | Unexpected but smooth |

### Secondary Dominants
V7 of any diatonic chord (except vii°). The dominant a 5th above the target:
- V7/V = D7 -> G (most common)
- V7/ii = A7 -> Dm
- V7/IV = C7 -> F
- V7/vi = E7 -> Am

Each introduces a chromatic note (not in the key).

### Tritone Substitution
Replace any V7 with a dom7 whose root is a tritone (6 semitones) away. They share the same tritone interval (3rd and 7th swap).
- G7 -> Db7 (both have B-F tritone)
- Creates chromatic bass movement: Dm7 -> Db7 -> Cmaj7

### Modal Interchange (Borrowed Chords)
Borrow chords from parallel minor into major key:
- bVI (Ab in C major): dramatic, epic
- bVII (Bb in C major): rock, powerful
- iv (Fm in C major): melancholy (minor plagal)
- bIII (Eb in C major): surprise, color

Common patterns: I-bVI-bVII-I (epic rock), I-iv-I (Creep)

### Chromatic Mediants
Chords a 3rd apart with chromatic relationship. Dramatic, cinematic (film scores).
- C -> Eb (minor 3rd up, share G)
- C -> E (major 3rd up, share E)
- C -> Ab (major 3rd down, share C)

### Pedal Points
Sustained bass note while harmony changes above. Creates stability (tonic pedal) or tension (dominant pedal). Common in intros, buildups, transitions.

### Suspensions
Hold a note from previous chord into new chord, creating dissonance that resolves:
- sus4 -> 3: F held over C chord, resolves to E
- sus2 -> 3: D held, resolves up to E
- 9-8, 7-6 suspensions in classical harmony

## Time Signatures

| Time Sig | Feel | Accent Pattern | Genres |
|----------|------|---------------|--------|
| 4/4 | Common | S-w-M-w | Most music |
| 3/4 | Waltz | S-w-w | Classical waltzes, folk |
| 6/8 | Compound duple | S-w-w-M-w-w | Jigs, ballads |
| 5/4 | Asymmetric (3+2) | S-w-w-M-w | Take Five, prog |
| 7/8 | Asymmetric | Various groupings | Balkan, prog (Money) |
| 12/8 | Compound quad | S-w-w-M-w-w-M-w-w-M-w-w | Blues, gospel |

## Song Forms

| Form | Structure | Energy Curve | Genres |
|------|-----------|-------------|--------|
| Verse-Chorus | ABABCBB | 0.5-0.8-0.5-0.8-0.6-0.9-1.0 | Pop, rock |
| AABA (32-bar) | A(8)-A(8)-B(8)-A(8) | 0.5-0.6-0.7-0.5 | Jazz standards |
| 12-bar blues | Repeated 12-bar cycles | Varies per verse | Blues |
| EDM drop | intro-build-drop-break-build-drop-outro | 0.3-0.7-1.0-0.3-0.7-1.0-0.2 | EDM |

### Energy Management
- Intro: 0.2-0.4 (set mood)
- Verse: 0.4-0.6 (leave room for chorus)
- Pre-chorus: 0.5-0.7 (build anticipation)
- Chorus: 0.8-1.0 (peak energy)
- Bridge: 0.4-0.7 (contrast)
- Outro: 0.2-0.5 (wind down)

### Transitions
- Riser: noise sweep / pitch rise over 4-8 bars
- Fill: drum fill (1-2 beats) connecting sections
- Drop: silence (1-2 beats) before big moment
- Build: add elements, increase density, automate filter up
- Impact: kick + crash + sub boom on beat 1
