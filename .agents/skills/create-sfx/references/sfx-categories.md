# SFX Categories & Recipes

Organized synthesis recipes for common sound effect categories. Each entry includes the core approach, frequency ranges, envelope shape, layering strategy, and a Python snippet.

---

## UI Sounds

### Click / Tap
- Approach: Short bandpass-filtered noise burst
- Frequencies: 2-5 kHz center
- Envelope: Attack 0.5ms, decay 5-20ms (total 5-20ms)
- Layers: Single noise burst, optionally a tiny sine transient at 3-4kHz
- Variations: Shift center frequency for different "materials" — higher = glass/digital, lower = plastic/soft

```python
def synth_click(sr=48000, duration_ms=15, center_freq=3500, q=8.0):
    """Short UI click sound."""
    n_samples = int(sr * duration_ms / 1000)
    t = np.arange(n_samples) / sr

    # Bandpass noise burst
    noise = np.random.randn(n_samples)
    sos = butter(4, [center_freq * 0.7, center_freq * 1.3], btype='band', fs=sr, output='sos')
    filtered = sosfilt(sos, noise)

    # Sharp envelope: 0.5ms attack, rest is decay
    attack_samples = max(1, int(sr * 0.0005))
    env = np.ones(n_samples)
    env[:attack_samples] = np.linspace(0, 1, attack_samples)
    env[attack_samples:] = np.exp(-np.linspace(0, 8, n_samples - attack_samples))

    result = filtered * env
    # Cosine fade edges
    fade = int(sr * 0.002)
    result[:fade] *= 0.5 * (1 - np.cos(np.pi * np.arange(fade) / fade))
    result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result / (np.max(np.abs(result)) + 1e-10)
```

### Hover
- Approach: Gentle sine sweep upward
- Frequencies: 1-2 kHz, sweep up 200-500 Hz
- Envelope: Attack 10ms, sustain 40ms, release 50ms (total 50-100ms)
- Layers: Pure sine + subtle noise layer at -20dB

```python
def synth_hover(sr=48000, duration_ms=80, freq_start=1200, freq_end=1600):
    """Gentle hover/focus sound."""
    n_samples = int(sr * duration_ms / 1000)
    t = np.arange(n_samples) / sr
    duration = duration_ms / 1000

    # Frequency sweep
    freq = np.linspace(freq_start, freq_end, n_samples)
    phase = 2 * np.pi * np.cumsum(freq) / sr
    tone = np.sin(phase) * 0.8

    # Soft noise layer
    noise = np.random.randn(n_samples) * 0.05
    sos = butter(2, 3000, btype='low', fs=sr, output='sos')
    noise = sosfilt(sos, noise)

    # Smooth envelope
    attack = int(sr * 0.01)
    release = int(sr * 0.05)
    env = np.ones(n_samples)
    env[:attack] = np.linspace(0, 1, attack)
    env[-release:] = np.linspace(1, 0, release)

    result = (tone + noise) * env
    return result / (np.max(np.abs(result)) + 1e-10)
```

### Success / Confirm
- Approach: Ascending two-tone with pleasant interval (P5 or M3)
- Frequencies: First tone 800-1000 Hz, second tone at P5 (1.5x) or M3 (1.26x) above
- Envelope: Each tone 80-120ms with 10ms attack, 30ms release
- Layers: Sine fundamental + quiet harmonic at 2x for warmth

```python
def synth_success(sr=48000, base_freq=880, interval_ratio=1.5, tone_dur_ms=100, gap_ms=30):
    """Ascending two-tone success chime."""
    tone_samples = int(sr * tone_dur_ms / 1000)
    gap_samples = int(sr * gap_ms / 1000)
    total = tone_samples * 2 + gap_samples
    t1 = np.arange(tone_samples) / sr
    t2 = np.arange(tone_samples) / sr

    # Two tones
    freq2 = base_freq * interval_ratio
    s1 = np.sin(2 * np.pi * base_freq * t1) * 0.8 + np.sin(2 * np.pi * base_freq * 2 * t1) * 0.15
    s2 = np.sin(2 * np.pi * freq2 * t2) * 0.8 + np.sin(2 * np.pi * freq2 * 2 * t2) * 0.15

    # Envelopes with attack/release
    attack = int(sr * 0.01)
    release = int(sr * 0.03)
    env = np.ones(tone_samples)
    env[:attack] = np.linspace(0, 1, attack)
    env[-release:] = np.exp(-np.linspace(0, 5, release))

    s1 *= env
    s2 *= env

    result = np.concatenate([s1, np.zeros(gap_samples), s2])
    # Cosine fade edges
    fade = int(sr * 0.002)
    result[:fade] *= 0.5 * (1 - np.cos(np.pi * np.arange(fade) / fade))
    result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result / (np.max(np.abs(result)) + 1e-10)
```

### Error / Deny
- Approach: Descending minor 2nd or harsh buzzer
- Frequencies: 400-600 Hz fundamental with dissonant harmonics
- Envelope: Attack 2ms, sustain 200ms, release 100ms
- Layers: Square-ish wave (odd harmonics) + noise grit

```python
def synth_error(sr=48000, freq=440, duration_ms=300):
    """Harsh error/deny buzzer."""
    n_samples = int(sr * duration_ms / 1000)
    t = np.arange(n_samples) / sr

    # Harsh tone: fundamental + close dissonant tone (minor 2nd = 16/15)
    tone1 = np.sin(2 * np.pi * freq * t)
    tone2 = np.sin(2 * np.pi * freq * (16/15) * t) * 0.7  # Minor 2nd — dissonant
    tone3 = np.sin(2 * np.pi * freq * 3 * t) * 0.2  # Odd harmonic for harshness

    # Noise grit
    noise = np.random.randn(n_samples) * 0.08

    # Envelope
    attack = int(sr * 0.002)
    release = int(sr * 0.1)
    env = np.ones(n_samples)
    env[:attack] = np.linspace(0, 1, attack)
    env[-release:] = np.linspace(1, 0, release)

    result = (tone1 + tone2 + tone3 + noise) * env
    return result / (np.max(np.abs(result)) + 1e-10)
```

### Notification / Bell
- Approach: FM synthesis with bell-like inharmonic spectrum
- Frequencies: Carrier 800-1200 Hz, modulator at 1.4x ratio (inharmonic = bell-like)
- Envelope: Attack 2ms, decay 500ms-1s (exponential)
- Layers: FM tone + subtle reverb tail

```python
def synth_notification(sr=48000, carrier_freq=1000, mod_ratio=1.4, mod_index=3.0, decay_ms=600):
    """Bell-like notification using FM synthesis."""
    n_samples = int(sr * decay_ms / 1000)
    t = np.arange(n_samples) / sr

    # FM synthesis — inharmonic ratio for bell timbre
    mod_freq = carrier_freq * mod_ratio
    # Decaying modulation index for evolving timbre
    mod_env = mod_index * np.exp(-t * 4)
    modulator = mod_env * np.sin(2 * np.pi * mod_freq * t)
    carrier = np.sin(2 * np.pi * carrier_freq * t + modulator)

    # Exponential decay envelope
    attack = int(sr * 0.002)
    env = np.exp(-t * (5000 / decay_ms))
    env[:attack] = np.linspace(0, 1, attack)

    result = carrier * env
    # Cosine fade edges
    fade = int(sr * 0.002)
    result[:fade] *= 0.5 * (1 - np.cos(np.pi * np.arange(fade) / fade))
    result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result / (np.max(np.abs(result)) + 1e-10)
```

### Toggle On / Off
- Approach: Paired ascending/descending tones
- Frequencies: On = 600→900 Hz sweep, Off = 900→600 Hz sweep
- Envelope: 5ms attack, 60-80ms total, 20ms release
- Layers: Sine sweep + subtle click transient at start

### Loading / Progress
- Approach: Looping gentle pulse or slow filter sweep on noise
- Frequencies: 500-1500 Hz bandpass sweep (slow, 1-2s cycle)
- Envelope: Smooth sine-shaped amplitude modulation at 1-3 Hz
- Layers: Filtered noise + quiet sine tone for pitch anchor

### Menu Open / Close
- Approach: Fast whoosh + tonal element
- Frequencies: Noise sweep 500→4000 Hz (open) or 4000→500 Hz (close), 80-150ms
- Envelope: Attack 5ms, fast decay
- Layers: Bandpass noise sweep + brief tonal accent at end

---

## Combat / Action

### Sword Swing
- Approach: Bandpass noise sweep with metallic ring
- Frequencies: Noise sweep 500→2000 Hz, metallic ring at 3-5 kHz
- Envelope: Attack 5ms, body 100-200ms, metallic tail 100-300ms
- Layers: (1) Bandpass noise sweep for air movement, (2) High sine with inharmonic partials for metallic ring

```python
def synth_sword_swing(sr=48000, duration_ms=200):
    """Sword swing whoosh with metallic edge."""
    n_samples = int(sr * duration_ms / 1000)
    t = np.arange(n_samples) / sr
    duration = duration_ms / 1000

    # Layer 1: Air whoosh — bandpass noise sweep
    noise = np.random.randn(n_samples)
    center_freq = np.linspace(500, 2500, n_samples)
    # Apply time-varying filter via short blocks
    block_size = 512
    whoosh = np.zeros(n_samples)
    for i in range(0, n_samples - block_size, block_size):
        cf = center_freq[i + block_size // 2]
        bw = cf * 0.4
        low = max(20, cf - bw)
        high = min(sr/2 - 1, cf + bw)
        sos = butter(2, [low, high], btype='band', fs=sr, output='sos')
        whoosh[i:i+block_size] = sosfilt(sos, noise[i:i+block_size])

    # Layer 2: Metallic ring
    ring = np.sin(2 * np.pi * 3800 * t) * 0.3 + np.sin(2 * np.pi * 5100 * t) * 0.15
    ring_env = np.exp(-t * 15) * (1 - np.exp(-t * 500))
    ring *= ring_env

    # Combined envelope
    env = np.sin(np.pi * t / duration) ** 0.5  # Arc shape — peaks in middle
    whoosh *= env * 0.7

    result = whoosh + ring
    # Cosine fade edges
    fade = int(sr * 0.002)
    result[:fade] *= 0.5 * (1 - np.cos(np.pi * np.arange(fade) / fade))
    result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result / (np.max(np.abs(result)) + 1e-10)
```

### Sword Clash / Metal Impact
- Approach: Multiple metallic bursts with resonant body decay
- Frequencies: Transient broadband, body resonance 500-2000 Hz, metallic partials 2-8 kHz
- Envelope: Attack <1ms (transient), body decay 200-500ms
- Layers: (1) Noise transient (broadband, 2ms), (2) Resonant body (bandpass noise, 200ms decay), (3) Metallic ring (inharmonic sines, 300-500ms decay)

### Gunshot
- Approach: Three-layer: sharp transient + low body + noise tail
- Frequencies: Transient broadband, body 80-200 Hz, tail 200-4000 Hz
- Envelope: Transient 1-2ms, body 30-80ms, tail 100-300ms
- Layers: (1) Short noise burst for crack, (2) Sine at 100Hz with fast decay for body, (3) Filtered noise with medium decay for tail

```python
def synth_gunshot(sr=48000, caliber='medium'):
    """Gunshot synthesis — configurable by caliber."""
    configs = {
        'small':  {'body_freq': 150, 'body_decay': 30, 'tail_ms': 100, 'total_ms': 150},
        'medium': {'body_freq': 100, 'body_decay': 50, 'tail_ms': 200, 'total_ms': 300},
        'large':  {'body_freq': 60,  'body_decay': 80, 'tail_ms': 400, 'total_ms': 500},
    }
    cfg = configs[caliber]
    n_samples = int(sr * cfg['total_ms'] / 1000)
    t = np.arange(n_samples) / sr

    # Layer 1: Transient crack
    crack_len = int(sr * 0.002)
    crack = np.random.randn(crack_len) * np.exp(-np.linspace(0, 10, crack_len))
    crack_padded = np.zeros(n_samples)
    crack_padded[:crack_len] = crack

    # Layer 2: Sub body
    body_env = np.exp(-t * (1000 / cfg['body_decay']))
    body_env[:int(sr*0.001)] = np.linspace(0, 1, int(sr*0.001))
    body = np.sin(2 * np.pi * cfg['body_freq'] * t) * body_env * 0.8

    # Layer 3: Noise tail
    tail = np.random.randn(n_samples)
    sos = butter(3, [200, 4000], btype='band', fs=sr, output='sos')
    tail = sosfilt(sos, tail)
    tail_env = np.exp(-t * (3000 / cfg['tail_ms']))
    tail *= tail_env * 0.4

    result = crack_padded + body + tail
    # Safety fade
    fade = int(sr * 0.002)
    result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result / (np.max(np.abs(result)) + 1e-10)
```

### Explosion
- Approach: Layered sub boom + noise burst + debris
- Frequencies: Sub 30-60 Hz, noise burst broadband, debris 500-3000 Hz
- Envelope: Sub 200-500ms decay, noise 100-300ms, debris 1-3s (random bursts)
- Layers: (1) Sine with pitch drop (60→30 Hz) for sub boom, (2) Broadband noise burst for initial blast, (3) Filtered noise with random amplitude for debris/rubble

### Shield / Block
- Approach: Resonant metallic impact with filtered reverb tail
- Frequencies: Impact 200-500 Hz, metallic resonance 1-4 kHz
- Envelope: Sharp attack, 300-600ms resonant decay
- Layers: (1) Impact transient, (2) Resonant bandpass filtered noise, (3) Metallic partials with slow decay

### Punch / Hit
- Approach: Sub transient + mid body + noise crack
- Frequencies: Sub 60-100 Hz, body 200-500 Hz, crack 1-4 kHz
- Envelope: Attack <2ms, total 50-150ms
- Layers: (1) Sine sub punch (60Hz, 30ms), (2) Bandpass noise body, (3) Short highpass noise for crack

### Laser / Plasma
- Approach: Sine sweep with FM modulation and distortion
- Frequencies: Sweep 2000→500 Hz (or 500→2000 Hz for charge-up), FM carrier
- Envelope: Attack 2ms, sustain 100-300ms, release 50ms
- Layers: (1) FM-modulated sine sweep, (2) Noise texture layer, (3) Sub bass for weight

```python
def synth_laser(sr=48000, freq_start=2000, freq_end=400, duration_ms=200, fm_index=5.0):
    """Laser/plasma beam sound."""
    n_samples = int(sr * duration_ms / 1000)
    t = np.arange(n_samples) / sr

    # Exponential frequency sweep
    freq = freq_start * (freq_end / freq_start) ** (t / t[-1])
    phase = 2 * np.pi * np.cumsum(freq) / sr

    # FM modulation for texture
    mod_freq = freq * 1.5
    mod_phase = 2 * np.pi * np.cumsum(mod_freq) / sr
    fm = fm_index * np.sin(mod_phase)

    tone = np.sin(phase + fm)

    # Envelope
    attack = int(sr * 0.002)
    release = int(sr * 0.05)
    env = np.ones(n_samples)
    env[:attack] = np.linspace(0, 1, attack)
    env[-release:] = np.exp(-np.linspace(0, 5, release))

    result = tone * env
    # Soft clip for grit
    result = np.tanh(result * 1.5)
    # Cosine fade edges
    fade = int(sr * 0.002)
    result[:fade] *= 0.5 * (1 - np.cos(np.pi * np.arange(fade) / fade))
    result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result / (np.max(np.abs(result)) + 1e-10)
```

---

## Environment

### Footsteps
- Approach: Short noise burst shaped by surface material filter
- Frequencies: Wood = 200-1500 Hz (warm), Stone = 500-4000 Hz (bright), Grass = 100-800 Hz (dull), Metal = 1000-6000 Hz (ringy)
- Envelope: Attack 1-3ms, decay 30-80ms
- Layers: (1) Impact transient (broadband, 2ms), (2) Surface-filtered noise body
- Variations: Randomize timing, pitch +-10%, and amplitude +-3dB for natural feel

```python
def synth_footstep(sr=48000, surface='stone', seed=42):
    """Footstep on various surfaces."""
    rng = np.random.default_rng(seed)
    surfaces = {
        'wood':   {'low': 200,  'high': 1500, 'decay_ms': 60, 'ring': False},
        'stone':  {'low': 500,  'high': 4000, 'decay_ms': 40, 'ring': True},
        'grass':  {'low': 100,  'high': 800,  'decay_ms': 80, 'ring': False},
        'metal':  {'low': 1000, 'high': 6000, 'decay_ms': 50, 'ring': True},
        'gravel': {'low': 300,  'high': 3000, 'decay_ms': 70, 'ring': False},
    }
    cfg = surfaces[surface]
    duration_ms = cfg['decay_ms'] + 20
    n_samples = int(sr * duration_ms / 1000)
    t = np.arange(n_samples) / sr

    # Noise burst
    noise = rng.standard_normal(n_samples)
    sos = butter(3, [cfg['low'], cfg['high']], btype='band', fs=sr, output='sos')
    filtered = sosfilt(sos, noise)

    # Impact envelope
    attack = int(sr * 0.002)
    env = np.exp(-t * (1000 / (cfg['decay_ms'] / 1000)))
    env[:attack] = np.linspace(0, 1, attack)

    result = filtered * env

    # Optional ring for hard surfaces
    if cfg['ring']:
        ring_freq = rng.uniform(2000, 4000)
        ring = np.sin(2 * np.pi * ring_freq * t) * np.exp(-t * 30) * 0.15
        result += ring

    fade = int(sr * 0.002)
    result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result / (np.max(np.abs(result)) + 1e-10)
```

### Door
- Approach: Low resonant thud + latch click + optional hinge creak
- Frequencies: Thud 80-200 Hz, latch click 2-5 kHz, hinge 300-1000 Hz sweep
- Envelope: Thud 100-200ms decay, click 5-15ms, hinge 200-500ms
- Layers: (1) Resonant lowpass noise for thud, (2) Short bandpass noise for latch, (3) Slow sine sweep for hinges

### Machinery
- Approach: Looping filtered noise + harmonic hum + rhythmic clicks
- Frequencies: Hum 60/120 Hz (mains), noise 200-2000 Hz, clicks 1-4 kHz
- Envelope: Looping — steady state with rhythmic modulation
- Layers: (1) Sawtooth hum at 60 Hz + harmonics, (2) Bandpass noise for mechanical texture, (3) Periodic click pattern

### Vehicle Engine
- Approach: Filtered sawtooth harmonics + sub rumble + noise
- Frequencies: Fundamental 40-150 Hz (RPM dependent), harmonics up to 2 kHz
- Envelope: Looping — steady state, modulate for acceleration
- Layers: (1) Sawtooth at engine RPM frequency, (2) Sub sine for rumble, (3) Bandpass noise for mechanical texture

---

## Transitions

### Whoosh
- Approach: Fast bandpass noise sweep
- Frequencies: Sweep 300→3000 Hz (or reverse), bandwidth narrows at peak
- Envelope: Arc shape — quiet at edges, loud in center (100-300ms)
- Layers: Single bandpass noise sweep, optionally add tonal element

```python
def synth_whoosh(sr=48000, duration_ms=200, freq_start=300, freq_end=3000):
    """Fast whoosh transition sound."""
    n_samples = int(sr * duration_ms / 1000)
    t = np.arange(n_samples) / sr
    duration = duration_ms / 1000

    noise = np.random.randn(n_samples)

    # Time-varying bandpass filter
    block_size = 256
    result = np.zeros(n_samples)
    center_freqs = np.linspace(freq_start, freq_end, n_samples)

    for i in range(0, n_samples - block_size, block_size):
        cf = center_freqs[i + block_size // 2]
        bw = cf * 0.3
        low = max(20, cf - bw)
        high = min(sr/2 - 1, cf + bw)
        sos = butter(3, [low, high], btype='band', fs=sr, output='sos')
        result[i:i+block_size] = sosfilt(sos, noise[i:i+block_size])

    # Arc envelope — peak in center
    env = np.sin(np.pi * t / duration) ** 1.5
    result *= env

    fade = int(sr * 0.002)
    result[:fade] *= 0.5 * (1 - np.cos(np.pi * np.arange(fade) / fade))
    result[-fade:] *= 0.5 * (1 + np.cos(np.pi * np.arange(fade) / fade))
    return result / (np.max(np.abs(result)) + 1e-10)
```

### Riser
- Approach: Ascending filtered noise or sine sweep (slow build)
- Frequencies: Start 100-300 Hz, end 2000-6000 Hz (sweep over 1-5s)
- Envelope: Exponential crescendo — quiet start, loud finish
- Layers: (1) Filtered noise sweep, (2) Sine sweep for pitch anchor, (3) Optional sub build

### Impact / Stinger
- Approach: Sub drop + noise burst + reverb tail
- Frequencies: Sub 30-60 Hz, noise broadband, reverb tail shaped by lowpass
- Envelope: Instant attack, sub 200-500ms, reverb tail 1-3s
- Layers: (1) Sub sine with pitch drop, (2) Broadband noise transient, (3) Reverb tail

### Tape Stop
- Approach: Pitch descent with accelerating slowdown curve
- Frequencies: Start at original pitch, end near 0 Hz
- Envelope: Maintains volume initially, fades as pitch drops below audible
- Implementation: Exponentially decreasing playback rate applied to any source sound

### Glitch
- Approach: Buffer repeat + bit-crush + random stutters
- Frequencies: Depends on source, typically mid-range 200-4000 Hz
- Envelope: Irregular — random gate pattern
- Layers: (1) Repeated micro-segments (5-50ms), (2) Bit-crushed noise, (3) Random pitch shifts

---

## Nature / Ambient

### Rain
- Approach: Filtered noise with random amplitude modulation
- Frequencies: Broadband, emphasis on 2-8 kHz for brightness
- Envelope: Steady state for loops, slow fade in/out for one-shots
- Layers: (1) Lowpass filtered noise for body, (2) Highpass filtered noise for brightness, (3) Random sparse clicks for individual drops
- Looping: Use crossfade at loop boundaries (50-100ms)

```python
def synth_rain(sr=48000, duration_s=5.0, intensity=0.7):
    """Rain ambience — loopable."""
    n_samples = int(sr * duration_s)
    t = np.arange(n_samples) / sr

    # Base noise
    noise = np.random.randn(n_samples)

    # Low layer — body of rain
    sos_low = butter(3, 3000, btype='low', fs=sr, output='sos')
    low = sosfilt(sos_low, noise) * 0.5

    # High layer — brightness/sizzle
    sos_high = butter(3, [4000, 10000], btype='band', fs=sr, output='sos')
    high_noise = np.random.randn(n_samples)
    high = sosfilt(sos_high, high_noise) * 0.3 * intensity

    # Random amplitude modulation for variation
    mod = np.random.randn(n_samples)
    sos_mod = butter(2, 2, btype='low', fs=sr, output='sos')
    mod = sosfilt(sos_mod, mod)
    mod = 0.7 + 0.3 * mod / (np.max(np.abs(mod)) + 1e-10)

    result = (low + high) * mod

    # Crossfade for seamless looping (100ms)
    xfade = int(sr * 0.1)
    fade_in = 0.5 * (1 - np.cos(np.pi * np.arange(xfade) / xfade))
    fade_out = 0.5 * (1 + np.cos(np.pi * np.arange(xfade) / xfade))
    result[:xfade] = result[:xfade] * fade_in + result[-xfade:] * fade_out
    result[-xfade:] = result[:xfade]  # Copy for seamless loop

    return result / (np.max(np.abs(result)) + 1e-10)
```

### Wind
- Approach: Slowly modulated bandpass noise
- Frequencies: Center 200-1500 Hz, bandwidth varies slowly
- Envelope: Steady state with slow (0.1-0.5 Hz) amplitude modulation
- Layers: (1) Main bandpass noise with LFO on center frequency, (2) Optional gusts — faster sweeps with higher amplitude
- Looping: Crossfade boundaries

### Thunder
- Approach: Sub boom + crackle noise chain + long reverb tail
- Frequencies: Sub 30-80 Hz, crackle 500-5000 Hz, reverb broadband
- Envelope: Sub 200-500ms, crackle irregular bursts over 1-3s, reverb tail 3-8s
- Layers: (1) Sub sine with pitch drop for initial boom, (2) Chains of short noise bursts for crackle, (3) Long reverb for rumble/echo

### Water Drop
- Approach: Karplus-Strong pluck at high frequency with short delay
- Frequencies: 2000-5000 Hz fundamental
- Envelope: Attack <1ms, decay 50-200ms
- Layers: Single Karplus-Strong string with high frequency and short decay

### Fire / Crackle
- Approach: Random short noise bursts with bandpass filter
- Frequencies: 500-4000 Hz
- Envelope: Random sparse bursts (2-10ms each), irregular timing
- Layers: (1) Continuous low-level filtered noise for base, (2) Random short bursts for crackle/pop
- Looping: Crossfade boundaries

---

## Quick Reference Table

| Category | Typical Duration | Default SR | Channels | Key Technique |
|----------|-----------------|------------|----------|---------------|
| UI Click | 5-20ms | 44.1kHz | Mono | Bandpass noise burst |
| UI Notification | 300-800ms | 44.1kHz | Mono | FM bell synthesis |
| Sword Swing | 100-300ms | 48kHz | Mono | Noise sweep + ring |
| Gunshot | 100-500ms | 48kHz | Mono | 3-layer: crack+body+tail |
| Explosion | 500ms-3s | 48kHz | Stereo | Sub boom + noise + debris |
| Footstep | 30-100ms | 48kHz | Mono | Surface-filtered noise |
| Whoosh | 100-400ms | 48kHz | Mono/Stereo | Bandpass noise sweep |
| Riser | 1-5s | 48kHz | Stereo | Ascending filtered noise |
| Rain (loop) | 5-30s | 48kHz | Stereo | Modulated filtered noise |
| Thunder | 2-8s | 48kHz | Stereo | Sub boom + crackle + reverb |
