# Soundscape Iteration & Refinement

Soundscape-specific refinement mappings and version management for iterative improvement. Use alongside [iteration-core.md](../../../shared/references/iteration-core.md) for the general refinement workflow.

## Refinement Map

When the user requests changes, map their feedback to specific parameter adjustments:

```python
REFINEMENT_MAP = {
    'more rain': { 'params': ['RAIN_VOL', 'RAIN_DENSITY', 'RAIN_CUTOFF'], 'action': 'Increase rain layer volume by 3-6dB. Increase density. Raise cutoff for heavier rain.' },
    'less rain': { 'params': ['RAIN_VOL', 'RAIN_DENSITY'], 'action': 'Reduce rain volume by 3-6dB. Lower density for lighter rain.' },
    'more wind': { 'params': ['WIND_VOL', 'WIND_DEPTH', 'GUST_FREQUENCY'], 'action': 'Increase wind volume. Increase gust depth/frequency.' },
    'less wind': { 'params': ['WIND_VOL', 'GUST_FREQUENCY'], 'action': 'Reduce wind volume. Reduce gust frequency.' },
    'add thunder': { 'params': ['THUNDER_FREQUENCY', 'THUNDER_DISTANCE'], 'action': 'Add thunder events (1-3 per minute). Set distance parameter for timing.' },
    'add birds': { 'params': ['BIRD_FREQUENCY', 'BIRD_TYPES'], 'action': 'Add bird call events. Vary pitch/timing.' },
    'deeper': { 'params': ['REVERB_DECAY', 'REVERB_WET', 'DEPTH_DIM'], 'action': 'Increase reverb decay to 3-6s. Increase wet. Raise depth dimension.' },
    'more intimate': { 'params': ['REVERB_DECAY', 'REVERB_WET', 'DEPTH_DIM'], 'action': 'Reduce reverb. Lower depth dimension. Bring elements closer.' },
    'more active': { 'params': ['EVENT_DENSITY', 'ACTIVITY_DIM'], 'action': 'Increase event frequency. Raise activity dimension by 2-3.' },
    'calmer': { 'params': ['EVENT_DENSITY', 'ACTIVITY_DIM', 'MOVEMENT_DIM'], 'action': 'Reduce event frequency. Lower activity and movement. Remove sudden events.' },
    'darker': { 'params': ['FILTER_CUTOFF', 'BRIGHTNESS_DIM'], 'action': 'Lower cutoff on all layers. Reduce brightness dimension.' },
    'brighter': { 'params': ['FILTER_CUTOFF', 'BRIGHTNESS_DIM', 'HIGH_SHELF'], 'action': 'Raise cutoff. Increase brightness. Boost high shelf.' },
    'longer': { 'params': ['DURATION'], 'action': 'Extend duration. Stretch evolution map proportionally.' },
    'shorter': { 'params': ['DURATION'], 'action': 'Reduce duration. Compress evolution map.' },
    'more evolving': { 'params': ['MOVEMENT_DIM', 'LFO_RATES'], 'action': 'Increase movement dimension. Speed up LFO rates slightly.' },
    'more static': { 'params': ['MOVEMENT_DIM', 'LFO_RATES'], 'action': 'Reduce movement. Slow or remove LFOs. Make bed layer constant.' },
    'make it loopable': { 'params': ['CROSSFADE_DURATION', 'LOOP_MODE'], 'action': 'Add crossfade loop at boundaries (2-5s). Ensure continuous layers are phase-aligned.' },
    'add water': { 'params': ['WATER_TYPE', 'WATER_VOL'], 'action': 'Add water layer (stream, drip, ocean). Adjust volume to blend.' },
    'more spacious': { 'params': ['STEREO_WIDTH', 'REVERB_SIZE', 'DECORRELATION'], 'action': 'Increase stereo width. Use larger reverb. Increase L/R decorrelation.' },
    'add drone': { 'params': ['DRONE_FREQ', 'DRONE_VOL', 'DRONE_FILTER'], 'action': 'Add low drone layer (40-80Hz sine or filtered noise). Keep very subtle.' },
}
```

## How to Apply the Refinement Map

1. Parse the user's feedback — match keywords to REFINEMENT_MAP entries
2. Identify the affected parameters — use the `params` list to find the constants in the script
3. Apply the action — follow the `action` description for specific adjustments
4. Preserve the seed — keep `rng` seeds unchanged so only the requested aspect changes
5. Re-run and compare — generate the new version and present both for comparison

### Compound Requests

Users often combine multiple requests. Process each independently:

| User Says | Map Entries | Combined Action |
|-----------|-------------|-----------------|
| "more rain and darker" | `more rain` + `darker` | Boost rain volume/density AND lower all filter cutoffs |
| "calmer but more spacious" | `calmer` + `more spacious` | Reduce events AND increase reverb size/stereo width |
| "add thunder, make it longer" | `add thunder` + `longer` | Add thunder events AND extend duration proportionally |

### Unmapped Requests

If the user's request doesn't match a map entry, follow this process:

1. Identify the intent — what sonic quality are they after?
2. Map to dimensions — which evolution dimensions are affected? (density, activity, depth, brightness, movement)
3. Determine parameters — which script constants control those dimensions?
4. Apply conservatively — make small changes (3dB volume, 1-2 dimension points) rather than dramatic ones
5. Document the mapping — tell the user what you changed and why

Common unmapped requests and how to handle them:

| Request | Approach |
|---------|----------|
| "more realistic" | Increase event variation (pitch, timing, volume randomization). Add detail layer elements. |
| "less synthetic" | Reduce pure sine tones. Use more filtered noise. Add subtle modulation to everything. |
| "scarier" | Lower brightness. Add sub-bass rumble. Use longer reverb. Make events sudden and sparse. |
| "more relaxing" | Reduce activity to 1-2. Remove sharp transients. Increase depth. Slow all modulation. |
| "like being inside" | Reduce depth dimension. Shorten reverb. Add room-tone bed. Muffle outdoor sounds (LP filter). |

## Version Management

### File Naming Convention

```
{environment}_{version}.wav

Examples:
  rainy_forest_v1.wav
  rainy_forest_v2_more_rain.wav
  rainy_forest_v3_darker_thunder.wav
  ocean_beach_v1.wav
  ocean_beach_v2_loopable.wav
```

### Version Tracking

Maintain a version history in the script as comments:

```python
# === VERSION HISTORY ===
# v1: Initial rainy forest — bed (rain), texture (wind), events (drips, owl)
# v2: User requested "more rain" — increased RAIN_VOL by 4dB, raised RAIN_CUTOFF to 3000Hz
# v3: User requested "darker, add thunder" — lowered all cutoffs by 30%, added thunder events
# === END VERSION HISTORY ===
```

### Script Organization for Iteration

Structure the script so parameters are easy to find and modify:

```python
# ============================================================
# TUNABLE PARAMETERS — Modify these for refinement
# ============================================================

# Duration & Output
DURATION = 300          # seconds (5 minutes)
SR = 44100              # sample rate
OUTPUT_FILE = "rainy_forest_v1.wav"

# Evolution Dimensions (0-10)
DENSITY = 5
ACTIVITY = 4
DEPTH = 7
BRIGHTNESS = 5
MOVEMENT = 3

# Bed Layer — Rain
RAIN_VOL = -6           # dB relative to full scale
RAIN_CUTOFF_LOW = 100   # Hz — bandpass low edge
RAIN_CUTOFF_HIGH = 2000 # Hz — bandpass high edge
RAIN_MOD_RATE = 0.03    # Hz — LFO rate for volume modulation

# Texture — Wind
WIND_VOL = -12          # dB
WIND_CENTER = 500       # Hz — bandpass center
WIND_BW = 600           # Hz — bandwidth
GUST_FREQUENCY = 0.2    # gusts per minute

# Events — Water Drips
DRIP_FREQUENCY = 1.5    # drips per minute
DRIP_PITCH_MIN = 1000   # Hz
DRIP_PITCH_MAX = 3000   # Hz
DRIP_VOL = -15          # dB

# Events — Bird/Owl
BIRD_FREQUENCY = 0.5    # calls per minute
BIRD_VOL = -18          # dB

# Detail — Thunder
THUNDER_FREQUENCY = 0.3 # per minute (1 every ~3 minutes)
THUNDER_DISTANCE = 0.7  # 0=close, 1=far
THUNDER_VOL = -12       # dB

# Spatial
REVERB_DECAY = 3.0      # seconds
REVERB_WET = 0.4        # 0-1
STEREO_WIDTH = 0.7      # 0-1

# ============================================================
# END TUNABLE PARAMETERS
# ============================================================
```

## A/B Comparison Workflow

When presenting refined versions to the user, follow this protocol:

### 1. Generate Both Versions
Keep the previous version's .wav file. Generate the new version with a new filename.

### 2. Present the Comparison

```
Version 1: rainy_forest_v1.wav
  - Rain volume: -6dB, cutoff 100-2000Hz
  - Activity: 4, Brightness: 5

Version 2: rainy_forest_v2_more_rain.wav
  - Rain volume: -2dB, cutoff 100-3000Hz  [CHANGED: +4dB, wider bandwidth]
  - Activity: 4, Brightness: 5            [unchanged]

Changes applied:
  - RAIN_VOL: -6dB → -2dB (+4dB increase)
  - RAIN_CUTOFF_HIGH: 2000Hz → 3000Hz (heavier rain has more high-frequency content)
  - RAIN_DENSITY: no change (volume increase alone was sufficient)
```

### 3. Offer Next Steps
After presenting, always suggest:
- Further refinement options based on what was changed
- Complementary adjustments (e.g., "since rain is louder, you might want to boost the thunder too")
- Alternative directions the user might explore

## Soundscape-Specific Iteration Tips

### Avoiding Common Issues During Refinement

| Issue | Cause | Fix |
|-------|-------|-----|
| Sounds muddy after adding layers | Too many layers in same frequency range | Use EQ to carve space — each layer should occupy its own band |
| Events sound mechanical | Same timing/pitch every time | Increase randomization ranges for pitch, volume, and timing |
| Lost sense of space after changes | Reverb parameters changed inconsistently | Keep DEPTH dimension consistent across layers |
| Harsh after brightening | Filter cutoffs too high | Use gentle shelving boost instead of raising cutoff |
| Clicks appeared after edit | New event overlaps existing audio | Ensure all events have fade-in/fade-out (5ms minimum) |
| Loop point audible | Crossfade too short or layers misaligned | Increase crossfade duration. Check that bed layer is continuous through boundary. |

### Iteration Limits

- Volume changes: +/- 3-6dB per iteration (larger changes are jarring)
- Filter changes: +/- 30% cutoff per iteration
- Dimension changes: +/- 2 points per iteration
- Event density: +/- 50% per iteration
- Duration: any change is fine, but stretch/compress evolution map proportionally
- New layers: add one at a time, at -18dB initially, then adjust up

### When to Start Over vs. Iterate

Iterate when:
- User wants more/less of existing elements
- Adjusting balance, mood, or density
- Adding 1-2 new elements
- Changing duration or loop mode

Start over when:
- User wants a completely different environment
- More than 3 layers need replacing
- Fundamental bed layer needs changing (rain → ocean)
- User says "something completely different"
