# Key-Emotion & Mood Reference

Comprehensive mapping of musical elements to emotional qualities for mood-driven composition.

## Key-Emotion Table

```python
KEY_EMOTIONS = {
    # Major keys
    'C_major':  {'emotion': 'innocent, pure, simple',       'valence': 8, 'arousal': 4, 'genre_affinity': ['pop', 'classical', 'children']},
    'Db_major': {'emotion': 'grief, depressive, meditative', 'valence': 3, 'arousal': 3, 'genre_affinity': ['film_score', 'ambient']},
    'D_major':  {'emotion': 'triumphant, victorious, war-like', 'valence': 9, 'arousal': 8, 'genre_affinity': ['rock', 'classical', 'country']},
    'Eb_major': {'emotion': 'love, devotion, intimacy',      'valence': 7, 'arousal': 5, 'genre_affinity': ['rnb', 'jazz', 'soul']},
    'E_major':  {'emotion': 'joy, delight, brightness',      'valence': 9, 'arousal': 7, 'genre_affinity': ['pop', 'rock', 'country']},
    'F_major':  {'emotion': 'calm, complaisance, controlled', 'valence': 6, 'arousal': 3, 'genre_affinity': ['folk', 'classical', 'pop']},
    'Gb_major': {'emotion': 'triumph over adversity, relief', 'valence': 7, 'arousal': 6, 'genre_affinity': ['jazz', 'film_score']},
    'G_major':  {'emotion': 'pastoral, idyllic, rustic calm', 'valence': 7, 'arousal': 4, 'genre_affinity': ['folk', 'country', 'pop']},
    'Ab_major': {'emotion': 'dark cosmos, expansive, grand',  'valence': 5, 'arousal': 5, 'genre_affinity': ['film_score', 'ambient', 'neo_soul']},
    'A_major':  {'emotion': 'cheerful, innocent love, warm',  'valence': 8, 'arousal': 6, 'genre_affinity': ['pop', 'rock', 'country']},
    'Bb_major': {'emotion': 'hopeful, clear conscience',      'valence': 7, 'arousal': 5, 'genre_affinity': ['jazz', 'brass_band', 'pop']},
    'B_major':  {'emotion': 'wild passion, anger, rage',      'valence': 4, 'arousal': 9, 'genre_affinity': ['metal', 'rock', 'edm']},

    # Minor keys
    'C_minor':  {'emotion': 'love declaration, longing, unhappy love', 'valence': 4, 'arousal': 5, 'genre_affinity': ['classical', 'rnb', 'film_score']},
    'Db_minor': {'emotion': 'deep darkness, despair, wailing',         'valence': 2, 'arousal': 4, 'genre_affinity': ['ambient', 'doom', 'film_score']},
    'D_minor':  {'emotion': 'melancholy, brooding, contemplation',     'valence': 3, 'arousal': 4, 'genre_affinity': ['classical', 'metal', 'film_score']},
    'Eb_minor': {'emotion': 'deep anguish, existential dread',         'valence': 2, 'arousal': 5, 'genre_affinity': ['ambient', 'dark_electronic']},
    'E_minor':  {'emotion': 'grief, mournful, restless',               'valence': 3, 'arousal': 5, 'genre_affinity': ['rock', 'folk', 'indie']},
    'F_minor':  {'emotion': 'deep depression, funereal lament',        'valence': 2, 'arousal': 3, 'genre_affinity': ['classical', 'dark_ambient', 'film_score']},
    'F#_minor': {'emotion': 'gloomy, passionate suffering',            'valence': 3, 'arousal': 6, 'genre_affinity': ['metal', 'post_rock', 'shoegaze']},
    'G_minor':  {'emotion': 'discontent, unease, resentment',          'valence': 3, 'arousal': 5, 'genre_affinity': ['classical', 'trap', 'dark_pop']},
    'Ab_minor': {'emotion': 'suffocating, anxious, struggling',        'valence': 2, 'arousal': 6, 'genre_affinity': ['dark_electronic', 'industrial']},
    'A_minor':  {'emotion': 'graceful, tender, soothing',              'valence': 5, 'arousal': 4, 'genre_affinity': ['pop', 'lo_fi', 'indie']},
    'Bb_minor': {'emotion': 'night, surly, turning from the world',    'valence': 3, 'arousal': 3, 'genre_affinity': ['ambient', 'dark_jazz', 'noir']},
    'B_minor':  {'emotion': 'patience, calm awaiting fate',            'valence': 4, 'arousal': 3, 'genre_affinity': ['folk', 'classical', 'indie']},
}
```

## Tempo-Emotion Mapping

```python
TEMPO_EMOTIONS = {
    'very_slow':  {'bpm': (40, 60),   'emotion': 'solemn, funeral, meditative',     'energy': 1, 'genres': ['ambient', 'drone', 'classical_adagio']},
    'slow':       {'bpm': (60, 80),   'emotion': 'sad, reflective, intimate',       'energy': 3, 'genres': ['ballad', 'lo_fi', 'rnb', 'downtempo']},
    'moderate':   {'bpm': (80, 100),  'emotion': 'contemplative, walking pace',     'energy': 4, 'genres': ['hip_hop', 'reggae', 'soul']},
    'medium':     {'bpm': (100, 120), 'emotion': 'neutral, driving, purposeful',    'energy': 5, 'genres': ['pop', 'rock', 'indie']},
    'upbeat':     {'bpm': (120, 140), 'emotion': 'energetic, happy, danceable',     'energy': 7, 'genres': ['house', 'pop', 'disco', 'funk']},
    'fast':       {'bpm': (140, 170), 'emotion': 'excited, urgent, intense',        'energy': 8, 'genres': ['trance', 'dubstep', 'punk', 'metal']},
    'very_fast':  {'bpm': (170, 200), 'emotion': 'frantic, chaotic, exhilarating',  'energy': 9, 'genres': ['drum_and_bass', 'speedcore', 'thrash']},
}
```

## Mode-Emotion Matrix

Combine mode with tempo zone for specific emotional result.

```python
MODE_EMOTION_MATRIX = {
    # (mode, tempo_zone) -> emotional quality
    ('major', 'slow'):      'nostalgic, bittersweet tenderness, gentle warmth',
    ('major', 'medium'):    'confident, steady, reassuring, content',
    ('major', 'fast'):      'joyful, celebratory, euphoric, triumphant',

    ('minor', 'slow'):      'sorrowful, mournful, deeply sad, introspective',
    ('minor', 'medium'):    'tense, determined, brooding, mysterious',
    ('minor', 'fast'):      'angry, urgent, aggressive, anxious',

    ('dorian', 'slow'):     'cool melancholy, jazz-club intimacy, late-night',
    ('dorian', 'medium'):   'smooth, sophisticated, urban groove',
    ('dorian', 'fast'):     'funky, driving, assertive cool',

    ('phrygian', 'slow'):   'exotic, mystical, ancient, ceremonial',
    ('phrygian', 'medium'): 'dark, Spanish/flamenco intensity, suspenseful',
    ('phrygian', 'fast'):   'aggressive, metal, relentless, dangerous',

    ('lydian', 'slow'):     'dreamy, floating, otherworldly, wonder',
    ('lydian', 'medium'):   'optimistic, magical, cinematic grandeur',
    ('lydian', 'fast'):     'bright, soaring, fantastical, adventurous',

    ('mixolydian', 'slow'): 'bluesy, rootsy, laid-back warmth',
    ('mixolydian', 'medium'): 'rock swagger, confident blues-rock',
    ('mixolydian', 'fast'): 'party rock, driving blues energy, barn-burner',

    ('aeolian', 'slow'):    'tragic, lamenting, classical sadness',
    ('aeolian', 'medium'):  'dark pop, moody, emotionally heavy',
    ('aeolian', 'fast'):    'intense, stormy, dramatic urgency',

    ('locrian', 'slow'):    'dread, horror, unsettling void',
    ('locrian', 'medium'):  'disorienting, unstable, anxiety-inducing',
    ('locrian', 'fast'):    'chaotic, nightmarish, extreme dissonance',
}
```

## Instrumentation-Emotion Map

```python
INSTRUMENT_EMOTIONS = {
    # Instruments
    'piano':          {'emotion': 'reflective, clarity, intimacy',     'valence': 6, 'versatility': 10},
    'acoustic_guitar': {'emotion': 'warmth, honesty, comfort',        'valence': 7, 'versatility': 8},
    'electric_guitar': {'emotion': 'power, rebellion, passion',       'valence': 5, 'versatility': 9},
    'strings':         {'emotion': 'drama, romance, grandeur',        'valence': 6, 'versatility': 9},
    'brass':           {'emotion': 'triumph, authority, celebration',  'valence': 7, 'versatility': 6},
    'woodwinds':       {'emotion': 'pastoral, whimsy, nostalgia',     'valence': 7, 'versatility': 5},
    'synth_pad':       {'emotion': 'atmosphere, mystery, vastness',   'valence': 5, 'versatility': 8},
    'synth_lead':      {'emotion': 'futuristic, energy, determination', 'valence': 6, 'versatility': 7},
    'organ':           {'emotion': 'sacred, soulful, warm depth',     'valence': 6, 'versatility': 5},
    'bells':           {'emotion': 'innocence, magic, delicacy',      'valence': 7, 'versatility': 4},
    'choir':           {'emotion': 'transcendence, awe, spiritual',   'valence': 6, 'versatility': 5},
    'bass_sub':        {'emotion': 'weight, power, physicality',      'valence': 4, 'versatility': 7},
    'harp':            {'emotion': 'ethereal, heavenly, enchantment', 'valence': 8, 'versatility': 3},
    'marimba':         {'emotion': 'playful, tropical, organic',      'valence': 7, 'versatility': 4},
    'music_box':       {'emotion': 'childhood, nostalgia, fragility', 'valence': 6, 'versatility': 2},

    # Articulations (modifiers — combine with any instrument)
    'staccato':   {'emotion': 'tension, urgency, nervousness',   'energy_modifier': +2},
    'legato':     {'emotion': 'calm, flowing, connected',         'energy_modifier': -1},
    'tremolo':    {'emotion': 'anxiety, anticipation, suspense',  'energy_modifier': +1},
    'pizzicato':  {'emotion': 'playful, light, quirky',           'energy_modifier': -1},
    'spiccato':   {'emotion': 'bouncy, energetic, precise',       'energy_modifier': +1},
    'glissando':  {'emotion': 'dramatic sweep, transition',       'energy_modifier': +1},
    'vibrato':    {'emotion': 'warmth, expressiveness, humanity', 'energy_modifier': 0},
    'marcato':    {'emotion': 'emphatic, forceful, assertive',    'energy_modifier': +2},
}
```

## Melodic Contour-Emotion

```python
CONTOUR_EMOTIONS = {
    'ascending':      {'emotion': 'hope, building energy, anticipation, joy',
                       'use': 'Pre-chorus builds, verses gaining momentum, uplifting moments'},
    'descending':     {'emotion': 'sadness, resolution, winding down, acceptance',
                       'use': 'Outros, verse endings, melancholic phrases'},
    'arch':           {'emotion': 'tension then release, narrative arc, completeness',
                       'use': 'Chorus melodies, main themes, pop hooks'},
    'inverted_arch':  {'emotion': 'introspection then emergence, vulnerability to strength',
                       'use': 'Bridge melodies, verse openings, emotional depth'},
    'plateau':        {'emotion': 'meditation, insistence, trance-like, hypnotic',
                       'use': 'Rap verses, chant-style hooks, ambient textures'},
    'zigzag':         {'emotion': 'restlessness, excitement, unpredictability',
                       'use': 'Funk riffs, jazz improvisation, energetic passages'},
    'delayed_climax': {'emotion': 'building anticipation, emotional payoff, surprise',
                       'use': 'Chorus melodies that peak on the last phrase'},
}
```

## Mood-to-Parameters Quick Reference

```python
def mood_to_params(mood):
    """Map a mood keyword to suggested musical parameters."""
    MOOD_PARAMS = {
        'melancholic':    {'key': 'D_minor',  'scale': 'natural_minor', 'tempo': (60, 80),   'instruments': ['piano', 'strings', 'synth_pad'], 'contour': 'descending', 'articulation': 'legato'},
        'euphoric':       {'key': 'E_major',  'scale': 'major',         'tempo': (128, 140),  'instruments': ['synth_lead', 'synth_pad', 'brass'], 'contour': 'ascending', 'articulation': 'staccato'},
        'tense':          {'key': 'G_minor',  'scale': 'harmonic_minor','tempo': (100, 130),  'instruments': ['strings', 'synth_pad', 'bass_sub'], 'contour': 'zigzag', 'articulation': 'tremolo'},
        'peaceful':       {'key': 'F_major',  'scale': 'major',         'tempo': (60, 80),   'instruments': ['acoustic_guitar', 'piano', 'woodwinds'], 'contour': 'arch', 'articulation': 'legato'},
        'angry':          {'key': 'B_major',  'scale': 'phrygian',      'tempo': (140, 170),  'instruments': ['electric_guitar', 'bass_sub', 'synth_lead'], 'contour': 'zigzag', 'articulation': 'marcato'},
        'romantic':       {'key': 'Eb_major', 'scale': 'major',         'tempo': (70, 90),   'instruments': ['piano', 'strings', 'harp'], 'contour': 'arch', 'articulation': 'legato'},
        'mysterious':     {'key': 'Ab_major', 'scale': 'dorian',        'tempo': (80, 100),  'instruments': ['synth_pad', 'bells', 'woodwinds'], 'contour': 'inverted_arch', 'articulation': 'legato'},
        'triumphant':     {'key': 'D_major',  'scale': 'major',         'tempo': (120, 140),  'instruments': ['brass', 'strings', 'choir'], 'contour': 'ascending', 'articulation': 'marcato'},
        'nostalgic':      {'key': 'A_minor',  'scale': 'natural_minor', 'tempo': (75, 95),   'instruments': ['piano', 'acoustic_guitar', 'music_box'], 'contour': 'arch', 'articulation': 'legato'},
        'dark':           {'key': 'F_minor',  'scale': 'phrygian',      'tempo': (70, 100),  'instruments': ['synth_pad', 'bass_sub', 'strings'], 'contour': 'descending', 'articulation': 'tremolo'},
        'dreamy':         {'key': 'Gb_major', 'scale': 'lydian',        'tempo': (70, 90),   'instruments': ['synth_pad', 'bells', 'harp'], 'contour': 'plateau', 'articulation': 'legato'},
        'energetic':      {'key': 'A_major',  'scale': 'mixolydian',    'tempo': (120, 145),  'instruments': ['electric_guitar', 'synth_lead', 'brass'], 'contour': 'zigzag', 'articulation': 'staccato'},
        'cinematic':      {'key': 'C_minor',  'scale': 'natural_minor', 'tempo': (80, 110),  'instruments': ['strings', 'brass', 'choir', 'piano'], 'contour': 'delayed_climax', 'articulation': 'legato'},
        'chill':          {'key': 'Bb_major', 'scale': 'dorian',        'tempo': (70, 90),   'instruments': ['synth_pad', 'acoustic_guitar', 'piano'], 'contour': 'plateau', 'articulation': 'legato'},
        'anxious':        {'key': 'Db_minor', 'scale': 'locrian',       'tempo': (110, 140),  'instruments': ['synth_pad', 'strings', 'bells'], 'contour': 'zigzag', 'articulation': 'tremolo'},
        'hopeful':        {'key': 'G_major',  'scale': 'lydian',        'tempo': (90, 110),  'instruments': ['piano', 'strings', 'acoustic_guitar'], 'contour': 'ascending', 'articulation': 'legato'},
        'epic':           {'key': 'D_minor',  'scale': 'harmonic_minor','tempo': (100, 130),  'instruments': ['choir', 'brass', 'strings', 'bass_sub'], 'contour': 'delayed_climax', 'articulation': 'marcato'},
        'playful':        {'key': 'C_major',  'scale': 'major',         'tempo': (110, 130),  'instruments': ['marimba', 'piano', 'bells'], 'contour': 'zigzag', 'articulation': 'pizzicato'},

        # Compound moods (modern trend: ultra-specific emotional blends)
        'melancholic_euphoria':  {'key': 'E_minor',  'scale': 'dorian',        'tempo': (120, 135), 'instruments': ['synth_pad', 'synth_lead', 'piano'], 'contour': 'arch', 'articulation': 'legato',
                                  'note': 'Minor key at dance tempo. Sad chords, uplifting energy.'},
        'dark_serenity':         {'key': 'Bb_minor', 'scale': 'natural_minor', 'tempo': (65, 80),  'instruments': ['synth_pad', 'piano', 'strings'], 'contour': 'plateau', 'articulation': 'legato',
                                  'note': 'Dark harmony but slow, calm delivery. Ambient darkness.'},
        'anxious_hope':          {'key': 'F#_minor', 'scale': 'harmonic_minor','tempo': (100, 120), 'instruments': ['piano', 'strings', 'synth_lead'], 'contour': 'ascending', 'articulation': 'staccato',
                                  'note': 'Nervous energy resolving upward. Harmonic minor tension with ascending resolution.'},
        'nostalgic_wonder':      {'key': 'Ab_major', 'scale': 'lydian',        'tempo': (80, 100),  'instruments': ['music_box', 'strings', 'synth_pad'], 'contour': 'delayed_climax', 'articulation': 'legato',
                                  'note': 'Lydian #4 creates wonder. Slow reveal. Childhood memory feeling.'},
        'aggressive_beauty':     {'key': 'C_minor',  'scale': 'harmonic_minor','tempo': (130, 150), 'instruments': ['strings', 'electric_guitar', 'choir'], 'contour': 'arch', 'articulation': 'marcato',
                                  'note': 'Beautiful harmony at intense tempo. Cinematic action with emotional weight.'},
    }
    return MOOD_PARAMS.get(mood, MOOD_PARAMS['cinematic'])  # default to cinematic
```

## Cross-References

- Scales & chords: See `music-theory.md` for scale formulas, chord construction, and voice leading
- Emotional arcs: See `energy-and-engagement.md` for macro-level tension/release curves and 5-dimensional energy mapping
- Genre defaults: See `genre-guide.md` for genre-specific BPM, scale, and mood defaults
