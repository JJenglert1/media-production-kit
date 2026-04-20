# Soundscape Design Reference

Comprehensive guide for designing and synthesizing ambient soundscapes. Covers layer architecture, environment recipes, spatial techniques, looping, and duration management.

## Layer Architecture

Every soundscape is built from 4 layer types, each serving a distinct role. The bed layer is mandatory; the others add richness and realism.

### 1. Bed Layer (Always Present)

The continuous foundation that defines the acoustic space. Without it, the soundscape feels hollow and disconnected. The bed should be audible at all times, even if very quiet.

By environment type:
- Nature: wind noise (bandpass brown/pink noise, 100-2000Hz), water flow (brown noise with slow modulation), leaf rustle (HP filtered noise with amplitude flutter)
- Urban: traffic hum (low-passed brown noise, 60-400Hz), distant city drone (layered filtered noise at multiple bands), HVAC hum (narrow bandpass at 120Hz + harmonics)
- Abstract: filtered noise pad (slowly sweeping bandpass), evolving drone (sine + slow FM), spectral freeze texture

Implementation pattern:
```python
def generate_bed_layer(duration, sr, seed=42):
    """Generate continuous bed layer using overlap-add for seamless noise."""
    rng = np.random.default_rng(seed)
    n_samples = int(duration * sr)
    chunk_size = sr * 2  # 2-second chunks
    hop_size = chunk_size // 2  # 50% overlap
    window = np.hanning(chunk_size)

    output = np.zeros(n_samples)
    pos = 0
    while pos < n_samples:
        chunk_len = min(chunk_size, n_samples - pos)
        noise = rng.normal(0, 1, chunk_size)
        # Apply bandpass filter (100-2000Hz example)
        sos = butter(4, [100, 2000], btype='band', fs=sr, output='sos')
        filtered = sosfilt(sos, noise)
        windowed = filtered * window
        # Overlap-add
        end = min(pos + chunk_size, n_samples)
        output[pos:end] += windowed[:end - pos]
        pos += hop_size

    # Apply slow LFO modulation to volume
    lfo = 0.7 + 0.3 * np.sin(2 * np.pi * 0.03 * np.arange(n_samples) / sr)
    output *= lfo

    return output / (np.max(np.abs(output)) + 1e-10)
```

### 2. Texture Layers (Continuous, Modulated)

Add character and movement to the bed. These are always-on but vary in intensity, creating the sense that the environment is alive.

By environment type:
- Nature: specific wind gusts (bandpass noise with amplitude swells, 200-1500Hz, 3-8s duration), water splashing patterns (short noise bursts with resonant filter), insect chorus (narrow bandpass noise at 3-8kHz with amplitude modulation at 5-15Hz)
- Urban: distant voices (noise shaped by formant filters at 300, 800, 2500Hz), machinery rhythms (filtered noise with periodic amplitude envelope), traffic patterns (low noise with slow volume surges)
- Abstract: granular textures (tiny noise grains with random pitch/timing), spectral drones (slowly morphing harmonic content), modulated feedback

Implementation pattern:
```python
def generate_texture_layer(duration, sr, center_freq=800, bandwidth=400,
                           mod_rate=0.05, seed=43):
    """Continuous texture with slow modulation."""
    rng = np.random.default_rng(seed)
    n_samples = int(duration * sr)
    t = np.arange(n_samples) / sr

    # Generate filtered noise (overlap-add)
    noise = generate_overlap_add_noise(n_samples, sr, rng)

    # Bandpass filter
    low = max(20, center_freq - bandwidth / 2)
    high = min(sr / 2 - 1, center_freq + bandwidth / 2)
    sos = butter(4, [low, high], btype='band', fs=sr, output='sos')
    filtered = sosfilt(sos, noise)

    # Slow amplitude modulation (creates breathing/pulsing effect)
    mod = 0.5 + 0.5 * np.sin(2 * np.pi * mod_rate * t + rng.uniform(0, 2 * np.pi))

    # Slow filter cutoff modulation via second pass
    cutoff_lfo = center_freq + bandwidth * 0.3 * np.sin(2 * np.pi * 0.02 * t)
    # Apply as gentle volume curve (approximation of filter sweep)
    filtered *= mod

    return filtered / (np.max(np.abs(filtered)) + 1e-10)
```

### 3. Event Layers (Sporadic)

Discrete occurrences that punctuate the soundscape. These are what make a scene feel real and alive. Timing must be randomized — never on a grid.

By environment type:
- Nature: bird calls (FM synthesis, 1-4kHz, varied), branch snaps (short noise burst with sharp attack), thunder claps (sub boom + noise crack), water drips (Karplus-Strong, 1-3kHz), frog croaks (low FM, 100-400Hz)
- Urban: car horn (sawtooth/square + bandpass, 300-600Hz), door slam (noise impact + resonance), footsteps (filtered noise clicks, varied timing), distant siren (sweeping sine, 600-1200Hz)
- Abstract: tonal pings (sine with fast decay), glitch bursts (bit-crushed noise), metallic impacts (FM with high modulation index)

Timing — Poisson distribution:
```python
def generate_poisson_event_times(duration, avg_events_per_minute, rng):
    """Generate random event times using Poisson process.

    Returns array of event times in seconds.
    More natural than fixed intervals.
    """
    if avg_events_per_minute <= 0:
        return np.array([])

    avg_interval = 60.0 / avg_events_per_minute
    times = []
    current_time = rng.exponential(avg_interval)

    while current_time < duration:
        times.append(current_time)
        current_time += rng.exponential(avg_interval)

    return np.array(times)
```

Variation is essential:
- Pitch: vary by +/- 10-30% between occurrences
- Volume: vary by +/- 6dB
- Duration: vary by +/- 20%
- Stereo position: randomize pan for each event
- Never repeat the exact same sound twice in a row

### 4. Detail Layers (Rare Accents)

Occasional surprises that prevent listener habituation. These are the sounds that make someone think "wow, this sounds so real." Maximum 1-3 per minute.

Examples by environment:
- Forest: distant airplane hum (very low, very quiet sine sweep), far-off dog bark, woodpecker (rapid clicking pattern)
- Ocean: whale call (low FM sweep), distant boat motor, splash of a jumping fish
- City: distant music from a window (filtered noise with rhythm), church bell, emergency vehicle far away
- Cave: distant water echo, bat squeak (very high, very quiet), rock settling

Implementation: Same as events but with much lower density (0.3-1 per minute) and generally quieter (-12 to -18dB relative to bed).

## Environment Recipes

### Rainy Forest at Night
```
Bed:     Brown noise, bandpass 100-2000Hz, slow modulation (0.03Hz)     | -6dB
Texture: Rain gusts — pink noise bursts 200-800Hz, 3-8s, random timing  | -9dB
Texture: Leaf drip pattern — HP noise > 4kHz, very subtle               | -18dB
Events:  Water drips — Karplus-Strong 1-3kHz, 0.5-2/min                 | -12dB
Events:  Leaf rustle — HP noise burst 3-8kHz, 0.3-1/min                 | -15dB
Detail:  Distant owl — sine + vibrato 400-800Hz, 1 per 2 min            | -18dB
Detail:  Distant thunder — sub boom 30-60Hz + noise crackle, 1/3-5 min  | -12dB
Depth:   Reverb decay 2-4s, depth dimension 7
Stereo:  Different rain seeds L/R, events randomly panned
```

### Ocean Beach
```
Bed:     Wave cycle — brown noise with 10-15s amplitude envelope          | -3dB
         Shape: silence → approach (3s ramp) → break (noise burst) →
         recede (4s decay) → pause (2-4s random)
Texture: Foam/hiss — white noise HP > 4kHz, amplitude follows wave peak  | -12dB
Texture: Undertow — very low rumble 30-80Hz, anti-phase to wave peak     | -15dB
Events:  Seagull — FM synthesis 1.5-3kHz, sporadic (1-3/min)             | -12dB
Events:  Sand shift — very quiet broadband noise burst, 50ms             | -24dB
Detail:  Distant boat — low filtered sine sweep, 1 per 5 min             | -24dB
Spatial: Waves from center-left, birds from various positions
```

### Meditation Space
```
Bed:     Sine drone 60-80Hz (very subtle) + filtered pink noise (quiet)  | -12dB
Texture: Tibetan bowl — FM bell 400-800Hz, slow amplitude swell          | -15dB
         every 30-60s, long decay (8-12s)
Events:  Water drip — Karplus-Strong, 1 per 30s (very quiet)             | -24dB
Detail:  Minimal — perhaps a very distant chime once per 2 min           | -30dB
Depth:   Large reverb (4-6s decay), depth dimension 8
Movement: Nearly static, movement dimension 1-2
Activity: Very low (1-2), no sudden events
```

### Thunderstorm (Narrative Arc)
```
Phase 1 (0:00-1:30) — Approach:
  Bed:     Light rain — filtered brown noise, quiet                      | -12dB
  Texture: Gentle wind — bandpass noise 200-800Hz                        | -15dB
  Events:  Distant thunder — sub rumble, 1 per minute, very distant      | -18dB

Phase 2 (1:30-3:30) — Peak:
  Bed:     Heavy rain — brown noise louder, wider bandwidth               | -3dB
  Texture: Strong wind gusts — bandpass sweeps 200-2000Hz, 5-15s          | -6dB
  Events:  Lightning + thunder — crack (100ms) + boom (1-3s decay)        | -3dB
           Delay between flash and thunder: 100-800ms (closer)
  Events:  Hail clusters — rapid noise clicks 2-5kHz, 10-20 per cluster  | -12dB

Phase 3 (3:30-5:00) — Recede:
  Bed:     Rain easing — reduce volume and bandwidth gradually            | -9dB
  Texture: Wind calming — narrower bandwidth, lower volume                | -12dB
  Events:  Thunder moving away — longer delays, quieter, less frequent    | -15dB
  Detail:  Birds resuming — tentative calls, increasing frequency         | -18dB
```

### Deep Space
```
Bed:     Sub drone — sine at 40Hz + slow FM (mod index 0.1)              | -9dB
         Pink noise very quiet, LP < 200Hz                               | -18dB
Texture: Solar wind — narrow bandpass noise, very slow sweep 100-500Hz   | -15dB
Texture: Radio static — noise bursts, AM modulated at irregular rates    | -24dB
Events:  Cosmic ping — high sine (2-6kHz) with fast decay, random pitch  | -15dB
Events:  Debris — noise impact with metallic resonance, 1 per 2 min      | -18dB
Detail:  Transmission — filtered voice-like formant noise, 1 per 5 min   | -24dB
Depth:   Very large reverb (6-10s), depth dimension 9-10
Stereo:  Maximum width, slow auto-pan on all events
```

### Coffee Shop
```
Bed:     Room tone — brown noise LP < 500Hz (HVAC, general ambience)     | -9dB
Texture: Chatter — pink noise shaped by formant filters (300, 800, 2500) | -6dB
         Amplitude modulated with slow irregular envelope
Texture: Music — very filtered (LP 800Hz, HP 200Hz), barely audible      | -24dB
Events:  Cup clink — high sine ping (2-4kHz) + noise, 2-4/min           | -15dB
Events:  Espresso machine — noise burst (1-4kHz), 1/min, 3-5s duration  | -12dB
Events:  Door — low noise thud + brief wind burst, 0.5/min              | -18dB
Detail:  Laughter — shaped noise burst, 1 per 2 min                     | -18dB
Detail:  Phone notification — high sine blip, 1 per 3 min               | -24dB
Depth:   Small-medium reverb (0.5-1.5s), depth dimension 4
```

## Binaural & Spatial Techniques

### Stereo Decorrelation
Different noise seeds for left and right channels create a wide, immersive field without phase issues.

```python
def stereo_decorrelated_noise(n_samples, sr, seed=42):
    """Generate stereo noise with natural decorrelation."""
    rng = np.random.default_rng(seed)
    noise_l = rng.normal(0, 1, n_samples)
    noise_r = rng.normal(0, 1, n_samples)  # Different samples = decorrelated
    return np.column_stack([noise_l, noise_r])
```

### Event Placement in Stereo Field
```python
def place_event_stereo(event_mono, pan_position, distance=0.5, sr=44100):
    """Place a mono event in the stereo field.

    Args:
        event_mono: mono audio array
        pan_position: -1.0 (full left) to 1.0 (full right)
        distance: 0.0 (very close) to 1.0 (very far)
        sr: sample rate

    Returns:
        stereo array (n_samples, 2)
    """
    n = len(event_mono)

    # Equal-power panning
    angle = (pan_position + 1) * np.pi / 4  # 0 to pi/2
    gain_l = np.cos(angle)
    gain_r = np.sin(angle)

    # Distance simulation
    volume_atten = 1.0 / (1.0 + 3.0 * distance)  # Inverse distance

    # High-frequency rolloff for distant sources
    if distance > 0.3:
        cutoff = max(1000, 16000 * (1.0 - distance))
        sos = butter(2, cutoff, btype='low', fs=sr, output='sos')
        event_mono = sosfilt(sos, event_mono)

    # Pre-delay for distance (speed of sound approximation)
    pre_delay_samples = int(distance * 0.1 * sr)  # Up to 100ms
    if pre_delay_samples > 0:
        event_mono = np.concatenate([np.zeros(pre_delay_samples), event_mono])

    event_mono *= volume_atten

    stereo = np.column_stack([event_mono * gain_l, event_mono * gain_r])
    return stereo
```

### Slow Auto-Pan
```python
def apply_auto_pan(stereo_signal, rate_hz=0.03, depth=0.3, sr=44100):
    """Apply slow auto-panning to a stereo signal.

    Args:
        stereo_signal: (n_samples, 2) array
        rate_hz: LFO rate (0.02-0.1 Hz for soundscapes)
        depth: 0.0 (no movement) to 1.0 (full L-R sweep)
        sr: sample rate
    """
    n = len(stereo_signal)
    t = np.arange(n) / sr

    # LFO generates pan position
    pan_lfo = depth * np.sin(2 * np.pi * rate_hz * t)

    # Convert to L/R gains (equal power)
    angle = (pan_lfo + 1) * np.pi / 4
    gain_l = np.cos(angle)
    gain_r = np.sin(angle)

    # Mix: mono content gets panned, keep some original stereo
    mono = (stereo_signal[:, 0] + stereo_signal[:, 1]) * 0.5
    original_weight = 1.0 - depth * 0.5
    pan_weight = depth * 0.5

    out_l = stereo_signal[:, 0] * original_weight + mono * gain_l * pan_weight
    out_r = stereo_signal[:, 1] * original_weight + mono * gain_r * pan_weight

    return np.column_stack([out_l, out_r])
```

### Distance-Based Spatial Processing
```python
def apply_distance(mono_signal, distance, reverb_func, sr=44100):
    """Apply distance-based processing to a mono signal.

    distance: 0.0 = very close, 1.0 = very far

    Close sounds:  louder, more high-freq, less reverb, no pre-delay
    Far sounds:    quieter, muffled, more reverb, longer pre-delay
    """
    # Volume attenuation (inverse square approximation)
    volume = 1.0 / (1.0 + 4.0 * distance ** 2)

    # High-frequency rolloff (air absorption)
    cutoff = max(800, 18000 * (1.0 - distance * 0.8))
    sos = butter(2, cutoff, btype='low', fs=sr, output='sos')
    processed = sosfilt(sos, mono_signal) * volume

    # Reverb wet/dry ratio based on distance
    wet_amount = 0.1 + 0.7 * distance  # 10% wet close, 80% wet far
    dry = processed * (1.0 - wet_amount)
    wet = reverb_func(processed) * wet_amount

    return dry + wet
```

## Looping Techniques

### Equal-Power Crossfade Loop
```python
def make_loopable(audio, crossfade_seconds=3.0, sr=44100):
    """Make audio seamlessly loopable with equal-power crossfade.

    Mixes the last crossfade_seconds with the first crossfade_seconds.
    Returns audio shortened by crossfade_seconds.

    Args:
        audio: numpy array, mono (n,) or stereo (n, 2)
        crossfade_seconds: duration of crossfade region
        sr: sample rate

    Returns:
        Loopable audio array
    """
    xfade_samples = int(crossfade_seconds * sr)

    if len(audio) < xfade_samples * 3:
        raise ValueError("Audio too short for requested crossfade duration")

    is_stereo = audio.ndim == 2

    # Split into body and crossfade regions
    body = audio[xfade_samples:-xfade_samples]
    head = audio[:xfade_samples]   # Beginning of the file
    tail = audio[-xfade_samples:]  # End of the file

    # Equal-power crossfade curves
    t = np.linspace(0, np.pi / 2, xfade_samples)
    fade_in = np.sin(t)    # 0 → 1
    fade_out = np.cos(t)   # 1 → 0

    if is_stereo:
        fade_in = fade_in[:, np.newaxis]
        fade_out = fade_out[:, np.newaxis]

    # Crossfade: tail fades out while head fades in
    crossfaded = tail * fade_out + head * fade_in

    # Reassemble: crossfade region + body
    looped = np.concatenate([crossfaded, body])

    return looped
```

### Verifying Loop Seamlessness
```python
def verify_loop(audio, sr=44100, check_samples=2048):
    """Check if a loop point is seamless.

    Returns True if the transition is smooth.
    """
    # Check amplitude continuity at boundary
    end_rms = np.sqrt(np.mean(audio[-check_samples:] ** 2))
    start_rms = np.sqrt(np.mean(audio[:check_samples] ** 2))
    rms_ratio = max(end_rms, start_rms) / (min(end_rms, start_rms) + 1e-10)

    # Check for DC offset jump
    end_mean = np.mean(audio[-check_samples:])
    start_mean = np.mean(audio[:check_samples])
    dc_jump = abs(end_mean - start_mean)

    # Check sample-level continuity
    sample_jump = abs(float(audio[-1]) - float(audio[0]))

    is_seamless = (rms_ratio < 1.5 and dc_jump < 0.01 and sample_jump < 0.05)

    if not is_seamless:
        print(f"Loop check FAILED: RMS ratio={rms_ratio:.2f}, "
              f"DC jump={dc_jump:.4f}, sample jump={sample_jump:.4f}")
    else:
        print("Loop check PASSED: seamless transition")

    return is_seamless
```

## Duration Guidelines

| Duration | Use Case | Chunk Processing | Evolution Sections |
|----------|----------|------------------|--------------------|
| 1-3 min  | Demo, single scene | Not needed | 3-4 sections |
| 5-10 min | Meditation, study | Recommended | 5-8 sections |
| 15-30 min | Sleep aid, extended ambient | Required | 10-20 sections |

### Memory-Efficient Processing for Long Durations
```python
def process_long_soundscape(duration, sr, chunk_seconds=30, overlap_seconds=2):
    """Process a long soundscape in chunks to manage memory.

    For durations > 5 minutes, generating all layers at once can
    exhaust memory. Process in overlapping chunks instead.
    """
    chunk_samples = int(chunk_seconds * sr)
    overlap_samples = int(overlap_seconds * sr)
    hop_samples = chunk_samples - overlap_samples
    n_samples = int(duration * sr)
    n_channels = 2  # stereo

    # Pre-allocate output (or write incrementally to file)
    output = np.zeros((n_samples, n_channels))

    # Crossfade window for overlap region
    fade = np.linspace(0, 1, overlap_samples)
    fade_2d = fade[:, np.newaxis]

    pos = 0
    chunk_idx = 0
    while pos < n_samples:
        chunk_end = min(pos + chunk_samples, n_samples)
        actual_len = chunk_end - pos
        chunk_time_start = pos / sr
        chunk_time_end = chunk_end / sr

        # Generate this chunk's audio (all layers)
        chunk_audio = generate_chunk(chunk_time_start, chunk_time_end, sr)

        if pos == 0:
            # First chunk: no crossfade needed at start
            output[pos:chunk_end] = chunk_audio[:actual_len]
        else:
            # Crossfade overlap region with previous chunk's tail
            xfade_end = min(pos + overlap_samples, n_samples)
            xfade_len = xfade_end - pos
            output[pos:xfade_end] *= (1 - fade_2d[:xfade_len])
            output[pos:xfade_end] += chunk_audio[:xfade_len] * fade_2d[:xfade_len]
            # Non-overlapping part
            if xfade_end < chunk_end:
                output[xfade_end:chunk_end] = chunk_audio[xfade_len:actual_len]

        pos += hop_samples
        chunk_idx += 1

    return output
```

### Overlap-Add Noise Generator
```python
def overlap_add_noise(n_samples, sr, filter_func=None, chunk_seconds=2.0, seed=42):
    """Generate seamless filtered noise using overlap-add.

    This is the fundamental building block for all noise-based layers.
    Prevents clicks/pops at chunk boundaries that occur with naive concatenation.

    Args:
        n_samples: total samples to generate
        sr: sample rate
        filter_func: optional function(signal, sr) -> filtered_signal
        chunk_seconds: size of each processing chunk
        seed: random seed

    Returns:
        Seamless noise array
    """
    rng = np.random.default_rng(seed)
    chunk_size = int(chunk_seconds * sr)
    hop_size = chunk_size // 2  # 50% overlap
    window = np.hanning(chunk_size)

    output = np.zeros(n_samples)
    pos = 0

    while pos < n_samples:
        # Generate noise chunk
        noise = rng.normal(0, 1, chunk_size)

        # Apply filter if provided
        if filter_func is not None:
            noise = filter_func(noise, sr)

        # Window the chunk
        windowed = noise * window

        # Add to output
        end = min(pos + chunk_size, n_samples)
        length = end - pos
        output[pos:end] += windowed[:length]

        pos += hop_size

    return output


def wave_amplitude_envelope(duration, sr, wave_period=12.0, rng=None):
    """Generate amplitude envelope mimicking ocean waves.

    Models the approach-break-recede-pause cycle of real waves.

    Args:
        duration: total duration in seconds
        sr: sample rate
        wave_period: average seconds per wave cycle
        rng: numpy random generator

    Returns:
        Amplitude envelope array
    """
    if rng is None:
        rng = np.random.default_rng(42)

    n_samples = int(duration * sr)
    envelope = np.zeros(n_samples)
    pos = 0

    while pos < n_samples:
        # Randomize this wave's timing
        period = wave_period * rng.uniform(0.7, 1.3)
        approach_time = period * 0.25   # Wave approaches
        break_time = period * 0.1       # Wave breaks (peak)
        recede_time = period * 0.35     # Wave recedes
        pause_time = period * 0.3       # Quiet between waves

        # Approach: slow rise
        approach_samples = int(approach_time * sr)
        t = np.linspace(0, np.pi / 2, approach_samples)
        approach = np.sin(t) ** 2  # Smooth rise

        # Break: brief peak
        break_samples = int(break_time * sr)
        peak = np.ones(break_samples) * rng.uniform(0.8, 1.0)

        # Recede: slower fall
        recede_samples = int(recede_time * sr)
        t = np.linspace(0, np.pi / 2, recede_samples)
        recede = np.cos(t) ** 2  # Smooth fall

        # Pause: near silence
        pause_samples = int(pause_time * sr)
        pause = np.ones(pause_samples) * rng.uniform(0.02, 0.08)

        # Concatenate this wave cycle
        wave_cycle = np.concatenate([approach, peak, recede, pause])

        end = min(pos + len(wave_cycle), n_samples)
        length = end - pos
        envelope[pos:end] = wave_cycle[:length]
        pos += len(wave_cycle)

    return envelope[:n_samples]
```
