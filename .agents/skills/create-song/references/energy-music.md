# Music Energy & Engagement

Music-specific emotional dimensions, emotional arcs, composition planning, engagement rules, and tension techniques. Extracted from the shared energy-and-engagement reference for music production use.

## Emotional Dimensions

```python
EMOTIONAL_DIMENSIONS = {
    'valence':    'Positive<->Negative (happy<->sad, major<->minor)',
    'arousal':    'High<->Low energy (excited<->calm)',
    'tension':    'Tight<->Relaxed (dissonant<->consonant)',
    'movement':   'Static<->Dynamic (sustained<->rhythmic)',
    'space':      'Intimate<->Epic (dry/close<->reverb/wide)',
}
```

## Emotional Arc Templates

```python
EMOTIONAL_ARCS = {
    'hero_journey': {
        # Classic: calm -> challenge -> triumph -> reflection
        'description': 'Start peaceful, build through struggle to victory, resolve gently',
        'arc': [
            {'section': 'intro',    'valence': 6, 'arousal': 2, 'tension': 1, 'space': 5},
            {'section': 'verse',    'valence': 5, 'arousal': 4, 'tension': 3, 'space': 5},
            {'section': 'build',    'valence': 4, 'arousal': 7, 'tension': 8, 'space': 7},
            {'section': 'chorus',   'valence': 9, 'arousal': 9, 'tension': 3, 'space': 9},
            {'section': 'verse2',   'valence': 6, 'arousal': 5, 'tension': 4, 'space': 5},
            {'section': 'chorus2',  'valence': 10,'arousal': 10,'tension': 2, 'space': 10},
            {'section': 'outro',    'valence': 8, 'arousal': 3, 'tension': 1, 'space': 7},
        ],
        'genres': ['EDM', 'trance', 'pop', 'orchestral', 'metal'],
    },

    'night_drive': {
        # Sustained mood: steady groove with subtle shifts
        'description': 'Consistent vibe with gentle undulations, hypnotic flow',
        'arc': [
            {'section': 'intro',    'valence': 5, 'arousal': 3, 'tension': 2, 'space': 6},
            {'section': 'groove',   'valence': 6, 'arousal': 6, 'tension': 3, 'space': 7},
            {'section': 'drift',    'valence': 5, 'arousal': 5, 'tension': 2, 'space': 8},
            {'section': 'peak',     'valence': 7, 'arousal': 7, 'tension': 4, 'space': 8},
            {'section': 'cruise',   'valence': 6, 'arousal': 6, 'tension': 3, 'space': 7},
            {'section': 'fade',     'valence': 5, 'arousal': 3, 'tension': 1, 'space': 9},
        ],
        'genres': ['synthwave', 'lo-fi', 'deep house', 'chillwave', 'ambient'],
    },

    'tension_release': {
        # Dramatic: build unbearable tension then explode
        'description': 'Slow suffocating build to cathartic release, repeat with higher stakes',
        'arc': [
            {'section': 'intro',      'valence': 3, 'arousal': 2, 'tension': 3, 'space': 4},
            {'section': 'build1',     'valence': 3, 'arousal': 5, 'tension': 7, 'space': 5},
            {'section': 'drop1',      'valence': 7, 'arousal': 10,'tension': 2, 'space': 9},
            {'section': 'breakdown',  'valence': 2, 'arousal': 2, 'tension': 5, 'space': 3},
            {'section': 'build2',     'valence': 2, 'arousal': 7, 'tension': 9, 'space': 6},
            {'section': 'drop2',      'valence': 8, 'arousal': 10,'tension': 1, 'space': 10},
            {'section': 'outro',      'valence': 5, 'arousal': 3, 'tension': 1, 'space': 7},
        ],
        'genres': ['dubstep', 'drum & bass', 'techno', 'trance', 'trap'],
    },

    'melancholic_beauty': {
        # Bittersweet: sadness with moments of beauty and hope
        'description': 'Wistful, contemplative, with brief moments of warmth breaking through',
        'arc': [
            {'section': 'intro',    'valence': 3, 'arousal': 2, 'tension': 2, 'space': 7},
            {'section': 'verse',    'valence': 3, 'arousal': 4, 'tension': 4, 'space': 6},
            {'section': 'chorus',   'valence': 6, 'arousal': 6, 'tension': 3, 'space': 8},
            {'section': 'verse2',   'valence': 2, 'arousal': 3, 'tension': 5, 'space': 5},
            {'section': 'chorus2',  'valence': 7, 'arousal': 7, 'tension': 2, 'space': 9},
            {'section': 'bridge',   'valence': 4, 'arousal': 5, 'tension': 6, 'space': 7},
            {'section': 'outro',    'valence': 5, 'arousal': 2, 'tension': 1, 'space': 8},
        ],
        'genres': ['indie', 'post-rock', 'ambient', 'classical', 'jazz', 'lo-fi'],
    },

    'party_energy': {
        # High energy: relentless groove with strategic breathers
        'description': 'Immediate groove, short breaks for breathing, escalating peaks',
        'arc': [
            {'section': 'intro',      'valence': 7, 'arousal': 5, 'tension': 3, 'space': 6},
            {'section': 'groove1',    'valence': 8, 'arousal': 8, 'tension': 3, 'space': 7},
            {'section': 'break1',     'valence': 6, 'arousal': 4, 'tension': 2, 'space': 5},
            {'section': 'groove2',    'valence': 9, 'arousal': 9, 'tension': 4, 'space': 8},
            {'section': 'break2',     'valence': 5, 'arousal': 3, 'tension': 5, 'space': 4},
            {'section': 'peak',       'valence': 10,'arousal': 10,'tension': 2, 'space': 9},
            {'section': 'groove3',    'valence': 9, 'arousal': 9, 'tension': 3, 'space': 8},
            {'section': 'outro',      'valence': 7, 'arousal': 5, 'tension': 1, 'space': 6},
        ],
        'genres': ['house', 'techno', 'disco', 'funk', 'dancehall', 'afrobeats'],
    },

    'cinematic_epic': {
        # Film score: slow establishment -> escalating drama -> massive climax -> resolution
        'description': 'Grandiose emotional journey with multiple escalating peaks',
        'arc': [
            {'section': 'establish',  'valence': 5, 'arousal': 1, 'tension': 1, 'space': 8},
            {'section': 'theme',      'valence': 6, 'arousal': 4, 'tension': 3, 'space': 7},
            {'section': 'develop',    'valence': 4, 'arousal': 6, 'tension': 6, 'space': 8},
            {'section': 'conflict',   'valence': 2, 'arousal': 8, 'tension': 9, 'space': 9},
            {'section': 'climax',     'valence': 8, 'arousal': 10,'tension': 5, 'space': 10},
            {'section': 'resolve',    'valence': 9, 'arousal': 4, 'tension': 1, 'space': 9},
            {'section': 'epilogue',   'valence': 7, 'arousal': 2, 'tension': 0, 'space': 7},
        ],
        'genres': ['orchestral', 'film score', 'post-rock', 'epic', 'trailer'],
    },
}
```

## Mapping Emotions to Production Parameters

```python
def emotion_to_production(emotion_state):
    """Convert emotional dimensions to concrete production parameters."""
    v = emotion_state  # dict with valence, arousal, tension, movement, space

    return {
        # VALENCE -> mode, chord quality
        'scale': 'major' if v['valence'] > 6 else 'minor' if v['valence'] < 4 else 'dorian',
        'chord_extensions': v['valence'] > 7,  # Add 9ths/11ths for warmth
        'bright_voicings': v['valence'] > 5,

        # AROUSAL -> tempo feel, dynamics, rhythmic drive
        'velocity_range': (60 + v['arousal'] * 4, 80 + v['arousal'] * 2),
        'rhythmic_density': v['arousal'] / 10.0,  # 0=sparse, 1=dense
        'sidechain_depth': 0.3 + v['arousal'] * 0.05,  # Stronger pump at high arousal

        # TENSION -> dissonance, harmonic instability, FX intensity
        'add_suspensions': v['tension'] > 6,
        'use_tritones': v['tension'] > 7,
        'reverb_predelay': 10 + v['tension'] * 5,  # Longer = more unsettled
        'delay_feedback': 0.2 + v['tension'] * 0.05,  # More feedback = more tension

        # SPACE -> reverb size, stereo width, distance
        'reverb_room': 0.3 + v['space'] * 0.07,  # 0.3 to 1.0
        'reverb_wet': 0.1 + v['space'] * 0.03,   # 0.1 to 0.4
        'stereo_width': 0.7 + v['space'] * 0.06,  # 0.7 to 1.3
        'pad_octave': 2 if v['space'] > 7 else 3,  # Lower = more expansive
    }
```

## Composition Plan

Structure your script's arrangement as a detailed composition plan -- each section gets explicit style directives (what to include AND what to exclude):

```python
COMPOSITION_PLAN = {
    'global_styles': {
        'positive': ['warm analog character', 'head-nodding groove', 'rich stereo field'],
        'negative': ['harsh digital artifacts', 'flat dynamics', 'thin low end'],
    },
    'sections': [
        {
            'name': 'Intro',
            'bars': 8,
            'duration_target_ms': 16000,  # ~16s at 120bpm
            'energy': {'intensity': 3, 'density': 2, 'rhythm': 2, 'harmonic': 4, 'brightness': 3},
            'emotion': {'valence': 5, 'arousal': 2, 'tension': 2, 'space': 7},
            'positive_styles': ['atmospheric', 'spacious reverb', 'gentle filter sweep opening'],
            'negative_styles': ['drums', 'full bass', 'bright sounds'],
            'transition_in': None,
            'transition_out': 'filter_open',
        },
        {
            'name': 'Verse 1',
            'bars': 16,
            'energy': {'intensity': 5, 'density': 5, 'rhythm': 5, 'harmonic': 5, 'brightness': 5},
            'emotion': {'valence': 5, 'arousal': 5, 'tension': 3, 'space': 5},
            'positive_styles': ['groove established', 'head-nodding beat', 'warm bass'],
            'negative_styles': ['full chorus energy', 'lead melody'],
            'transition_in': 'impact',
            'transition_out': 'riser',
        },
        # ... more sections
    ],
}
```

## Engagement Rules by Context

### Short-Form (15-60s)

```python
SHORT_FORM_RULES = {
    'hook_window':    '0-3 seconds — must grab attention immediately',
    'no_long_intros': 'Skip or minimize intro, start near the action',
    'peak_early':     'First energy peak within 8-15 seconds',
    'constant_motion':'Something must change every 4-8 seconds',
    'loop_friendly':  'End should flow back into beginning',
    'bright_mix':     'Higher brightness for phone speakers (boost 2-5kHz)',
}
```

### Full Tracks (2-4 minutes)

```python
FULL_TRACK_RULES = {
    'establish_mood':    '0-15s: Set the sonic world (ambient, texture, tone)',
    'introduce_groove':  '15-30s: Main rhythm and bass establish the pocket',
    'first_payoff':      '30-60s: First chorus/drop — reward the listeners patience',
    'develop':           '60-120s: Variations, new elements, build/release cycles',
    'climax':            '120-150s: Biggest moment — all elements, max energy, widest stereo',
    'resolve':           '150-180s: Wind down gracefully, callback to opening motif',
    'never_repeat_exact':'No 8-bar section should be an exact copy of another',
    'contrast_ratio':    'Energy difference between peaks and valleys should be 4+ points',
}
```

### Background/Ambient (3-10 minutes)

```python
AMBIENT_RULES = {
    'slow_evolution':    'Changes happen over 16-32 bars, not 4-8',
    'no_sudden_shifts':  'All transitions use 4+ bar crossfades',
    'subtle_engagement': 'New micro-details emerge every 30-60 seconds',
    'drone_foundation':  'At least one element sustains throughout entire piece',
    'harmonic_drift':    'Chords change every 4-8 bars, not every bar',
    'spatial_movement':  'Pan automation and reverb shifts substitute for rhythmic energy',
}
```

## The 10 Golden Rules of Engagement

1. Front-load the hook -- First 8 bars must establish something memorable (a groove, a sound, a texture) that makes the listener want to hear what's next

2. Contrast creates interest -- The drop only hits hard because of the breakdown. The chorus only soars because the verse was restrained. Always build contrast:
   - Energy contrast: peaks vs valleys (aim for 4+ points difference)
   - Timbral contrast: warm vs bright sections
   - Spatial contrast: intimate (dry/close) vs epic (reverb/wide)
   - Rhythmic contrast: groove vs breakdown

3. Something must change every 4-8 bars -- Add a layer, remove a layer, change a pattern, shift a filter. Static = boring. But changes should be subtle enough to feel natural

4. The 2/3 climax rule -- The biggest moment should come at approximately 2/3 through the track (bar 42 of 64, bar 56 of 80). This mirrors natural storytelling arcs and creates the most satisfying emotional payoff

5. Strategic silence -- 1-2 beats of silence before a big moment is more powerful than any riser. The absence of sound creates maximum anticipation

6. Never introduce everything at once -- Spread new elements across 8-16 bars. Each new layer gives the listener something to discover. Front-loading all elements leaves nowhere to go

7. Call back to the beginning -- The outro should reference the intro (same pad, same filter position, same motif). This creates closure and makes the track feel intentional

8. Ride the groove, don't fight it -- Once a groove is established, let it breathe for at least 8 bars before major changes. People need time to lock into a rhythm before you can meaningfully depart from it

9. Ear candy every 16 bars -- A subtle detail that rewards attentive listening: a reversed note, a brief stereo widening, a one-time percussion hit, a vocal chop. These are not structural but they keep engaged listeners discovering new things

10. End with intent -- Fading out is lazy. Either resolve decisively (final chord + reverb tail) or create a deliberate loop point. The last 4 bars should feel like a conclusion, not an abandonment

## Track Density Thresholds

> Cross-reference: The full `TRACK_DENSITY_THRESHOLD` dict and `is_track_active()` function live in [mixing-music.md](mixing-music.md). Repeated here for context within the energy system.

```python
TRACK_DENSITY_THRESHOLD = {
    'pad': 1, 'atmosphere': 1,
    'chord': 3, 'melody': 3, 'arp': 3,
    'kick': 4, 'bass': 4, 'hihat': 4, 'snare': 4, 'ride': 4,
    'strings': 5, 'counter_mel': 5, 'perc': 5,
    'clap': 7, 'lead': 7,
    'fx': 9, 'doubled': 9, 'harmony': 9,
}
```

## Music-Specific Macro Tension Techniques

These large-scale tension builders (4-32 bars) are specific to music production:

```python
# From MACRO_TENSION_TECHNIQUES:
'snare_roll': {
    'description': 'Accelerating snare hits from 8th->16th->32nd notes',
    'energy_effect': 'rhythm +3, intensity +2',
    'implementation': 'Exponential density increase over 4-8 bars',
    'placement': 'Last 4-8 bars before drop/chorus',
},
'harmonic_build': {
    'description': 'Progressively add chord extensions and layers',
    'energy_effect': 'harmonic +3, density +2',
    'implementation': 'Bar 1: triads -> Bar 4: 7ths -> Bar 8: 9ths + inversions',
    'placement': 'Verse->chorus transitions',
},
'rhythmic_acceleration': {
    'description': 'Drum pattern subdivision increases (4th->8th->16th)',
    'energy_effect': 'rhythm +4 over build',
    'implementation': 'Hi-hat doubles, then triples, then rolls',
    'placement': 'Last 4 bars before peak sections',
},
```

## Music-Specific Micro Tension Techniques

Small elements (1-4 bars) that maintain forward momentum:

```python
# From MICRO_TENSION_TECHNIQUES:
'kick_drop': {
    'description': 'Remove kick for 1-2 beats, return with impact',
    'effect': 'Creates momentary void that makes return satisfying',
    'frequency': 'Every 8-16 bars, before phrase boundaries',
},
'reverse_crash': {
    'description': 'Reversed cymbal crash into forward crash (pull->push)',
    'effect': 'Signals upcoming change, creates anticipation',
    'frequency': 'Before section transitions, every 8 bars',
},
'drum_fill': {
    'description': 'Varied fills (NOT the same fill every time)',
    'effect': 'Breaks pattern monotony, signals phrase boundary',
    'frequency': 'Every 4-8 bars, varied types',
    'variants': ['snare_roll', 'tom_cascade', 'hat_flurry', 'ghost_buildup'],
},
'ghost_notes': {
    'description': 'Very quiet anticipatory hits before main hits',
    'effect': 'Adds groove depth and human feel',
    'frequency': 'Continuously in drum patterns at vel 20-35%',
},
'harmonic_surprise': {
    'description': 'Brief chromatic passing chord or borrowed chord',
    'effect': 'Ear candy, prevents harmonic predictability',
    'frequency': 'Once per 8-16 bar section, tastefully',
},
```
