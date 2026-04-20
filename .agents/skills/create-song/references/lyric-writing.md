# Lyric Writing Reference

Rhyme schemes, meter, songwriting techniques, and section roles for lyric generation.

## Rhyme Schemes

```python
RHYME_SCHEMES = {
    'AABB': {
        'pattern': 'Lines 1-2 rhyme, lines 3-4 rhyme',
        'example': 'I walk alone beneath the rain (A) / To wash away the years of pain (A) / The city lights begin to fade (B) / As memories of you cascade (B)',
        'character': 'Simple, direct, driving momentum',
        'genre_affinity': ['pop', 'hip_hop', 'children'],
        'difficulty': 1,
    },
    'ABAB': {
        'pattern': 'Lines 1-3 rhyme, lines 2-4 rhyme',
        'example': 'I see you standing in the rain (A) / Your shadow fading in the dark (B) / I call your name but its in vain (A) / I search for one remaining spark (B)',
        'character': 'Sophisticated, storytelling, structured',
        'genre_affinity': ['folk', 'rock', 'country', 'classical'],
        'difficulty': 3,
    },
    'ABCB': {
        'pattern': 'Only lines 2 and 4 rhyme',
        'example': 'The morning comes without a sound (A) / I pour the coffee black and strong (B) / Your jacket hanging on the door (C) / Reminds me something has gone wrong (B)',
        'character': 'Conversational, breathing room, narrative freedom',
        'genre_affinity': ['folk', 'country', 'indie', 'ballad'],
        'difficulty': 2,
    },
    'XAXA': {
        'pattern': 'Only even lines rhyme, odd lines are free',
        'example': 'The television hums all night (X) / While shadows dance across the wall (A) / I count the hours til the light (X) / And wonder if you think at all (A)',
        'character': 'Loose, modern, natural speech flow',
        'genre_affinity': ['indie', 'alt_rock', 'spoken_word'],
        'difficulty': 2,
    },
    'AABA': {
        'pattern': 'Lines 1-2-4 rhyme, line 3 breaks away',
        'example': 'Fly me to the moon and let me play (A) / Among the stars on this fine day (A) / In other words, hold my hand (B) / In other words, baby understand (A)',
        'character': 'Classic Tin Pan Alley, satisfying resolution',
        'genre_affinity': ['jazz', 'standards', 'musical_theater'],
        'difficulty': 3,
    },
    'free_verse': {
        'pattern': 'No fixed rhyme scheme',
        'example': 'The weight of everything I carry / settles into my shoulders / and the morning asks nothing of me / except to keep going',
        'character': 'Raw, poetic, emotionally direct',
        'genre_affinity': ['spoken_word', 'art_pop', 'experimental', 'indie'],
        'difficulty': 4,  # harder to make memorable without rhyme
    },
}
```

## Rhyme Types

```python
RHYME_TYPES = {
    'perfect':       {'description': 'Identical stressed vowel + ending sounds',
                      'example': 'cat/hat, moon/spoon, fire/desire',
                      'strength': 5, 'best_use': 'Chorus hooks, song titles, emotional peaks'},
    'slant':         {'description': 'Similar but not identical sounds',
                      'example': 'sun/moon, light/late, home/bone',
                      'strength': 3, 'best_use': 'Verses for sophistication, avoiding predictability'},
    'internal':      {'description': 'Rhyme within the same line',
                      'example': 'I FEEL the STEEL beneath my wheels',
                      'strength': 4, 'best_use': 'Hip-hop flow, creating density, rapid-fire sections'},
    'multisyllabic': {'description': 'Multiple syllables rhyme together',
                      'example': 'elevation/celebration, terrified/clarified',
                      'strength': 5, 'best_use': 'Rap, impressive hooks, showing craft'},
    'assonance':     {'description': 'Matching vowel sounds only',
                      'example': 'lake/rain, holy/over, try/light',
                      'strength': 2, 'best_use': 'Dreamy quality, soft rhyming, R&B melodies'},
    'consonance':    {'description': 'Matching consonant sounds only',
                      'example': 'luck/lick, bat/bit, puff/poof',
                      'strength': 2, 'best_use': 'Punchy rhythmic effect, hip-hop, percussive lines'},
    'eye_rhyme':     {'description': 'Looks like it rhymes but doesnt',
                      'example': 'love/move, cough/through',
                      'strength': 1, 'best_use': 'Visual poetry, lyrics read as text'},
}
```

## Syllable Patterns & Meter

```python
METERS = {
    'iambic':    {'pattern': 'da-DUM', 'stress': '01',
                  'effect': 'Natural English speech rhythm, conversational, most common in pop/rock',
                  'example': 'a-WAKE | my-SOUL | and-SING | to-NIGHT'},
    'trochaic':  {'pattern': 'DUM-da', 'stress': '10',
                  'effect': 'Forceful, commanding, chant-like, driving',
                  'example': 'NEV-er | GON-na | GIVE-you | UP-now'},
    'anapestic': {'pattern': 'da-da-DUM', 'stress': '001',
                  'effect': 'Galloping, energetic, building momentum',
                  'example': 'in-the-NIGHT | on-the-ROAD | to-the-END'},
    'dactylic':  {'pattern': 'DUM-da-da', 'stress': '100',
                  'effect': 'Rolling, waltz-like, grand, classical feel',
                  'example': 'BEAU-ti-ful | MER-ri-ly | WON-der-ful'},
    'spondaic':  {'pattern': 'DUM-DUM', 'stress': '11',
                  'effect': 'Heavy, emphatic, used sparingly for impact',
                  'example': 'HEART-BREAK | DEAD-WEIGHT | COLD-BLOOD'},
}
# Tip: mismatched meter makes lyrics feel awkward against the melody.
# Match stressed syllables to strong beats (1 and 3 in 4/4 time).
```

## Section Roles

```python
SECTION_ROLES = {
    'verse': {
        'purpose': 'Tell the story, paint specific scenes, advance the narrative',
        'lyric_strategy': 'Concrete imagery, sensory details, unique per verse, character/setting/conflict',
        'typical_lines': 4-8,
        'repetition': 'low — each verse has new words',
        'tip': 'Each verse should reveal something new while building toward the chorus emotion',
    },
    'pre_chorus': {
        'purpose': 'Build anticipation, create lift toward chorus',
        'lyric_strategy': 'Transition from verse specifics to chorus universals, rising emotion',
        'typical_lines': 2-4,
        'repetition': 'medium — often same words each time',
        'tip': 'Use ascending melody and lyrics that create urgency or question the chorus will answer',
    },
    'chorus': {
        'purpose': 'Deliver the emotional core, the hook, the main message',
        'lyric_strategy': 'Universal themes, title/hook repetition, simple memorable language',
        'typical_lines': 4-8,
        'repetition': 'high — same or nearly same each time',
        'tip': 'Place the song title in the first or last line. Make it singable on first listen.',
    },
    'bridge': {
        'purpose': 'Provide contrast, new perspective, emotional shift',
        'lyric_strategy': 'Different viewpoint, twist, confession, time shift, or reframing',
        'typical_lines': 4-8,
        'repetition': 'none — appears once',
        'tip': 'If the chorus says "I love you", the bridge might ask "but is love enough?"',
    },
    'outro': {
        'purpose': 'Resolve or leave lingering, close the emotional arc',
        'lyric_strategy': 'Reprise a hook, final thought, or let the music fade',
        'typical_lines': 2-4,
        'repetition': 'varies — can repeat chorus hook or be unique',
        'tip': 'Can mirror the intro for bookend effect, or trail off for ambiguity',
    },
    'intro': {
        'purpose': 'Set the scene, establish mood, draw the listener in',
        'lyric_strategy': 'Often instrumental, but if vocal: a teaser line, spoken word, or humming',
        'typical_lines': 0-2,
        'repetition': 'none',
        'tip': 'Less is more. A single evocative line or silence can be more powerful than a full verse.',
    },
}
```

## Songwriting Techniques

```python
LYRIC_TECHNIQUES = {
    'title_repetition': {
        'description': 'Place the song title prominently in the chorus (first or last line)',
        'why': 'Cements identity, makes the song instantly recognizable',
        'example': '"Rolling in the Deep" — title is the chorus hook',
    },
    'sensory_imagery': {
        'description': 'Appeal to sight, sound, touch, taste, smell',
        'why': 'Makes abstract emotions concrete and visceral',
        'example': '"The taste of salt on your skin" vs "I was sad about you leaving"',
    },
    'show_dont_tell': {
        'description': 'Use specific details instead of naming the emotion directly',
        'why': 'Lets the listener feel the emotion rather than being told what to feel',
        'example': '"Your coffee cup still warm beside the door" vs "I miss you"',
    },
    'verse_specific_chorus_universal': {
        'description': 'Verses paint unique scenes; chorus captures the universal feeling',
        'why': 'Specific details create empathy, universal choruses create singalongs',
        'example': 'Verse: "Tuesday morning, rain on 5th avenue" → Chorus: "We all fall down"',
    },
    'contrast': {
        'description': 'Pair opposites for emotional impact',
        'why': 'Creates tension and memorability through juxtaposition',
        'example': '"Fire and rain", "Hello / goodbye", "whisper to me then scream"',
    },
    'conversational_tone': {
        'description': 'Write as if speaking to one person, not performing for an audience',
        'why': 'Feels intimate and authentic, connects with the listener personally',
        'example': '"You know that thing you do when no one else is watching?"',
    },
    'power_of_three': {
        'description': 'Group ideas in threes for rhythmic and rhetorical completeness',
        'why': 'Three items feel complete: setup, development, payoff',
        'example': '"Heart and soul and everything between"',
    },
    'open_interpretation': {
        'description': 'Leave some meaning ambiguous so listeners project their own story',
        'why': 'Songs that mean different things to different people become timeless',
        'example': '"Somebody that I used to know" — who? everyone has a "somebody"',
    },
    'callback': {
        'description': 'Reference an earlier line later with changed context or meaning',
        'why': 'Rewards attentive listening, creates emotional depth',
        'example': 'Verse 1: "I built these walls to keep you safe" → Bridge: "I built these walls and now I cant get out"',
    },
}
```

## Practical Vocal Range Guide

| Voice Type | Comfortable Range | Notes |
|-----------|-------------------|-------|
| Bass | E2–E4 | Deepest male voice, rich low end |
| Baritone | A2–A4 | Most common male voice |
| Tenor | C3–C5 | High male voice, pop/rock leads |
| Alto | F3–F5 | Lower female voice, warmth |
| Mezzo-Soprano | A3–A5 | Most common female voice |
| Soprano | C4–C6 | Highest female voice, classical |

Rules of thumb:
- Keep melody within 1 to 1.5 octaves for singability
- Verse melodies: use the lower portion of the range (intimate, conversational)
- Chorus melodies: push higher (emotional peak, energy)
- Bridge: can briefly exceed the range for dramatic effect
- Rap/spoken: keep within 1 octave centered around speaking pitch
- Default safe range for pop: A3–D5 (covers most untrained singers)
