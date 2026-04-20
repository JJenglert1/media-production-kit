# Music Automation & Modulation

Music-specific genre automation recommendations, automation plan builder, and energy-based depth scaling. Extracted from the shared automation-modulation reference for music production use.

## Per-Genre Automation Recommendations (GENRE_AUTOMATION)

```python
GENRE_AUTOMATION = {
    'lo-fi': {
        'filter': ['lo_fi_drift on master bus', 'slow cutoff wander 2-5kHz range'],
        'pitch': ['wow_flutter: random pitch drift +/-5 cents at 0.3-1Hz'],
        'volume': ['subtle breathing: amplitude LFO 0.08Hz depth 0.08'],
        'character': 'Nothing should feel precise. Imperfection is the aesthetic.',
    },
    'synthwave': {
        'filter': ['pad filter sweep open on chorus (breathe template)', 'arp filter open_build on pre-chorus'],
        'pitch': ['slow vibrato on leads 4.5Hz depth 0.02 semitones'],
        'volume': ['pad volume swell 0.1Hz', 'sidechain pump on chords 15-25%'],
        'character': 'Lush, wide, evolving. Every pad must breathe.',
    },
    'trap': {
        'filter': ['808 filter sweep on builds', 'hi-hat filter close for variation'],
        'pitch': ['808 slide automation: pitch bend -12 to 0 semitones over 100-300ms'],
        'volume': ['hi-hat velocity ramps ascending over 2 bars', 'volume chops on vocal samples'],
        'character': 'Hard contrast between static 808 and automated builds.',
    },
    'house': {
        'filter': ['sidechain_filter on pads (pump feel)', 'open_build on transitions'],
        'pitch': ['minimal - keep it locked to the groove'],
        'volume': ['sidechain pump as primary modulation 20-40%', 'pad breathing 0.1Hz'],
        'character': 'The sidechain IS the modulation. Filter sweeps for transitions only.',
    },
    'ambient': {
        'filter': ['spectral_tilt over 32 bars', 'lo_fi_drift on everything'],
        'pitch': ['granular position sweep 0.02Hz', 'pitch drift +/-10 cents at 0.05Hz'],
        'volume': ['long crossfades 4-8 bars', 'amplitude LFO 0.03Hz depth 0.15'],
        'character': 'Everything slow (0.02-0.2Hz). No sudden changes. Glacial movement.',
    },
    'trance': {
        'filter': ['supersaw filter open_build 16 bars into chorus', 'gated pluck volume LFO synced to tempo'],
        'pitch': ['pitch risers over 8-16 bars before drop'],
        'volume': ['gated trance LFO: square wave at 1/8 note rate, depth 0.8', 'long riser volumes'],
        'character': 'Tempo-synced gates and long sweeps. Build tension relentlessly.',
    },
    'dnb': {
        'filter': ['reese bass filter sweep (wobble template, rate 0.5-1Hz)', 'breakbeat filter automation'],
        'pitch': ['reese bass pitch modulation +/-2 semitones at 0.3Hz'],
        'volume': ['pad slow swell 0.05Hz', 'break chop volume automation'],
        'character': 'Reese movement is essential. Pads contrast with frenetic breaks.',
    },
    'jazz': {
        'filter': ['no filter automation (natural sound)', 'subtle high shelf drift if any'],
        'pitch': ['vibrato on leads 5Hz depth 0.02 semitones (emulate breath/bow)'],
        'volume': ['velocity dynamics only (no LFOs)', 'natural volume swells via performance'],
        'character': 'No synthetic modulation. All movement from dynamics and performance.',
    },
    'metal': {
        'filter': ['no filter LFOs (tight and precise)', 'wah on solos only (lead_filter template)'],
        'pitch': ['vibrato on solo bends only', 'no pitch drift on rhythm parts'],
        'volume': ['feedback automation on lead channel', 'tight palm-mute gating'],
        'character': 'Precision is king. Automation only for solos and transitions.',
    },
    'techno': {
        'filter': ['long filter sweeps over 16-32 bars', 'resonance_peak on acid lines'],
        'pitch': ['minimal - lock to the grid'],
        'volume': ['sidechain pump 20-35%', 'slow volume builds over 8+ bars'],
        'character': 'Hypnotic repetition with glacial filter movement. Less is more.',
    },
    'pop': {
        'filter': ['subtle filter open on pre-chorus to chorus', 'close_intimate for verse'],
        'pitch': ['lead vibrato 5Hz depth 0.015 semitones', 'pitch correction artifacts for effect'],
        'volume': ['volume automation rides on vocals (manual feel)', 'sidechain on synths 10-15%'],
        'character': 'Transparent automation. Listener should feel the movement, not hear the trick.',
    },
    'r&b': {
        'filter': ['slow pad filter breathing', 'lo_fi_drift on keys for warmth'],
        'pitch': ['vibrato on vocal synths 4.5Hz depth 0.02', 'subtle pitch drift on pads'],
        'volume': ['sidechain pump 10-20% (gentle)', 'pad breathing 0.08Hz'],
        'character': 'Smooth, warm, intimate. Modulation should feel like a human touch.',
    },
    'dubstep': {
        'filter': ['wobble template on bass (primary sound)', 'aggressive filter sweeps on builds'],
        'pitch': ['bass pitch drops on accents', 'pitch risers before drops'],
        'volume': ['hard volume gating for rhythmic bass', 'sidechain pump 30-50%'],
        'character': 'The wobble bass IS the genre. Filter LFO rate = rhythmic identity.',
    },
    'funk': {
        'filter': ['auto-wah on guitar (lead_filter, rate synced to 16th notes)', 'envelope follower feel'],
        'pitch': ['vibrato on held notes only'],
        'volume': ['dynamic playing is the modulation', 'clavinet velocity sensitivity'],
        'character': 'Groove-driven. Wah and dynamics, nothing synthetic.',
    },
}
```

## build_automation_plan()

Generate automation plan for a section based on energy map and genre. References GENRE_AUTOMATION and music section names (intro, verse, breakdown, etc.).

```python
def build_automation_plan(section, genre, instruments):
    """Generate automation plan for a section based on energy map and genre.

    Args:
        section: dict with 'name', 'energy' (5-dim dict), 'transition_out', etc.
                 from the COMPOSITION_PLAN in energy-and-engagement.md
        genre: string, lowercase genre name (key into GENRE_AUTOMATION)
        instruments: list of instrument name strings active in this section

    Returns:
        dict with optional keys:
            'filter_sweep': a FILTER_SWEEP_TEMPLATES entry (possibly modified)
            'lfo': list of LFO config dicts with rate, depth, shape, target
    """
    plan = {}
    energy = section.get('energy', {})
    energy_level = energy.get('intensity', 5)
    transition_out = section.get('transition_out', None)
    section_name = section.get('name', '').lower()

    # --- Auto-assign filter sweeps based on section type and transition ---
    if transition_out == 'build' or transition_out == 'riser':
        sweep = dict(FILTER_SWEEP_TEMPLATES['open_build'])
        # Scale duration to section length if provided
        if 'bars' in section:
            sweep['duration_bars'] = min(section['bars'], 16)
        plan['filter_sweep'] = sweep

    elif transition_out == 'filter_open':
        plan['filter_sweep'] = dict(FILTER_SWEEP_TEMPLATES['open_build'])

    elif 'breakdown' in section_name or 'bridge' in section_name:
        plan['filter_sweep'] = dict(FILTER_SWEEP_TEMPLATES['close_intimate'])

    elif 'verse' in section_name:
        # Gentle breathing on verses
        plan['filter_sweep'] = dict(FILTER_SWEEP_TEMPLATES['breathe'])

    elif 'intro' in section_name:
        sweep = dict(FILTER_SWEEP_TEMPLATES['spectral_tilt'])
        sweep['cutoff_start'] = 800
        sweep['cutoff_end'] = 6000
        plan['filter_sweep'] = sweep

    # --- Genre-specific filter override ---
    genre_cfg = GENRE_AUTOMATION.get(genre, {})
    if genre == 'dubstep' and any('bass' in inst for inst in instruments):
        plan['filter_sweep'] = dict(FILTER_SWEEP_TEMPLATES['wobble'])
    elif genre == 'techno' and energy_level >= 6:
        if 'filter_sweep' not in plan:
            sweep = dict(FILTER_SWEEP_TEMPLATES['spectral_tilt'])
            sweep['duration_bars'] = section.get('bars', 16)
            plan['filter_sweep'] = sweep

    # --- Auto-assign LFOs based on genre defaults and active instruments ---
    lfo_list = []

    # Depth scaling by energy level
    depth_scale = _depth_scale_for_energy(energy_level)

    for inst in instruments:
        # Match instrument to LFO assignments
        if 'pad' in inst:
            cfg = LFO_ASSIGNMENTS['pad_filter']
            lfo_list.append({
                'rate': np.random.uniform(*cfg['rate_hz']),
                'depth': np.random.uniform(*cfg['depth']) * depth_scale,
                'shape': cfg['shape'],
                'target': cfg['target'],
            })
            cfg_vol = LFO_ASSIGNMENTS['pad_volume']
            lfo_list.append({
                'rate': np.random.uniform(*cfg_vol['rate_hz']),
                'depth': np.random.uniform(*cfg_vol['depth']) * depth_scale,
                'shape': cfg_vol['shape'],
                'target': cfg_vol['target'],
            })

        elif 'lead' in inst or 'melody' in inst:
            cfg = LFO_ASSIGNMENTS['lead_vibrato']
            lfo_list.append({
                'rate': np.random.uniform(*cfg['rate_hz']),
                'depth': np.random.uniform(*cfg['depth']) * depth_scale,
                'shape': cfg['shape'],
                'target': cfg['target'],
            })
            if genre in ('synthwave', 'trance', 'dubstep', 'dnb'):
                cfg_f = LFO_ASSIGNMENTS['lead_filter']
                lfo_list.append({
                    'rate': np.random.uniform(*cfg_f['rate_hz']),
                    'depth': np.random.uniform(*cfg_f['depth']) * depth_scale,
                    'shape': cfg_f['shape'],
                    'target': cfg_f['target'],
                })

        elif 'bass' in inst and genre in ('dubstep', 'dnb'):
            cfg = LFO_ASSIGNMENTS['bass_wobble']
            lfo_list.append({
                'rate': np.random.uniform(*cfg['rate_hz']),
                'depth': np.random.uniform(*cfg['depth']) * depth_scale,
                'shape': cfg['shape'],
                'target': cfg['target'],
            })

        elif 'hat' in inst or 'hihat' in inst:
            cfg = LFO_ASSIGNMENTS['hat_pan']
            lfo_list.append({
                'rate': np.random.uniform(*cfg['rate_hz']),
                'depth': np.random.uniform(*cfg['depth']) * depth_scale,
                'shape': cfg['shape'],
                'target': cfg['target'],
            })

        elif 'arp' in inst:
            cfg = LFO_ASSIGNMENTS['auto_pan']
            lfo_list.append({
                'rate': np.random.uniform(*cfg['rate_hz']),
                'depth': np.random.uniform(*cfg['depth']) * depth_scale,
                'shape': cfg['shape'],
                'target': cfg['target'],
            })

    # Genre-specific additional LFOs
    if genre == 'lo-fi':
        lfo_list.append({
            'rate': np.random.uniform(0.3, 1.0),
            'depth': 0.004,
            'shape': 'sine',
            'target': 'pitch',
            'notes': 'wow_flutter',
        })
    elif genre == 'trance':
        lfo_list.append({
            'rate': bpm / 60.0 / 2,  # 8th note rate
            'depth': 0.8,
            'shape': 'square',
            'target': 'amplitude',
            'notes': 'trance_gate',
        })

    if lfo_list:
        plan['lfo'] = lfo_list

    return plan
```

## _depth_scale_for_energy()

```python
def _depth_scale_for_energy(energy_level):
    """Scale modulation depth based on energy level (1-10).

    Low energy = subtle movement. High energy = can be aggressive.
    """
    if energy_level <= 3:
        return 0.5
    elif energy_level <= 6:
        return 1.0
    elif energy_level <= 9:
        return 1.4
    else:
        return 1.0  # Peak energy: pull back or go raw depending on genre
```
