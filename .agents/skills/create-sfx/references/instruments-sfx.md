# SFX Synthesis Primitives

Core building blocks for sound effect synthesis. Each function is self-contained, uses numpy/scipy, and follows the mandatory DSP rules (SOS filters, PolyBLEP, click-free envelopes).

---

## 1. Noise Generators

### White Noise
Flat spectrum — equal energy at all frequencies.

```python
def white_noise(n_samples, amplitude=1.0, seed=None):
    """Generate white noise with optional seeded randomness."""
    rng = np.random.default_rng(seed)
    return rng.standard_normal(n_samples) * amplitude
```

### Pink Noise (1/f)
-3dB/octave rolloff — perceptually balanced, sounds "natural."

```python
def pink_noise(n_samples, seed=None):
    """Generate pink noise using the Voss-McCartney algorithm."""
    rng = np.random.default_rng(seed)
    # Approximate pink noise with layered random values
    n_rows = 16
    n_cols = n_samples
    array = rng.standard_normal((n_rows, n_cols))
    # Each row is updated at decreasing rates
    for i in range(1, n_rows):
        step = 2 ** i
        hold = np.repeat(array[i, ::step], step)[:n_cols]
        array[i, :] = hold
    result = np.sum(array, axis=0)
    return result / (np.max(np.abs(result)) + 1e-10)
```

### Brown Noise (1/f^2)
-6dB/octave rolloff — deep, rumbly. Good for sub-bass textures.

```python
def brown_noise(n_samples, seed=None):
    """Generate brown noise via cumulative sum of white noise."""
    rng = np.random.default_rng(seed)
    white = rng.standard_normal(n_samples)
    brown = np.cumsum(white)
    # Highpass to remove DC drift
    from scipy.signal import butter, sosfilt
    sos = butter(1, 20, btype='high', fs=48000, output='sos')
    brown = sosfilt(sos, brown)
    return brown / (np.max(np.abs(brown)) + 1e-10)
```

### Bandpass Noise Generator
Noise filtered to a specific frequency band — the workhorse of SFX synthesis.

```python
def bandpass_noise(n_samples, sr, center_freq, bandwidth_hz, order=4, seed=None):
    """Generate bandpass-filtered noise at a specific frequency band.

    Args:
        n_samples: Number of output samples
        sr: Sample rate
        center_freq: Center frequency in Hz
        bandwidth_hz: Bandwidth in Hz (total, not half)
        order: Filter order (higher = steeper rolloff)
        seed: Random seed for reproducibility
    """
    from scipy.signal import butter, sosfilt
    rng = np.random.default_rng(seed)
    noise = rng.standard_normal(n_samples)

    low = max(20, center_freq - bandwidth_hz / 2)
    high = min(sr / 2 - 1, center_freq + bandwidth_hz / 2)
    sos = butter(order, [low, high], btype='band', fs=sr, output='sos')
    return sosfilt(sos, noise)
```

### Velvet Noise
Sparse impulse noise — useful for subtle textures and sparse crackle effects.

```python
def velvet_noise(n_samples, density=0.01, seed=None):
    """Generate velvet noise — sparse random impulses.

    Args:
        n_samples: Number of output samples
        density: Probability of impulse per sample (0.001-0.1)
        seed: Random seed
    """
    rng = np.random.default_rng(seed)
    noise = np.zeros(n_samples)
    impulse_mask = rng.random(n_samples) < density
    noise[impulse_mask] = rng.choice([-1.0, 1.0], size=np.sum(impulse_mask))
    return noise
```

---

## 2. Transient Shapers

### Impact Transient Generator
Configurable attack/decay transient — the foundation of any impact or hit sound.

```python
def impact_transient(sr, attack_ms=1.0, decay_ms=50, freq=100, noise_mix=0.3):
    """Generate an impact transient with tonal and noise components.

    Args:
        sr: Sample rate
        attack_ms: Attack time (0.5-5ms typical)
        decay_ms: Decay time (20-500ms typical)
        freq: Fundamental frequency of tonal component
        noise_mix: Blend of noise vs tone (0=pure tone, 1=pure noise)
    """
    total_ms = attack_ms + decay_ms + 10  # Extra for safety fade
    n_samples = int(sr * total_ms / 1000)
    t = np.arange(n_samples) / sr

    # Tonal component
    tone = np.sin(2 * np.pi * freq * t)

    # Noise component
    noise = np.random.randn(n_samples)
    from scipy.signal import butter, sosfilt
    sos = butter(3, min(freq * 8, sr/2 - 1), btype='low', fs=sr, output='sos')
    noise = sosfilt(sos, noise)

    # Mix
    signal = tone * (1 - noise_mix) + noise * noise_mix

    # Envelope
    attack_samples = max(1, int(sr * attack_ms / 1000))
    env = np.zeros(n_samples)
    env[:attack_samples] = np.linspace(0, 1, attack_samples)
    env[attack_samples:] = np.exp(-np.linspace(0, 8, n_samples - attack_samples))

    result = signal * env

    # Safety cosine fade
    fade = int(sr * 0.002)
    result[:fade] *= 0.5 * (1 - np.cos(np.pi * np.arange(fade) / fade))
    result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result / (np.max(np.abs(result)) + 1e-10)
```

### Click Generator
Ultra-short transient for UI sounds, digital artifacts, and percussive accents.

```python
def click(sr, duration_ms=3, freq=3000, resonance=4.0):
    """Generate a short click/tick sound.

    Args:
        sr: Sample rate
        duration_ms: Total duration (2-20ms)
        freq: Center frequency of click
        resonance: Resonance of bandpass filter (Q factor)
    """
    n_samples = int(sr * duration_ms / 1000)

    # Impulse through resonant bandpass
    impulse = np.zeros(n_samples)
    impulse[0] = 1.0

    from scipy.signal import butter, sosfilt
    bw = freq / resonance
    low = max(20, freq - bw)
    high = min(sr/2 - 1, freq + bw)
    sos = butter(2, [low, high], btype='band', fs=sr, output='sos')
    result = sosfilt(sos, impulse)

    # Exponential decay
    t = np.arange(n_samples) / sr
    result *= np.exp(-t * (1000 / duration_ms) * 3)

    # Safety fade
    fade = min(int(sr * 0.001), n_samples // 4)
    if fade > 0:
        result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result / (np.max(np.abs(result)) + 1e-10)
```

### Transient Boost / Shape
Apply transient emphasis or de-emphasis to any signal.

```python
def shape_transient(signal, sr, attack_boost_db=6.0, sustain_db=0.0, attack_ms=5.0):
    """Shape the transient of a signal — boost or cut the attack.

    Args:
        signal: Input signal
        sr: Sample rate
        attack_boost_db: dB boost for transient (positive = punchier, negative = softer)
        sustain_db: dB change for sustain portion
        attack_ms: Duration of the transient portion
    """
    attack_samples = int(sr * attack_ms / 1000)
    n = len(signal)

    # Create transient/sustain gain envelope
    env = np.ones(n) * (10 ** (sustain_db / 20))
    attack_gain = 10 ** (attack_boost_db / 20)
    env[:attack_samples] = np.linspace(attack_gain, env[attack_samples] if attack_samples < n else 1.0, attack_samples)

    return signal * env
```

---

## 3. Sweep Generators

### Frequency Sweep
Generate a sine/saw sweep between two frequencies — linear, exponential, or logarithmic.

```python
def freq_sweep(sr, duration_s, freq_start, freq_end, sweep_type='exponential', waveform='sine'):
    """Generate a frequency sweep (chirp).

    Args:
        sr: Sample rate
        duration_s: Duration in seconds
        freq_start: Starting frequency in Hz
        freq_end: Ending frequency in Hz
        sweep_type: 'linear', 'exponential', or 'logarithmic'
        waveform: 'sine', 'saw', or 'square'
    """
    n_samples = int(sr * duration_s)
    t = np.arange(n_samples) / sr

    # Generate instantaneous frequency curve
    if sweep_type == 'linear':
        freq = np.linspace(freq_start, freq_end, n_samples)
    elif sweep_type == 'exponential':
        freq = freq_start * (freq_end / freq_start) ** (t / duration_s)
    elif sweep_type == 'logarithmic':
        freq = np.exp(np.linspace(np.log(freq_start), np.log(freq_end), n_samples))
    else:
        raise ValueError(f"Unknown sweep_type: {sweep_type}")

    # Integrate frequency to get phase
    phase = 2 * np.pi * np.cumsum(freq) / sr

    # Generate waveform
    if waveform == 'sine':
        signal = np.sin(phase)
    elif waveform == 'saw':
        signal = 2.0 * (phase / (2 * np.pi) % 1.0) - 1.0
    elif waveform == 'square':
        signal = np.sign(np.sin(phase))
    else:
        raise ValueError(f"Unknown waveform: {waveform}")

    return signal, freq
```

### Filter Sweep on Noise
Apply a time-varying bandpass filter to noise — creates whooshes, risers, and sweeps.

```python
def filter_sweep_noise(sr, duration_s, freq_start, freq_end, bandwidth_ratio=0.3,
                       sweep_type='exponential', noise_type='white', seed=None):
    """Apply a sweeping bandpass filter to noise.

    Args:
        sr: Sample rate
        duration_s: Duration in seconds
        freq_start: Starting center frequency
        freq_end: Ending center frequency
        bandwidth_ratio: Bandwidth as ratio of center frequency (0.1-0.5)
        sweep_type: 'linear' or 'exponential'
        noise_type: 'white', 'pink', or 'brown'
        seed: Random seed
    """
    from scipy.signal import butter, sosfilt
    n_samples = int(sr * duration_s)

    # Generate noise
    if noise_type == 'white':
        noise = white_noise(n_samples, seed=seed)
    elif noise_type == 'pink':
        noise = pink_noise(n_samples, seed=seed)
    elif noise_type == 'brown':
        noise = brown_noise(n_samples, seed=seed)
    else:
        noise = white_noise(n_samples, seed=seed)

    # Process in blocks with varying filter
    block_size = 256
    result = np.zeros(n_samples)

    if sweep_type == 'exponential':
        centers = freq_start * (freq_end / freq_start) ** (np.arange(n_samples) / n_samples)
    else:
        centers = np.linspace(freq_start, freq_end, n_samples)

    for i in range(0, n_samples - block_size, block_size):
        cf = centers[i + block_size // 2]
        bw = cf * bandwidth_ratio
        low = max(20, cf - bw)
        high = min(sr / 2 - 1, cf + bw)
        if low >= high:
            continue
        sos = butter(3, [low, high], btype='band', fs=sr, output='sos')
        result[i:i + block_size] = sosfilt(sos, noise[i:i + block_size])

    # Handle remaining samples
    remaining = n_samples % block_size
    if remaining > 0:
        start = n_samples - remaining
        cf = centers[-1]
        bw = cf * bandwidth_ratio
        low = max(20, cf - bw)
        high = min(sr / 2 - 1, cf + bw)
        if low < high:
            sos = butter(3, [low, high], btype='band', fs=sr, output='sos')
            result[start:] = sosfilt(sos, noise[start:])

    return result / (np.max(np.abs(result)) + 1e-10)
```

---

## 4. Impact Layers

These three components combine to create realistic impact sounds (hits, thuds, crashes).

### Sub Boom
Low-frequency sine with pitch drop — provides weight and physical impact feel.

```python
def sub_boom(sr, freq_start=80, freq_end=30, duration_ms=300, attack_ms=1.0):
    """Sub-bass boom with pitch drop.

    Args:
        sr: Sample rate
        freq_start: Initial frequency (50-150 Hz typical)
        freq_end: Final frequency (20-60 Hz typical)
        duration_ms: Total duration
        attack_ms: Attack time
    """
    n_samples = int(sr * duration_ms / 1000)
    t = np.arange(n_samples) / sr
    duration = duration_ms / 1000

    # Exponential pitch drop
    freq = freq_start * (freq_end / freq_start) ** (t / duration)
    phase = 2 * np.pi * np.cumsum(freq) / sr
    tone = np.sin(phase)

    # Envelope: fast attack, exponential decay
    attack_samples = max(1, int(sr * attack_ms / 1000))
    env = np.exp(-t * (5 / duration))
    env[:attack_samples] = np.linspace(0, 1, attack_samples)

    result = tone * env

    # Safety fade
    fade = int(sr * 0.005)
    result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result
```

### Body Layer
Resonant bandpass noise — provides the "material" character of the impact.

```python
def impact_body(sr, center_freq=400, bandwidth=300, duration_ms=150, resonance=2.0):
    """Resonant noise body layer for impacts.

    Args:
        sr: Sample rate
        center_freq: Center frequency (200-2000 Hz — lower = duller, higher = brighter)
        bandwidth: Bandwidth in Hz
        duration_ms: Duration
        resonance: Filter resonance (1-8)
    """
    from scipy.signal import butter, sosfilt
    n_samples = int(sr * duration_ms / 1000)
    t = np.arange(n_samples) / sr

    noise = np.random.randn(n_samples)

    # Resonant bandpass
    low = max(20, center_freq - bandwidth / 2)
    high = min(sr / 2 - 1, center_freq + bandwidth / 2)
    sos = butter(int(resonance), [low, high], btype='band', fs=sr, output='sos')
    filtered = sosfilt(sos, noise)

    # Envelope
    attack = max(1, int(sr * 0.002))
    env = np.exp(-t * (8 / (duration_ms / 1000)))
    env[:attack] = np.linspace(0, 1, attack)

    result = filtered * env
    fade = int(sr * 0.002)
    result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result / (np.max(np.abs(result)) + 1e-10)
```

### Crack / Snap
Short highpass noise — provides the initial attack "crack" of an impact.

```python
def impact_crack(sr, cutoff=2000, duration_ms=15, brightness=1.0):
    """Short highpass noise crack for impact transients.

    Args:
        sr: Sample rate
        cutoff: Highpass cutoff (1000-6000 Hz — higher = sharper/brighter)
        duration_ms: Duration (5-30ms)
        brightness: Multiplier for cutoff (0.5-2.0)
    """
    from scipy.signal import butter, sosfilt
    n_samples = int(sr * duration_ms / 1000)
    t = np.arange(n_samples) / sr

    noise = np.random.randn(n_samples)

    effective_cutoff = min(cutoff * brightness, sr / 2 - 1)
    sos = butter(3, effective_cutoff, btype='high', fs=sr, output='sos')
    filtered = sosfilt(sos, noise)

    # Very fast decay
    env = np.exp(-t * (2000 / duration_ms))
    env[:max(1, int(sr * 0.0005))] = np.linspace(0, 1, max(1, int(sr * 0.0005)))

    result = filtered * env
    fade = min(int(sr * 0.001), n_samples // 4)
    if fade > 0:
        result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result / (np.max(np.abs(result)) + 1e-10)
```

### Composite Impact
Combine all three layers into a complete impact sound.

```python
def composite_impact(sr, sub_freq=80, body_freq=400, crack_cutoff=2000,
                     duration_ms=300, sub_level=0.6, body_level=0.4, crack_level=0.3):
    """Full impact sound combining sub boom, body, and crack layers.

    Args:
        sr: Sample rate
        sub_freq: Sub boom starting frequency
        body_freq: Body resonance center frequency
        crack_cutoff: Crack highpass cutoff
        duration_ms: Total duration
        sub_level/body_level/crack_level: Relative mix levels
    """
    n_total = int(sr * duration_ms / 1000)

    # Generate layers
    sub = sub_boom(sr, freq_start=sub_freq, duration_ms=duration_ms) * sub_level
    body = impact_body(sr, center_freq=body_freq, duration_ms=int(duration_ms * 0.6)) * body_level
    crack_sig = impact_crack(sr, cutoff=crack_cutoff) * crack_level

    # Pad shorter layers to total length
    result = np.zeros(n_total)
    result[:len(sub)] += sub[:n_total]
    result[:len(body)] += body[:min(len(body), n_total)]
    result[:len(crack_sig)] += crack_sig[:min(len(crack_sig), n_total)]

    # Normalize
    peak = np.max(np.abs(result))
    if peak > 0:
        result /= peak

    # Final safety fade
    fade = int(sr * 0.002)
    result[:fade] *= 0.5 * (1 - np.cos(np.pi * np.arange(fade) / fade))
    result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result
```

---

## 5. Metallic Synthesis

### Inharmonic Partials (Ring Modulation)
Ring modulation creates sum/difference frequencies — inherently inharmonic, metallic timbre.

```python
def metallic_ring_mod(sr, freq_a=440, freq_b=587, duration_ms=500, decay_rate=5.0):
    """Metallic tone via ring modulation of two sine waves.

    The sum and difference frequencies create an inharmonic spectrum
    characteristic of bells, gongs, and metallic impacts.

    Args:
        sr: Sample rate
        freq_a: First oscillator frequency
        freq_b: Second oscillator frequency (use non-integer ratio for maximum inharmonicity)
        duration_ms: Duration
        decay_rate: Exponential decay rate
    """
    n_samples = int(sr * duration_ms / 1000)
    t = np.arange(n_samples) / sr

    osc_a = np.sin(2 * np.pi * freq_a * t)
    osc_b = np.sin(2 * np.pi * freq_b * t)

    # Ring modulation = multiplication
    ring = osc_a * osc_b

    # Decaying envelope
    attack = max(1, int(sr * 0.001))
    env = np.exp(-t * decay_rate)
    env[:attack] = np.linspace(0, 1, attack)

    result = ring * env
    fade = int(sr * 0.002)
    result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result / (np.max(np.abs(result)) + 1e-10)
```

### FM Metallic (Irrational Ratios)
FM synthesis with irrational carrier:modulator ratios produces dense metallic spectra.

```python
def metallic_fm(sr, carrier_freq=500, mod_ratio=1.4142, mod_index=5.0,
                duration_ms=600, decay_rate=4.0):
    """Metallic FM synthesis using irrational frequency ratios.

    Common metallic ratios:
        1.4142 (sqrt(2)) — bell-like
        1.7321 (sqrt(3)) — gong-like
        2.2360 (sqrt(5)) — chime-like
        1.6180 (golden ratio) — complex metallic

    Args:
        sr: Sample rate
        carrier_freq: Carrier frequency
        mod_ratio: Modulator/carrier ratio (irrational = metallic)
        mod_index: FM modulation depth (higher = more partials)
        duration_ms: Duration
        decay_rate: Decay rate
    """
    n_samples = int(sr * duration_ms / 1000)
    t = np.arange(n_samples) / sr

    mod_freq = carrier_freq * mod_ratio

    # Decaying modulation index — timbre evolves over time (bright→dark)
    mod_env = mod_index * np.exp(-t * decay_rate * 0.5)
    modulator = mod_env * np.sin(2 * np.pi * mod_freq * t)

    carrier = np.sin(2 * np.pi * carrier_freq * t + modulator)

    # Amplitude envelope
    attack = max(1, int(sr * 0.001))
    env = np.exp(-t * decay_rate)
    env[:attack] = np.linspace(0, 1, attack)

    result = carrier * env
    fade = int(sr * 0.002)
    result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result / (np.max(np.abs(result)) + 1e-10)
```

### Multi-Partial Metallic
Layer multiple inharmonic partials for complex metallic timbres.

```python
def metallic_partials(sr, base_freq=200, partial_ratios=None, partial_decays=None,
                      duration_ms=800):
    """Metallic sound from layered inharmonic partials.

    Args:
        sr: Sample rate
        base_freq: Fundamental frequency
        partial_ratios: List of frequency ratios (default: bell-like ratios)
        partial_decays: List of decay rates per partial (higher partials decay faster)
        duration_ms: Duration
    """
    if partial_ratios is None:
        # Bell-like partial ratios (inharmonic)
        partial_ratios = [1.0, 2.0, 2.414, 3.0, 3.732, 4.414, 5.236, 6.0]
    if partial_decays is None:
        # Higher partials decay faster
        partial_decays = [3.0 + i * 1.5 for i in range(len(partial_ratios))]

    n_samples = int(sr * duration_ms / 1000)
    t = np.arange(n_samples) / sr
    result = np.zeros(n_samples)

    for ratio, decay in zip(partial_ratios, partial_decays):
        freq = base_freq * ratio
        if freq >= sr / 2:
            continue
        # Each partial has its own decay
        amp = np.exp(-t * decay) / (ratio ** 0.5)  # Higher partials quieter
        result += np.sin(2 * np.pi * freq * t) * amp

    # Attack
    attack = max(1, int(sr * 0.001))
    result[:attack] *= np.linspace(0, 1, attack)

    # Safety fade
    fade = int(sr * 0.002)
    result[:fade] *= 0.5 * (1 - np.cos(np.pi * np.arange(fade) / fade))
    result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result / (np.max(np.abs(result)) + 1e-10)
```

---

## 6. Physical Modeling

### Karplus-Strong (Pluck / Impact)
Delay line with filtered feedback — models vibrating strings, plucked sounds, and short resonant impacts.

```python
def karplus_strong(sr, freq, duration_s=0.5, decay=0.996, brightness=0.5, seed=None):
    """Karplus-Strong string synthesis for plucks and impacts.

    Args:
        sr: Sample rate
        freq: Fundamental frequency (determines delay line length)
        duration_s: Output duration
        decay: Feedback coefficient (0.99-0.999 — higher = longer sustain)
        brightness: Lowpass blend (0=dark/muted, 1=bright/harsh)
        seed: Random seed for initial excitation
    """
    rng = np.random.default_rng(seed)
    n_samples = int(sr * duration_s)
    delay_length = int(sr / freq)

    if delay_length < 2:
        return np.zeros(n_samples)

    # Initial excitation: noise burst in delay buffer
    buffer = rng.uniform(-1, 1, delay_length)
    output = np.zeros(n_samples)

    # Process sample by sample
    idx = 0
    for i in range(n_samples):
        output[i] = buffer[idx]

        # Lowpass averaging filter (Karplus-Strong core)
        next_idx = (idx + 1) % delay_length
        filtered = brightness * buffer[idx] + (1 - brightness) * buffer[next_idx]
        buffer[idx] = filtered * decay

        idx = next_idx

    # Safety cosine fade
    fade = int(sr * 0.002)
    output[:fade] *= 0.5 * (1 - np.cos(np.pi * np.arange(fade) / fade))
    output[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return output / (np.max(np.abs(output)) + 1e-10)
```

### Simple Waveguide (Resonant Body)
Bidirectional delay line — models tubes, resonant cavities, and hollow objects.

```python
def waveguide_resonator(sr, input_signal, freq, decay=0.98, brightness=0.6):
    """Simple waveguide resonator — models resonant bodies/cavities.

    Feed any signal through to add resonant coloration.

    Args:
        sr: Sample rate
        input_signal: Input signal to resonate
        freq: Resonant frequency (determines delay line length)
        decay: Feedback amount (0.9-0.999)
        brightness: Lowpass filter blend
    """
    from scipy.signal import butter, sosfilt
    delay_length = max(2, int(sr / freq))
    n_samples = len(input_signal)

    delay_line = np.zeros(delay_length)
    output = np.zeros(n_samples)
    idx = 0

    for i in range(n_samples):
        # Read from delay line + input
        delayed = delay_line[idx]
        output[i] = input_signal[i] + delayed * 0.5

        # Write back with filtering
        next_idx = (idx + 1) % delay_length
        filtered = brightness * delayed + (1 - brightness) * delay_line[next_idx]
        delay_line[idx] = (input_signal[i] * 0.3 + filtered) * decay

        idx = next_idx

    return output / (np.max(np.abs(output)) + 1e-10)
```

---

## 7. Texture Generators

### Granular Noise
Noise built from tiny overlapping grains — creates organic, textured noise with controllable density.

```python
def granular_noise(sr, duration_s, grain_size_ms=20, density=10, freq_range=(200, 4000),
                   seed=None):
    """Granular noise texture — overlapping filtered noise grains.

    Args:
        sr: Sample rate
        duration_s: Duration in seconds
        grain_size_ms: Size of each grain in ms (5-100ms)
        density: Average grains per second
        freq_range: (low, high) frequency range for random bandpass per grain
        seed: Random seed
    """
    from scipy.signal import butter, sosfilt
    rng = np.random.default_rng(seed)
    n_samples = int(sr * duration_s)
    output = np.zeros(n_samples)
    grain_samples = int(sr * grain_size_ms / 1000)

    # Hann window for each grain
    window = 0.5 * (1 - np.cos(2 * np.pi * np.arange(grain_samples) / grain_samples))

    # Place grains randomly
    n_grains = int(duration_s * density)
    positions = rng.integers(0, max(1, n_samples - grain_samples), n_grains)

    for pos in positions:
        # Random frequency for this grain
        cf = rng.uniform(freq_range[0], freq_range[1])
        bw = cf * 0.3
        low = max(20, cf - bw)
        high = min(sr / 2 - 1, cf + bw)

        grain = rng.standard_normal(grain_samples)
        if low < high:
            sos = butter(2, [low, high], btype='band', fs=sr, output='sos')
            grain = sosfilt(sos, grain)
        grain *= window

        end = min(pos + grain_samples, n_samples)
        output[pos:end] += grain[:end - pos]

    return output / (np.max(np.abs(output)) + 1e-10)
```

### Crackle / Pop Generator
Random sparse noise bursts — fire crackle, vinyl noise, electrical interference.

```python
def crackle(sr, duration_s, rate=20, intensity=0.8, freq_range=(1000, 6000), seed=None):
    """Generate crackle/pop texture.

    Args:
        sr: Sample rate
        duration_s: Duration
        rate: Average crackles per second
        intensity: Amplitude intensity (0-1)
        freq_range: Frequency range for crackle filtering
        seed: Random seed
    """
    from scipy.signal import butter, sosfilt
    rng = np.random.default_rng(seed)
    n_samples = int(sr * duration_s)
    output = np.zeros(n_samples)

    n_crackles = int(duration_s * rate)
    positions = sorted(rng.integers(0, n_samples, n_crackles))

    for pos in positions:
        # Each crackle: 1-8ms noise burst
        dur = int(sr * rng.uniform(0.001, 0.008))
        if pos + dur >= n_samples:
            continue

        burst = rng.standard_normal(dur)

        # Random bandpass
        cf = rng.uniform(freq_range[0], freq_range[1])
        bw = cf * 0.4
        low = max(20, cf - bw)
        high = min(sr / 2 - 1, cf + bw)
        if low < high and dur > 10:
            sos = butter(2, [low, high], btype='band', fs=sr, output='sos')
            burst = sosfilt(sos, burst)

        # Tiny envelope
        env = np.exp(-np.linspace(0, 8, dur))
        burst *= env * intensity * rng.uniform(0.3, 1.0)

        output[pos:pos + dur] += burst

    return output / (np.max(np.abs(output)) + 1e-10)
```

### Debris Generator
Random short noise bursts simulating falling debris, rubble, or scattered impacts.

```python
def debris(sr, duration_s, density=15, size='medium', seed=None):
    """Generate debris/rubble texture — scattered impact sounds.

    Args:
        sr: Sample rate
        duration_s: Duration
        density: Impacts per second
        size: 'small' (glass/pebbles), 'medium' (rocks), 'large' (boulders)
        seed: Random seed
    """
    from scipy.signal import butter, sosfilt
    rng = np.random.default_rng(seed)
    n_samples = int(sr * duration_s)
    output = np.zeros(n_samples)

    size_configs = {
        'small':  {'freq': (2000, 8000), 'dur': (2, 15),  'decay': 15},
        'medium': {'freq': (500, 3000),  'dur': (10, 50), 'decay': 8},
        'large':  {'freq': (80, 800),    'dur': (30, 150), 'decay': 4},
    }
    cfg = size_configs[size]

    n_impacts = int(duration_s * density)
    # Exponentially decreasing density (more impacts at start)
    positions = np.sort(rng.exponential(n_samples / 3, n_impacts).astype(int))
    positions = positions[positions < n_samples]

    for pos in positions:
        dur_ms = rng.uniform(cfg['dur'][0], cfg['dur'][1])
        dur_samples = int(sr * dur_ms / 1000)
        if pos + dur_samples >= n_samples:
            continue

        # Impact: noise burst with resonance
        burst = rng.standard_normal(dur_samples)
        cf = rng.uniform(cfg['freq'][0], cfg['freq'][1])
        bw = cf * 0.3
        low = max(20, cf - bw)
        high = min(sr / 2 - 1, cf + bw)
        if low < high and dur_samples > 10:
            sos = butter(2, [low, high], btype='band', fs=sr, output='sos')
            burst = sosfilt(sos, burst)

        t_burst = np.arange(dur_samples) / sr
        env = np.exp(-t_burst * cfg['decay'] * (1000 / dur_ms))
        env[:max(1, int(sr * 0.001))] = np.linspace(0, 1, max(1, int(sr * 0.001)))
        burst *= env * rng.uniform(0.2, 1.0)

        output[pos:pos + dur_samples] += burst

    return output / (np.max(np.abs(output)) + 1e-10)
```

---

## Usage Example: Combining Primitives

```python
import numpy as np
from scipy.signal import butter, sosfilt

SR = 48000

# Build a complex impact sound from primitives
sub = sub_boom(SR, freq_start=80, freq_end=30, duration_ms=400) * 0.7
body = impact_body(SR, center_freq=500, duration_ms=200) * 0.5
crack_sig = impact_crack(SR, cutoff=3000, duration_ms=10) * 0.4
metal = metallic_fm(SR, carrier_freq=800, mod_ratio=1.4142, duration_ms=600) * 0.2
rubble = debris(SR, duration_s=1.5, density=20, size='small') * 0.15

# Mix to common length
length = max(len(sub), len(body), len(crack_sig), len(metal), len(rubble))
mixed = np.zeros(length)
for layer in [sub, body, crack_sig, metal, rubble]:
    mixed[:len(layer)] += layer

# Normalize to -1 dBFS
peak = np.max(np.abs(mixed))
target = 10 ** (-1 / 20)  # -1 dBFS
mixed = mixed / peak * target

# Final safety fade
fade = int(SR * 0.002)
mixed[:fade] *= 0.5 * (1 - np.cos(np.pi * np.arange(fade) / fade))
mixed[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
```
