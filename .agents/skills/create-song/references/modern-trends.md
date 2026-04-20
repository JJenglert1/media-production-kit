# Modern Music Trends (2024–2026)

Current production trends, genre movements, blending patterns, and sound design directions.

## Production Trends

```python
PRODUCTION_TRENDS = {
    'ai_assisted': {
        'description': 'AI tools embedded in most major workflows — stem separation, mixing assistance, melody generation, mastering',
        'production_tip': 'Use AI for ideation and cleanup, keep human decisions for arrangement and emotion',
        'relevance': 9,
    },
    'analog_digital_hybrid': {
        'description': 'Analog warmth (tape saturation, tube compression, vinyl crackle) applied to digital workflows',
        'production_tip': 'Add tape_saturate() and tube_warmth() to master chain for organic feel',
        'relevance': 8,
    },
    'spatial_audio': {
        'description': 'Dolby Atmos, binaural mixing, immersive 3D audio formats gaining mainstream adoption',
        'production_tip': 'Design mixes with height and depth, not just L/R panning',
        'relevance': 7,
    },
    'adaptive_modular': {
        'description': 'Stems that respond to user behavior — gaming soundtracks, interactive media, platform-aware mixing',
        'production_tip': 'Generate independent stems that work as a whole and individually',
        'relevance': 6,
    },
    'bedroom_production': {
        'description': 'DIY lo-fi aesthetic as deliberate artistic choice, not limitation',
        'production_tip': 'Intentional imperfection: slight detuning, room ambience, tape wobble',
        'relevance': 8,
    },
    'minimalist_maximalist_split': {
        'description': 'Production polarizing between stripped-back minimalism and dense layered maximalism',
        'production_tip': 'Choose one extreme and commit — middle ground feels unfocused',
        'relevance': 7,
    },
}
```

## Genre Growth & Popularity

```python
GENRE_TRENDS = {
    'edm': {
        'growth': '+124% streaming growth',
        'direction': 'Festival culture driving mainstream crossover, melodic bass and future bass leading',
        'production_notes': 'Heavier sidechaining, longer builds, more vocal chops',
    },
    'modern_country': {
        'growth': '+150% in sample usage',
        'direction': 'Pop-country fusion, stadium country, country-trap crossover',
        'production_notes': 'Clean acoustic guitar + modern drum programming + pop vocal production',
    },
    'rnb_neo_soul': {
        'growth': 'Significant climb',
        'direction': 'Smooth production, sample-heavy, jazz-influenced harmony renaissance',
        'production_notes': 'Extended chords (9ths, 11ths), vinyl texture, warm compression, chorus on everything',
    },
    'drum_and_bass': {
        'growth': '+16% usage',
        'direction': 'Mainstream crossover via pop features, liquid DnB gaining traction',
        'production_notes': 'Reese bass + chopped vocal samples + breakbeat-derived drums at 170-180 BPM',
    },
    'latin': {
        'growth': '+7.2%',
        'direction': 'Reggaeton, Latin trap, dembow rhythms entering non-Latin genres',
        'production_notes': 'Dembow rhythm (boom-ch-boom-chick), 808 slides, bilingual hooks',
    },
    'afrobeats': {
        'growth': 'Global expansion',
        'direction': 'West African rhythms crossing into pop, EDM, R&B',
        'production_notes': 'Log drum patterns, call-response vocals, 100-115 BPM, sparse but rhythmic',
    },
    'hip_hop': {
        'growth': 'Plateau/slight decline in streaming share',
        'direction': 'Fragmenting into micro-subgenres, experimental production rising',
        'production_notes': 'More melodic, pluggnb influence, rage beats, Jersey club rhythms infiltrating',
    },
}
```

## Genre Blending Examples

```python
GENRE_BLENDS = {
    'afro_house_dnb': {
        'bpm': (170, 180),
        'elements': 'African percussion + rolling DnB basslines + house chord stabs',
        'scale': 'dorian',
        'reference': 'Shy FX, ENNY — DnB with Afro vocal patterns',
    },
    'hyperpop_indie_folk': {
        'bpm': (130, 150),
        'elements': 'Acoustic guitar + extreme pitch shifting + bitcrushed drums + autotuned folk vocals',
        'scale': 'major',
        'reference': '100 gecs meets Bon Iver — organic source, digital destruction',
    },
    'phonk_revival': {
        'bpm': (130, 145),
        'elements': 'Memphis rap samples + cowbell + drift aesthetic + heavy 808 + chopped vocals',
        'scale': 'minor_pentatonic',
        'reference': 'Drift phonk — aggressive, lo-fi, car culture energy',
    },
    'neo_city_pop': {
        'bpm': (95, 115),
        'elements': '1980s Japanese city pop chords + modern bedroom production + lush reverb + FM synths',
        'scale': 'lydian',
        'reference': 'Tatsuro Yamashita through a modern lo-fi lens',
    },
    'country_edm': {
        'bpm': (125, 135),
        'elements': 'Nashville topline + four-on-floor drop + banjo/fiddle samples + stadium synths',
        'scale': 'mixolydian',
        'reference': 'Festival country, pop-country with EDM builds and drops',
    },
    'lo_fi_jazz': {
        'bpm': (70, 85),
        'elements': 'Jazz harmony (ii-V-I with extensions) + lo-fi texture (vinyl, tape, bitcrush) + slow tempo',
        'scale': 'dorian',
        'reference': 'Nujabes, lo-fi beats study playlists — jazz chords through a dusty filter',
    },
    'classical_trap': {
        'bpm': (130, 150),
        'elements': 'Orchestral strings/brass + trap 808s + hi-hat rolls + classical melody over heavy bass',
        'scale': 'harmonic_minor',
        'reference': 'Yellow Claw, KSHMR — epic orchestral drops with trap percussion',
    },
    'reggaeton_pop': {
        'bpm': (90, 100),
        'elements': 'Dembow rhythm + pop vocal melody + Latin percussion + 808 bass',
        'scale': 'minor',
        'reference': 'Bad Bunny, Rosalía — dembow foundation with pop songwriting',
    },
    'ambient_rnb': {
        'bpm': (60, 80),
        'elements': 'Ambient pads + R&B vocal runs + minimal drums + extended reverb',
        'scale': 'dorian',
        'reference': 'SZA, Frank Ocean — atmospheric, sparse, emotionally heavy',
    },
    'metal_electronic': {
        'bpm': (130, 160),
        'elements': 'Distorted guitar riffs + EDM synths + breakbeats + screamed and clean vocals',
        'scale': 'phrygian',
        'reference': 'Bring Me The Horizon, Crossfaith — metalcore meets electronic',
    },
}
```

## Mood-First Production

The shift from genre-first to mood-first thinking in music creation.

```python
MOOD_FIRST = {
    'trend': 'Users increasingly describe desired music by emotion rather than genre',
    'top_moods': ['soft', 'emotional', 'cinematic', 'chill', 'dark', 'uplifting', 'dreamy', 'intense'],
    'cinematic_share': '~25% of music generation requests include the word "cinematic"',

    # Compound mood recipes — map ultra-specific emotional blends to production parameters
    'compound_moods': {
        'melancholic_euphoria': {
            'primary': 'melancholy', 'secondary': 'euphoria',
            'key': 'E_minor', 'tempo': 128, 'scale': 'dorian',
            'instruments': ['synth_pad', 'synth_lead', 'piano', '808'],
            'effects': ['heavy_reverb', 'sidechain_pump', 'filter_automation'],
            'note': 'Sad chords at dance tempo. The "crying in the club" sound.',
        },
        'dark_serenity': {
            'primary': 'darkness', 'secondary': 'serenity',
            'key': 'Bb_minor', 'tempo': 72, 'scale': 'natural_minor',
            'instruments': ['synth_pad', 'piano', 'strings', 'sub_bass'],
            'effects': ['long_reverb', 'slow_chorus', 'tape_saturation'],
            'note': 'Dark ambient calm. Horror movie before anything happens.',
        },
        'anxious_hope': {
            'primary': 'anxiety', 'secondary': 'hope',
            'key': 'F#_minor', 'tempo': 110, 'scale': 'harmonic_minor',
            'instruments': ['piano', 'strings', 'synth_arp', 'light_drums'],
            'effects': ['tremolo', 'delay', 'filter_sweep_opening'],
            'note': 'Nervous energy that resolves upward. Harmonic minor tension with ascending resolution.',
        },
        'nostalgic_wonder': {
            'primary': 'nostalgia', 'secondary': 'wonder',
            'key': 'Ab_major', 'tempo': 88, 'scale': 'lydian',
            'instruments': ['music_box', 'strings', 'synth_pad', 'glockenspiel'],
            'effects': ['vinyl_crackle', 'reverb', 'chorus'],
            'note': 'Childhood memory with magical overtones. Lydian #4 = sense of wonder.',
        },
        'aggressive_beauty': {
            'primary': 'aggression', 'secondary': 'beauty',
            'key': 'C_minor', 'tempo': 140, 'scale': 'harmonic_minor',
            'instruments': ['strings', 'distorted_guitar', 'choir', 'heavy_drums'],
            'effects': ['distortion', 'epic_reverb', 'compression'],
            'note': 'Beautiful orchestral harmony at intense tempo. Cinematic action.',
        },
        '3am_nostalgia': {
            'primary': 'loneliness', 'secondary': 'comfort',
            'key': 'D_minor', 'tempo': 78, 'scale': 'dorian',
            'instruments': ['rhodes', 'synth_pad', 'soft_drums', 'bass'],
            'effects': ['lo_fi_filter', 'vinyl_crackle', 'tape_delay'],
            'note': 'Late-night lo-fi. Alone but okay with it. Neo-soul warmth.',
        },
    },
}
```

## Sound Design Directions

```python
SOUND_DESIGN_TRENDS = {
    'maximalism': {
        'description': 'Dense, layered, wall-of-sound production surging in popularity (+999% searches)',
        'how_to_achieve': 'Stack 3-5 layers per element, aggressive compression, fill the frequency spectrum',
        'techniques': ['parallel_compression', 'multiband_saturation', 'dense_reverb_tails', 'layer_stacking'],
    },
    'lo_fi_evolution': {
        'description': 'Beyond simple bitcrush — into purpose-built productivity audio and textured imperfection',
        'how_to_achieve': 'Vinyl crackle, tape wobble, bit reduction, room ambience, detuned instruments',
        'techniques': ['tape_emulation', 'bit_crush', 'sample_rate_reduction', 'gentle_distortion', 'wow_flutter'],
    },
    'game_inspired': {
        'description': 'Chiptune elements in mainstream production, interactive/modular soundtrack thinking',
        'how_to_achieve': 'Square/pulse wave melodies, arpeggio patterns, 8-bit drums mixed with modern bass',
        'techniques': ['pulse_wave_synth', 'arpeggio_sequencing', 'retro_game_sfx', 'adaptive_stems'],
    },
    '1950s_nostalgia': {
        'description': 'Doo-wop progressions, vintage reverb, tape echo, pre-autotune vocal aesthetics',
        'how_to_achieve': 'Spring reverb, tape delay, simple chord progressions, breathy natural vocals',
        'techniques': ['spring_reverb', 'tape_delay', 'mono_mixing', 'vintage_eq_curves', 'doo_wop_chords'],
    },
    'organic_electronic': {
        'description': 'Field recordings integrated with synthesis — nature meets technology',
        'how_to_achieve': 'Layer environmental sounds (rain, birds, traffic) under synth pads, process organic with electronic',
        'techniques': ['granular_on_field_recordings', 'convolution_reverb', 'nature_as_percussion', 'spectral_processing'],
    },
    'textural_minimalism': {
        'description': 'Few elements but rich in texture and movement — every sound is alive',
        'how_to_achieve': 'Fewer tracks, but each with LFO modulation, slow filter sweeps, granular shimmer',
        'techniques': ['slow_filter_automation', 'granular_texture', 'generative_patterns', 'breathing_dynamics'],
    },
}
```
