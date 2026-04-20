# SFX Iteration & Refinement

SFX-specific refinement mappings and version management for sound effect iteration.

---

## Refinement Map

When the user requests a change, look up the keyword in this map to determine which parameters to adjust and how.

```python
REFINEMENT_MAP = {
    'shorter': {
        'params': ['DURATION'],
        'action': 'Reduce duration by 30-50%. Tighten envelope decay.'
    },
    'longer': {
        'params': ['DURATION', 'REVERB_DECAY'],
        'action': 'Extend duration by 50-100%. Lengthen decay/reverb tail.'
    },
    'punchier': {
        'params': ['ATTACK_MS', 'TRANSIENT_BOOST', 'COMPRESSION'],
        'action': 'Shorten attack to 0.5-2ms. Boost transient by +3-6dB. Add compression 6:1.'
    },
    'softer': {
        'params': ['ATTACK_MS', 'FILTER_CUTOFF', 'VOLUME'],
        'action': 'Lengthen attack to 10-50ms. Lower cutoff. Reduce volume.'
    },
    'higher pitch': {
        'params': ['FREQ_START', 'FREQ_END'],
        'action': 'Raise all frequencies by 50-100%. Adjust filter cutoffs up.'
    },
    'lower pitch': {
        'params': ['FREQ_START', 'FREQ_END'],
        'action': 'Lower all frequencies by 30-50%. Add sub layer if needed.'
    },
    'more reverb': {
        'params': ['REVERB_DECAY', 'REVERB_WET'],
        'action': 'Increase decay by 50-100%. Increase wet mix by 10-20%.'
    },
    'less reverb': {
        'params': ['REVERB_DECAY', 'REVERB_WET'],
        'action': 'Reduce decay by 50%. Reduce wet mix.'
    },
    'brighter': {
        'params': ['FILTER_CUTOFF', 'HIGH_SHELF'],
        'action': 'Raise cutoff by 50-100%. Boost high shelf at 8kHz +2-3dB.'
    },
    'darker': {
        'params': ['FILTER_CUTOFF', 'HIGH_SHELF'],
        'action': 'Lower cutoff by 30-50%. Cut high shelf.'
    },
    'more distortion': {
        'params': ['DRIVE', 'SATURATION'],
        'action': 'Increase drive/saturation by 1.5-2x. Consider bitcrushing.'
    },
    'cleaner': {
        'params': ['DRIVE', 'SATURATION'],
        'action': 'Remove or reduce distortion. Clean up signal path.'
    },
    'more impact': {
        'params': ['SUB_FREQ', 'SUB_VOL', 'TRANSIENT'],
        'action': 'Add or boost sub layer (30-60Hz). Boost transient. Add compression.'
    },
    'more metallic': {
        'params': ['PARTIALS', 'FM_RATIO'],
        'action': 'Add inharmonic partials. Use irrational FM ratios (1.414, 1.732).'
    },
    'more organic': {
        'params': ['NOISE_MIX', 'RANDOMNESS', 'FILTER_MOD'],
        'action': 'Add noise layer. Increase randomization. Add slow filter modulation.'
    },
    'more sci-fi': {
        'params': ['FM_INDEX', 'FREQ_SWEEP', 'CHORUS'],
        'action': 'Add FM synthesis. Add frequency sweeps. Add chorus/phaser.'
    },
    'add tail': {
        'params': ['REVERB_DECAY', 'DELAY'],
        'action': 'Add reverb (1-3s decay). Add delay feedback 0.3-0.5.'
    },
    'tighter': {
        'params': ['DURATION', 'REVERB_WET', 'RELEASE'],
        'action': 'Shorten duration. Remove/reduce reverb. Tighten release.'
    },
    'add sub': {
        'params': ['SUB_FREQ', 'SUB_VOL', 'SUB_DECAY'],
        'action': 'Add sine sub layer at 30-60Hz. Decay 100-500ms.'
    },
    'more variations': {
        'params': ['SEED', 'VARIATION_COUNT'],
        'action': 'Generate N variations with different random seeds. Vary timing, pitch, filter by +-10-20%.'
    },
}
```

---

## How to Apply Refinements

1. Parse the user's request — match keywords to the REFINEMENT_MAP keys. Multiple keywords can apply (e.g., "punchier and brighter" = two entries).
2. Identify parameters — look at the `params` list to find the variables in the current script.
3. Apply the action — follow the specific guidance in the `action` field.
4. Preserve the rest — do not change unrelated parameters. The user's intent is surgical.
5. Re-run and validate — execute the modified script and confirm the change is audible.

### Multi-Keyword Requests

When the user combines multiple descriptors:
- "punchier and brighter" — apply both `punchier` and `brighter` entries
- "shorter with more reverb" — apply `shorter` (tighter body) + `more reverb` (longer tail, creating a compressed-then-reverbed effect)
- "lower pitch and more impact" — apply `lower pitch` + `more impact` (sub layer reinforcement)

### Intensity Modifiers

Scale the adjustments based on qualifier words:
- "slightly" / "a bit" — apply 50% of the suggested range
- (no qualifier) — apply the midpoint of the suggested range
- "much" / "way more" / "a lot" — apply 150% of the suggested range or exceed the range
- "extremely" — push to the limits; this is where sound design gets creative

---

## Version Management

### Filename Convention

SFX files follow the pattern: `{description}_v{N}.wav`

Examples:
- `laser_v1.wav` → `laser_v2.wav` → `laser_v3.wav`
- `sword_clash_v1.wav` → `sword_clash_v2.wav`
- `ui_click_v1.wav` → `ui_click_v2.wav`

### Rules

1. Never overwrite — always increment the version number.
2. First version is always `_v1`.
3. Check for existing files before writing — find the highest existing version and increment.
4. Keep all versions — let the user decide which to keep/delete.
5. Variation packs use a sub-numbering: `footstep_v1_01.wav`, `footstep_v1_02.wav`, etc.

### Version Detection

```python
import glob
import re

def next_version(base_name, output_dir='.'):
    """Find the next available version number for a sound effect.

    Args:
        base_name: Base filename without version (e.g., 'laser', 'sword_clash')
        output_dir: Directory to check for existing files

    Returns:
        Full filename with next version (e.g., 'laser_v3.wav')
    """
    pattern = f"{base_name}_v*.wav"
    existing = glob.glob(os.path.join(output_dir, pattern))

    if not existing:
        return f"{base_name}_v1.wav"

    # Extract version numbers
    versions = []
    for f in existing:
        match = re.search(r'_v(\d+)\.wav$', f)
        if match:
            versions.append(int(match.group(1)))

    next_v = max(versions) + 1 if versions else 1
    return f"{base_name}_v{next_v}.wav"
```

### Script Version Tracking

When iterating on a script, track changes with a comment block at the top:

```python
# === SFX Generation Script ===
# Sound: Laser beam
# Version: 3
# Changes from v2: Raised pitch by 50%, added FM modulation
# Output: laser_v3.wav
# =============================
```

---

## Common Iteration Patterns

### "It sounds too digital / artificial"
Apply these in order:
1. Add noise layer at -15 to -10 dB
2. Add subtle random pitch variation (+-1-3%)
3. Add slow filter modulation (0.5-2 Hz LFO on cutoff)
4. Add very short pre-delay (1-5ms) before main sound
5. Add subtle room reverb (dry/wet 90/10, 0.3s decay)

### "It doesn't sound like {real thing}"
Research the real-world sound again. Focus on:
1. What is the actual frequency content? (Often lower than expected)
2. What is the actual envelope? (Often longer attack than expected)
3. What are the multiple components? (Real sounds are always layered)
4. Is there room/environment contribution? (Almost always yes)

### "Make it game-ready"
1. Normalize to -1 dBFS
2. Trim silence (max 5ms at start, 10ms at end)
3. Ensure mono (unless stereo is needed for the specific use case)
4. Set sample rate to 48kHz
5. Export as 16-bit WAV (smaller files, standard for game engines)
6. Generate 3-5 variations with seeded randomness

### "Make it loop seamlessly"
1. Generate extra length (2x desired loop length)
2. Find zero-crossing points near desired loop boundaries
3. Crossfade at boundaries (50-100ms cosine crossfade)
4. Verify by concatenating 3 copies and checking for clicks at join points

### "I need a whole set of these"
1. Define the variation parameters (what changes between each sound)
2. Use seeded randomness — increment seed for each variation
3. Keep the core character consistent — vary only secondary parameters by +-10-20%
4. Name with sub-numbering: `footstep_stone_v1_01.wav` through `footstep_stone_v1_05.wav`
5. Optionally vary timing/pitch slightly for natural feel
