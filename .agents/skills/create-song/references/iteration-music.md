# Music Iteration & Refinement

Music-specific refinement mappings, refinement chains, preservation rules, partial regeneration entries, and parameter locations. Extracted from the shared iteration-guide reference for music production use.

## REFINEMENT_MAP

Complete mapping of user requests to parameter changes for music tracks:

```python
REFINEMENT_MAP = {
    # -- Tonal / Mood Changes -----------------------------------------------
    'darker': {
        'params': ['FILTER_CUTOFF', 'BRIGHTNESS', 'REVERB_WET', 'SCALE'],
        'action': 'Lower cutoff by 30-40%. Reduce brightness dimension by 2-3 points. Increase reverb wet by 10-15%. Switch to minor scale if currently major.',
        'side_effects': ['Check that bass is still audible after lowering cutoff', 'Re-check energy map brightness values'],
    },
    'brighter': {
        'params': ['FILTER_CUTOFF', 'BRIGHTNESS', 'AIR_SHELF_GAIN'],
        'action': 'Raise cutoff by 30-40%. Increase brightness dimension by 2-3 points. Add or boost air shelf EQ at 10-12kHz by +2-3dB.',
        'side_effects': ['Check for harshness in the 3-5kHz range', 'Ensure hi-hats do not become piercing'],
    },
    'warmer': {
        'params': ['FILTER_CUTOFF', 'SATURATION_DRIVE', 'HIGH_SHELF_GAIN'],
        'action': 'Lower highpass cutoff slightly. Add subtle saturation (drive 1.2-1.5). Cut high shelf at 8kHz by -2dB. Boost low-mids around 200-400Hz by +1-2dB.',
        'side_effects': ['Check for muddiness in the 200-400Hz range', 'Ensure vocals/lead still cut through'],
    },
    'colder': {
        'params': ['REVERB_WET', 'SATURATION_DRIVE', 'HIGH_SHELF_GAIN', 'DETUNE'],
        'action': 'Increase reverb wet and decay. Remove or reduce saturation. Boost high shelf at 8kHz by +1-2dB. Add slight detune/chorus for sterility.',
        'side_effects': ['Check that track does not become too washy', 'Ensure rhythmic definition is maintained'],
    },
    'more aggressive': {
        'params': ['DISTORTION_DRIVE', 'COMPRESSION_RATIO', 'ATTACK_MS', 'HIGH_MID_BOOST'],
        'action': 'Add or increase distortion drive by 1.5-2.5x. Increase compression ratio to 6:1+. Shorten attack to 1-5ms. Boost 1-3kHz by +2-4dB for bite.',
        'side_effects': ['Check peak levels do not clip', 'Re-check LUFS target', 'Ensure bass remains tight'],
    },
    'softer': {
        'params': ['DISTORTION_DRIVE', 'COMPRESSION_RATIO', 'ATTACK_MS', 'MASTER_VOL'],
        'action': 'Remove or reduce distortion. Lower compression ratio to 2:1-3:1. Lengthen attack to 15-30ms. Reduce overall volume by 1-2dB.',
        'side_effects': ['Check that transients are not too spiky with longer attack', 'Ensure track still has presence'],
    },
    'gentler': {
        'params': ['FILTER_CUTOFF', 'VELOCITY_SCALE', 'ATTACK_MS', 'REVERB_WET'],
        'action': 'Lower cutoff by 20%. Reduce velocity/volume variation. Lengthen attack for softer transients. Increase reverb wet by 5-10%.',
        'side_effects': ['Same as softer', 'Check rhythmic clarity is maintained'],
    },
    'moodier': {
        'params': ['REVERB_DECAY', 'REVERB_WET', 'FILTER_CUTOFF', 'HARMONIC_RICHNESS', 'DELAY_WET'],
        'action': 'Increase reverb decay by 30-50%. Increase wet mix. Lower cutoff by 15-20%. Increase harmonic richness (add 7ths, 9ths). Add delay with moderate feedback.',
        'side_effects': ['Check that reverb tail does not blur chord changes', 'Ensure low end stays defined'],
    },
    'more uplifting': {
        'params': ['SCALE', 'BPM', 'BRIGHTNESS', 'ENERGY_MAP'],
        'action': 'Switch to major scale if minor. Increase BPM by 5-10. Raise brightness by 2 points. Increase energy curve for chorus sections.',
        'side_effects': ['Re-check chord progression works in new key', 'Verify melody notes are in new scale'],
    },
    'dreamier': {
        'params': ['REVERB_DECAY', 'REVERB_WET', 'CHORUS_DEPTH', 'FILTER_CUTOFF', 'DELAY_WET'],
        'action': 'Increase reverb decay to 3-5s. Increase wet to 40-60%. Add chorus with depth 0.4-0.7. Lower cutoff by 20%. Add ping-pong delay at 1/4 or 1/8.',
        'side_effects': ['Check that mix does not become a wall of wash', 'Ensure rhythmic pulse is still perceivable'],
    },
    'grittier': {
        'params': ['DISTORTION_DRIVE', 'BIT_DEPTH', 'SATURATION_DRIVE', 'NOISE_LEVEL'],
        'action': 'Add distortion drive 1.5-3.0. Optionally reduce bit depth for texture. Add tape saturation. Add subtle noise layer (-30dB).',
        'side_effects': ['Check that grit does not mask melody', 'Ensure bass frequencies are not overly distorted'],
    },
    'more ethereal': {
        'params': ['REVERB_DECAY', 'REVERB_WET', 'SHIMMER_GAIN', 'FILTER_CUTOFF', 'PAD_VOL'],
        'action': 'Long reverb (4-7s decay). High wet (50-70%). Add shimmer reverb (pitch-shifted reflections). Raise cutoff for airiness. Boost pad volume.',
        'side_effects': ['Monitor for phase issues with long reverb', 'Check mono compatibility'],
    },
    'more intense': {
        'params': ['COMPRESSION_RATIO', 'DISTORTION_DRIVE', 'ENERGY_MAP', 'DENSITY', 'BPM'],
        'action': 'Increase compression ratio to 6:1+. Add subtle drive. Raise energy map values by 1-2 across the board. Increase density. Optionally bump BPM by 3-5.',
        'side_effects': ['Check LUFS is still within target', 'Ensure dynamic range is not completely crushed'],
    },
    'more relaxed': {
        'params': ['BPM', 'COMPRESSION_RATIO', 'ENERGY_MAP', 'REVERB_WET', 'SWING'],
        'action': 'Reduce BPM by 5-15. Lower compression ratio. Drop energy map values by 1-2. Add reverb. Add slight swing (5-15%).',
        'side_effects': ['Check that groove still feels intentional at lower tempo', 'Verify song does not drag'],
    },

    # -- Mix Balance Changes ------------------------------------------------
    'drums too loud': {
        'params': ['KICK_VOL', 'SNARE_VOL', 'HAT_VOL', 'DRUM_BUS_VOL'],
        'action': 'Reduce drum bus or individual drum volumes by 3-6dB. Start with overall drum bus, then adjust individuals.',
        'side_effects': ['Check that kick still anchors the low end', 'Ensure snare still cuts through'],
    },
    'drums too quiet': {
        'params': ['KICK_VOL', 'SNARE_VOL', 'HAT_VOL', 'DRUM_BUS_VOL'],
        'action': 'Increase drum bus or individual drum volumes by 3-6dB. Add parallel compression for more punch without raw volume.',
        'side_effects': ['Check for clipping on master', 'Re-check LUFS'],
    },
    'bass too loud': {
        'params': ['BASS_VOL', 'BASS_CUTOFF', 'SUB_VOL'],
        'action': 'Reduce bass volume by 2-4dB. Consider lowering bass filter cutoff to remove upper harmonics. Check sub separately.',
        'side_effects': ['Check on both speakers and headphones reference', 'Ensure low end does not disappear on small speakers'],
    },
    'bass too quiet': {
        'params': ['BASS_VOL', 'BASS_SATURATION', 'SUB_VOL', 'BASS_COMPRESSION'],
        'action': 'Increase bass volume by 2-4dB. Add subtle saturation to create upper harmonics for small speaker audibility. Check sub level.',
        'side_effects': ['Check for frequency masking with kick', 'Ensure bass does not muddy the mix'],
    },
    'needs more bass': {
        'params': ['BASS_VOL', 'SUB_VOL', 'BASS_SATURATION', 'LOW_SHELF_GAIN'],
        'action': 'Boost bass volume by 2-4dB. Add sub layer if not present. Add saturation for harmonic presence. Boost low shelf at 80-100Hz by +2-3dB.',
        'side_effects': ['Check for kick/bass frequency conflict', 'Ensure sub does not cause rumble'],
    },
    'vocals too quiet': {
        'params': ['LEAD_VOL', 'LEAD_COMPRESSION', 'MID_EQ_BOOST'],
        'action': 'Increase lead/vocal volume by 2-4dB. Add compression (3:1-4:1) to even out dynamics. Boost 2-5kHz presence by +1-2dB.',
        'side_effects': ['Check that lead does not mask other mid-range elements', 'Re-check stereo balance'],
    },
    'too muddy': {
        'params': ['HIGHPASS_FREQ', 'LOW_MID_CUT', 'BASS_CUTOFF', 'REVERB_WET'],
        'action': 'Add highpass at 30-60Hz on all non-bass tracks. Cut 200-400Hz by 2-4dB on competing elements. Tighten bass cutoff. Reduce reverb wet on low instruments.',
        'side_effects': ['Check that warmth is preserved after cuts', 'Ensure track does not become thin'],
    },
    'too thin': {
        'params': ['LOW_MID_BOOST', 'BASS_VOL', 'SATURATION_DRIVE', 'LAYER_COUNT'],
        'action': 'Boost 150-300Hz by +2-3dB. Increase bass volume. Add subtle saturation for harmonic density. Consider adding a pad or doubling layer.',
        'side_effects': ['Check for muddiness after boosting low mids', 'Ensure clarity is maintained'],
    },
    'too harsh': {
        'params': ['HIGH_MID_CUT', 'FILTER_CUTOFF', 'DE_ESS_FREQ'],
        'action': 'Cut 2-5kHz by 2-4dB on offending tracks. Lower master cutoff slightly. Add de-essing at 4-8kHz if lead is harsh.',
        'side_effects': ['Check that presence and clarity are maintained', 'Ensure cymbals still shimmer'],
    },

    # -- Tempo & Feel Changes -----------------------------------------------
    'faster': {
        'params': ['BPM'],
        'action': 'Increase BPM by 5-15. For large jumps (20+), consider this a genre shift and check REFINEMENT_CHAINS.',
        'side_effects': ['Check that delay times still sync musically', 'Verify reverb tails fit within new bar length'],
    },
    'slower': {
        'params': ['BPM'],
        'action': 'Decrease BPM by 5-15. For large drops (20+), consider adding more rhythmic elements to fill space.',
        'side_effects': ['Check that track does not feel empty at slower tempo', 'Verify delay and reverb timing'],
    },
    'more swing': {
        'params': ['SWING_AMOUNT', 'SWING_SUBDIVISION'],
        'action': 'Increase swing to 15-30% on 8th or 16th notes. Typical values: light=10-15%, medium=20-25%, heavy=30-40%.',
        'side_effects': ['Check that swing feels musical not drunk', 'Ensure kick stays on grid if swing is only on hats/perc'],
    },
    'straighter': {
        'params': ['SWING_AMOUNT'],
        'action': 'Reduce or remove swing. Set SWING_AMOUNT to 0.',
        'side_effects': ['Check that groove still feels human if desired', 'Consider adding subtle velocity variation instead'],
    },
    'more groove': {
        'params': ['SWING_AMOUNT', 'VELOCITY_VARIATION', 'GHOST_NOTES', 'SYNCOPATION'],
        'action': 'Add swing 10-20%. Add velocity variation (+/-15-25%). Add ghost notes on snare/hat. Introduce syncopated hits.',
        'side_effects': ['Check that groove is consistent across sections', 'Ensure downbeat remains strong'],
    },
    'more robotic': {
        'params': ['SWING_AMOUNT', 'VELOCITY_VARIATION', 'TIMING_HUMANIZE'],
        'action': 'Set swing to 0. Set velocity variation to 0. Remove timing humanization. Lock all hits to exact grid.',
        'side_effects': ['Verify this is intentional (robotic feel)', 'Works well for techno, industrial, synthwave'],
    },
    'more bouncy': {
        'params': ['SWING_AMOUNT', 'SIDECHAIN_DEPTH', 'BASS_PATTERN', 'VELOCITY_ACCENT'],
        'action': 'Add swing 15-25%. Increase sidechain depth for pumping effect. Add offbeat bass notes. Accent upbeats.',
        'side_effects': ['Check that bounce does not fight the kick pattern', 'Ensure low-end stays tight'],
    },
    'heavier': {
        'params': ['KICK_VOL', 'BASS_VOL', 'DISTORTION_DRIVE', 'COMPRESSION_RATIO', 'LOW_BOOST'],
        'action': 'Boost kick and bass by 2-3dB. Add distortion drive. Increase compression. Boost 60-100Hz shelf by +2-3dB.',
        'side_effects': ['Check for low-end masking', 'Ensure master does not clip', 'Re-check LUFS'],
    },

    # -- Key & Harmony Changes ----------------------------------------------
    'change key': {
        'params': ['ROOT_NOTE', 'SCALE', 'CHORD_PROGRESSION'],
        'action': 'Update ROOT_NOTE to requested key. Transpose all note values accordingly. Keep same intervals/progression.',
        'side_effects': ['Check that all melody notes are updated', 'Verify bass notes match new root', 'Check instrument ranges still work'],
    },
    'make it minor': {
        'params': ['SCALE', 'CHORD_PROGRESSION'],
        'action': 'Change SCALE from major to natural minor (or harmonic/melodic minor). Adjust chord progression: I-IV-V becomes i-iv-v or i-VI-III-VII.',
        'side_effects': ['Check melody notes that fall on 3rd, 6th, 7th scale degrees', 'Verify emotional intent matches'],
    },
    'make it major': {
        'params': ['SCALE', 'CHORD_PROGRESSION'],
        'action': 'Change SCALE from minor to major. Adjust chord progression: i-iv-v becomes I-IV-V or I-V-vi-IV.',
        'side_effects': ['Check melody notes on 3rd, 6th, 7th degrees', 'May need to adjust overall mood/energy to match'],
    },
    'jazzier chords': {
        'params': ['CHORD_PROGRESSION', 'HARMONIC_RICHNESS', 'VOICING'],
        'action': 'Add 7ths, 9ths, 11ths, 13ths to existing chords. Use ii-V-I patterns. Add tritone substitutions. Use rootless voicings.',
        'side_effects': ['Check that bass still outlines correct roots', 'Verify no dissonant clashes with melody'],
    },
    'simpler chords': {
        'params': ['CHORD_PROGRESSION', 'HARMONIC_RICHNESS'],
        'action': 'Strip chords to triads (root, 3rd, 5th). Remove extensions. Use root position. Reduce chord changes per bar.',
        'side_effects': ['Check that track does not become boring', 'Consider adding melodic interest to compensate'],
    },
    'more dissonant': {
        'params': ['CHORD_PROGRESSION', 'DETUNE', 'INTERVAL_CHOICES'],
        'action': 'Add minor 2nds, tritones, major 7ths. Add slight detune (5-15 cents). Use cluster voicings. Add suspended chords that do not resolve.',
        'side_effects': ['Check that dissonance is musical not just unpleasant', 'Ensure some resolution points exist'],
    },
    'more consonant': {
        'params': ['CHORD_PROGRESSION', 'DETUNE', 'INTERVAL_CHOICES'],
        'action': 'Use perfect 5ths, octaves, major 3rds. Remove detune. Resolve all suspensions. Stick to diatonic harmony.',
        'side_effects': ['Check that track does not become too predictable', 'Maintain some harmonic motion'],
    },

    # -- Structure Changes --------------------------------------------------
    'make it longer': {
        'params': ['TOTAL_BARS', 'STRUCTURE', 'ENERGY_MAP'],
        'action': 'Add bars to existing sections or repeat sections. Extend by 8 or 16 bars at a time. Update energy map for new bars.',
        'side_effects': ['Check that added bars have appropriate energy transitions', 'Ensure file size is manageable'],
    },
    'make it shorter': {
        'params': ['TOTAL_BARS', 'STRUCTURE', 'ENERGY_MAP'],
        'action': 'Remove repeated sections or shorten intros/outros. Cut by 8 or 16 bars. Update energy map.',
        'side_effects': ['Check that remaining transitions still flow smoothly', 'Ensure intro and outro still exist'],
    },
    'add a drop': {
        'params': ['STRUCTURE', 'ENERGY_MAP', 'TOTAL_BARS'],
        'action': 'Insert 8-16 bar drop section after a buildup. Set energy intensity=10, density=9, brightness=10. Add 4-8 bar buildup before it with rising filter/energy.',
        'side_effects': ['Check total bar count is updated', 'Ensure energy map has contrast before the drop'],
    },
    'add a breakdown': {
        'params': ['STRUCTURE', 'ENERGY_MAP', 'TOTAL_BARS'],
        'action': 'Insert 8-16 bar breakdown. Set energy intensity=3, density=2, rhythm=2. Strip to 1-2 elements (pad + melody or just drums).',
        'side_effects': ['Check that re-entry after breakdown has impact', 'Verify element muting is handled'],
    },
    'add an intro': {
        'params': ['STRUCTURE', 'ENERGY_MAP', 'TOTAL_BARS'],
        'action': 'Prepend 4-8 bars. Start with 1-2 elements, gradually layer. Energy intensity starts at 2-3.',
        'side_effects': ['Shift all existing bar references', 'Update total bar count'],
    },
    'extend the outro': {
        'params': ['STRUCTURE', 'ENERGY_MAP', 'TOTAL_BARS'],
        'action': 'Add 4-8 bars at the end. Gradually strip elements. Apply fade-out or filter sweep down in final 4 bars.',
        'side_effects': ['Check that fade is smooth', 'Ensure energy map decreases gradually'],
    },
    'add a bridge': {
        'params': ['STRUCTURE', 'ENERGY_MAP', 'CHORD_PROGRESSION', 'TOTAL_BARS'],
        'action': 'Insert 8 bar section with contrasting harmony (borrow from relative key, use IV or vi as new tonal center). Energy: moderate, density: reduced.',
        'side_effects': ['Check that transition back to chorus works', 'Verify new chords resolve properly'],
    },
    'remove a section': {
        'params': ['STRUCTURE', 'ENERGY_MAP', 'TOTAL_BARS'],
        'action': 'Remove the specified section bars. Update energy map. Add a 1-2 bar transition to smooth the edit point.',
        'side_effects': ['Check that remaining flow is coherent', 'Verify total bar count is correct'],
    },

    # -- Instrument Changes -------------------------------------------------
    'remove instrument': {
        'params': ['TRACK_LIST', 'MIX_SECTION'],
        'action': 'Comment out or remove the instrument builder call and its mixing entry. Remove associated volume, pan, and effect entries.',
        'side_effects': ['Check that frequency spectrum is not left empty in a range', 'Consider if another element should fill the gap'],
    },
    'add instrument': {
        'params': ['TRACK_LIST', 'MIX_SECTION', 'FREQUENCY_SLOTS'],
        'action': 'Add builder function call, assign frequency slot, volume, pan, and effects. Slot into energy map density threshold.',
        'side_effects': ['Check for frequency slot conflicts with existing instruments', 'Verify panning maintains stereo balance'],
    },
    'change instrument sound': {
        'params': ['WAVEFORM', 'FILTER_CUTOFF', 'ENVELOPE', 'EFFECTS'],
        'action': 'Modify the instrument builder parameters: waveform type, filter settings, ADSR envelope, and effect chain. Keep volume and pan unchanged.',
        'side_effects': ['Check that new timbre fits the frequency slot', 'Verify no masking with other instruments'],
    },
}
```

## REFINEMENT_CHAINS

Common compound refinement operations with ordered parameter changes:

```python
REFINEMENT_CHAINS = {
    'make it darker': [
        {'param': 'FILTER_CUTOFF',  'action': 'reduce by 30-40%'},
        {'param': 'BRIGHTNESS',     'action': 'reduce by 2-3 points in energy map'},
        {'param': 'REVERB_WET',     'action': 'increase by 10-15%'},
        {'param': 'REVERB_DECAY',   'action': 'increase by 20-30%'},
        {'param': 'SCALE',          'action': 'switch to minor if currently major'},
        {'param': 'HIGH_SHELF',     'action': 'cut 8kHz+ by -2 to -3dB'},
    ],
    'make it punchier': [
        {'param': 'COMPRESSION_RATIO', 'action': 'increase to 4:1-6:1 on drums'},
        {'param': 'ATTACK_MS',         'action': 'shorten to 1-5ms'},
        {'param': 'TRANSIENT_BOOST',   'action': 'boost attack transients by +3-4dB'},
        {'param': 'REVERB_DECAY',      'action': 'shorten to 0.3-0.8s'},
        {'param': 'KICK_PRESENCE',     'action': 'boost 2-4kHz by +2-3dB on kick'},
        {'param': 'SIDECHAIN_DEPTH',   'action': 'increase for more pumping contrast'},
    ],
    'make it wider': [
        {'param': 'STEREO_WIDTH',   'action': 'increase mid/side width ratio'},
        {'param': 'HAAS_DELAY',     'action': 'add 5-15ms delay on selected elements (L or R)'},
        {'param': 'PAN_SPREAD',     'action': 'widen panning — move elements further from center'},
        {'param': 'CHORUS_DEPTH',   'action': 'add chorus to pads and synths (depth 0.3-0.5)'},
        {'param': 'REVERB_STEREO',  'action': 'use stereo reverb with different L/R early reflections'},
    ],
    'make it more professional': [
        {'param': 'HIGHPASS_FREQ',     'action': 'add 30-40Hz highpass on master to remove rumble'},
        {'param': 'LOW_END_MONO',      'action': 'ensure everything below 150Hz is mono'},
        {'param': 'SATURATION_DRIVE',  'action': 'add subtle saturation (drive 1.1-1.3) on mix bus'},
        {'param': 'STEREO_IMAGE',      'action': 'check and fix stereo balance and correlation'},
        {'param': 'LUFS_TARGET',       'action': 'match genre-appropriate LUFS (-14 streaming, -9 to -6 club)'},
        {'param': 'FREQUENCY_BALANCE', 'action': 'EQ to match genre reference curve'},
    ],
    'make it more lo-fi': [
        {'param': 'VINYL_NOISE',      'action': 'add vinyl crackle layer at -28 to -24dB'},
        {'param': 'SAMPLE_RATE_CRUSH','action': 'downsample to 22050 or 16000 then back up'},
        {'param': 'WOW_FLUTTER',      'action': 'add pitch modulation: 0.5-2Hz rate, 5-15 cent depth'},
        {'param': 'FILTER_CUTOFF',    'action': 'lower master cutoff to 8-10kHz'},
        {'param': 'TAPE_SATURATION',  'action': 'add warm tape saturation (drive 1.3-1.8)'},
        {'param': 'BIT_DEPTH',        'action': 'optionally reduce to 12-bit for texture'},
    ],
    'make it more epic': [
        {'param': 'REVERB_DECAY',   'action': 'increase to 3-5s for hall-like space'},
        {'param': 'LAYER_COUNT',    'action': 'double key elements (octave layers, stereo doublings)'},
        {'param': 'DENSITY',        'action': 'increase energy map density by 2-3 points'},
        {'param': 'TIMPANI_HITS',   'action': 'add percussive impacts on downbeats in climax'},
        {'param': 'DYNAMICS',       'action': 'increase contrast: quieter verses, louder chorus (+4-6dB)'},
        {'param': 'SUB_BASS',       'action': 'add sub layer for weight in big sections'},
    ],
    'make it more minimal': [
        {'param': 'DENSITY',         'action': 'reduce energy map density by 3-4 points'},
        {'param': 'TRACK_COUNT',     'action': 'remove 2-4 non-essential layers'},
        {'param': 'REVERB_WET',      'action': 'reduce to 10-20% — drier sound'},
        {'param': 'EFFECTS_CHAIN',   'action': 'strip effects to basics: EQ + compression only'},
        {'param': 'HARMONIC_RICHNESS','action': 'simplify chords to triads or dyads'},
        {'param': 'WHITESPACE',      'action': 'add rests and space between phrases'},
    ],
    'make it more energetic': [
        {'param': 'BPM',              'action': 'increase by 5-10'},
        {'param': 'DENSITY',          'action': 'increase across all sections by 1-2'},
        {'param': 'RHYTHM_COMPLEXITY','action': 'add 16th note patterns, offbeat hats'},
        {'param': 'COMPRESSION_RATIO','action': 'increase to 4:1+ on mix bus'},
        {'param': 'BRIGHTNESS',       'action': 'increase by 2 points for more presence'},
        {'param': 'SIDECHAIN_DEPTH',  'action': 'add or increase for rhythmic pumping'},
    ],
    'make it more spacious': [
        {'param': 'REVERB_DECAY',  'action': 'increase to 2-4s'},
        {'param': 'REVERB_WET',    'action': 'increase to 30-45%'},
        {'param': 'DELAY_WET',     'action': 'add stereo delay at 15-25% wet'},
        {'param': 'DENSITY',       'action': 'reduce by 1-2 to create room'},
        {'param': 'PAN_SPREAD',    'action': 'widen panning for openness'},
        {'param': 'PRE_DELAY',     'action': 'add 20-40ms pre-delay on reverb for clarity'},
    ],
    'make it more vintage': [
        {'param': 'TAPE_SATURATION', 'action': 'add warm saturation (drive 1.2-1.6)'},
        {'param': 'FILTER_CUTOFF',   'action': 'roll off above 12-14kHz'},
        {'param': 'CHORUS_DEPTH',    'action': 'add analog-style chorus (rate 0.5-1Hz)'},
        {'param': 'STEREO_WIDTH',    'action': 'narrow slightly — vintage mixes were less wide'},
        {'param': 'COMPRESSION',     'action': 'use slower attack (15-30ms) and release (100-200ms)'},
        {'param': 'NOISE_FLOOR',     'action': 'add subtle hiss at -36 to -30dB'},
    ],
    'make it more modern': [
        {'param': 'COMPRESSION_RATIO', 'action': 'increase for louder, more compressed sound'},
        {'param': 'STEREO_WIDTH',      'action': 'widen — modern mixes use full stereo field'},
        {'param': 'SUB_BASS',          'action': 'add dedicated sub layer (30-60Hz)'},
        {'param': 'BRIGHTNESS',        'action': 'boost air frequencies (10-16kHz) by +2-3dB'},
        {'param': 'SIDECHAIN_DEPTH',   'action': 'add sidechain pumping for rhythmic movement'},
        {'param': 'TRANSIENT_DESIGN',  'action': 'sharpen transients on drums and leads'},
    ],
    'make it more atmospheric': [
        {'param': 'REVERB_DECAY',    'action': 'long decay 3-6s'},
        {'param': 'DELAY_FEEDBACK',  'action': 'increase feedback to 40-60% for echoing layers'},
        {'param': 'PAD_VOL',         'action': 'increase pad presence by 2-3dB'},
        {'param': 'NOISE_TEXTURE',   'action': 'add ambient noise bed (rain, wind, texture) at -24dB'},
        {'param': 'FILTER_MOD',      'action': 'add slow filter modulation (0.1-0.5Hz LFO)'},
        {'param': 'DENSITY',         'action': 'reduce rhythmic density, increase textural density'},
    ],
}
```

## Music-Specific PRESERVE_RULES

Parameters that should always be preserved during iteration (unless the user explicitly requests a change):

```python
PRESERVE_RULES = {
    'ALWAYS_PRESERVE': [
        'BPM',                    # Unless user explicitly says "change tempo"
        'TIME_SIGNATURE',         # Almost never changes mid-iteration
        'SONG_STRUCTURE',         # Section order (intro-verse-chorus-etc) unless asked
        'CHORD_PROGRESSION',      # Harmonic foundation, very disruptive to change
        'ROOT_NOTE',              # Unless key change requested
    ],
}
```

## Music-Specific PARTIAL_REGENERATE

When to regenerate only part of the track:

```python
PARTIAL_REGENERATE = {
    'different chorus melody':   'Regenerate only the melody builder output for chorus bar range. Keep all other tracks.',
    'different drum pattern':    'Regenerate only the drum builder. Keep kick timing if user likes the groove.',
    'different bass line':       'Regenerate bass builder. Keep root notes matching chord progression.',
    'different intro':           'Regenerate first N bars of all tracks. Keep the rest intact.',
    'different chord voicings':  'Regenerate chord/pad builder with new voicings. Keep progression and rhythm.',
    'different effects':         'Rebuild effect chains only. Keep all builder outputs and mix levels.',
    'different arrangement':     'Rebuild energy map and structure. Keep all instrument timbres and mix settings.',
}
```

## Music-Specific PARAM_LOCATIONS

Where to find key music parameters in a generated script:

```python
PARAM_LOCATIONS = {
    'BPM': {
        'location': 'Top-level constant, typically lines 5-15',
        'pattern':  'BPM = <int>',
        'example':  'BPM = 120',
    },
    'ROOT_NOTE': {
        'location': 'Top-level constant, typically lines 5-15',
        'pattern':  'ROOT = <int> or ROOT_NOTE = <int>',
        'example':  'ROOT = 60  # Middle C',
    },
    'SCALE': {
        'location': 'Top-level constant, typically lines 5-20',
        'pattern':  'SCALE = [<intervals>] or SCALE_TYPE = "<name>"',
        'example':  'SCALE = [0, 2, 4, 5, 7, 9, 11]  # Major',
    },
    'TIME_SIGNATURE': {
        'location': 'Top-level constant, typically lines 5-15',
        'pattern':  'BEATS_PER_BAR = <int>',
        'example':  'BEATS_PER_BAR = 4',
    },
    'TOTAL_BARS': {
        'location': 'Top-level constant, typically lines 10-25',
        'pattern':  'TOTAL_BARS = <int>',
        'example':  'TOTAL_BARS = 64',
    },
    'ENERGY_MAP': {
        'location': 'Near top, after constants, typically lines 15-50',
        'pattern':  'energy_map = {...} or SECTION_ENERGY = {...} or sections = [...]',
        'example':  "SECTION_ENERGY = {'intro': {'intensity': 3, ...}, ...}",
    },
    'CHORD_PROGRESSION': {
        'location': 'Near top or in harmony section, lines 20-50',
        'pattern':  'chords = [...] or PROGRESSION = [...] or chord_degrees = [...]',
        'example':  'PROGRESSION = [0, 5, 7, 3]  # I-IV-V-ii in semitones from root',
    },
    'STRUCTURE': {
        'location': 'Near energy map, lines 15-50',
        'pattern':  'STRUCTURE = [...] or sections = {...} or arrangement = [...]',
        'example':  "STRUCTURE = ['intro']*4 + ['verse']*8 + ['chorus']*8 + ...",
    },
}
```
