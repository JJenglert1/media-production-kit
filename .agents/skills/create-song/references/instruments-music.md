# Music Instrument Synthesis Recipes

All instruments synthesized from scratch using numpy/scipy. Each recipe is a self-contained function. For music production use.

## Drum Synthesis

### Kick Drum

```python
def synth_kick(duration=0.5, freq_start=180, freq_end=45, click_freq=1000,
               body_decay=0.15, sub_decay=0.3, sr=SR):
    """Layered kick: click transient + pitched body + sub layer.
    3ms quadratic fade-in to prevent initial pop."""
    t = np.arange(int(sr * duration)) / sr
    n = len(t)
    # Pitch envelope (exponential drop)
    pitch_env = freq_end + (freq_start - freq_end) * np.exp(-t * 30)
    phase = 2 * np.pi * np.cumsum(pitch_env) / sr
    # Body
    body = np.sin(phase) * np.exp(-t / body_decay)
    # Sub layer
    sub = np.sin(2*np.pi*freq_end*t) * np.exp(-t / sub_decay)
    # Click transient
    click_n = int(0.005 * sr)
    click_env = np.zeros(n)
    click_env[:click_n] = np.exp(-np.arange(click_n) / (click_n * 0.2))
    click = np.sin(2*np.pi*click_freq*t) * click_env
    # Mix and fade
    kick = body * 0.7 + sub * 0.3 + click * 0.15
    fade_n = int(0.003 * sr)
    kick[:fade_n] *= np.linspace(0, 1, fade_n) ** 2  # quadratic fade-in
    return kick / np.max(np.abs(kick) + 1e-10)
```

Key parameters:
- `freq_start` 120-350 Hz (higher = more attack click)
- `freq_end` 30-65 Hz (fundamental pitch)
- `body_decay` 80-400ms (shorter = tighter, longer = boomy)
- For 808 kick: `freq_end=38`, `body_decay=0.8-2.0`, no click

### Snare Drum

```python
def synth_snare(duration=0.3, body_freq=200, body_decay=0.08, noise_decay=0.15, sr=SR):
    """Snare: pitched body (~180-250Hz) + bandpass noise (snare wires).
    Body: 40% of signal, Noise: 60%."""
    t = np.arange(int(sr * duration)) / sr
    n = len(t)
    # Body with fast pitch drop
    pitch = body_freq + 80 * np.exp(-t * 50)
    phase = 2 * np.pi * np.cumsum(pitch) / sr
    body = np.sin(phase) * np.exp(-t / body_decay)
    # Noise (snare wires) bandpassed 1-8kHz
    noise = np.random.randn(n)
    sos = butter(2, [1000, 8000], btype='band', fs=sr, output='sos')
    noise = sosfilt(sos, noise) * np.exp(-t / noise_decay)
    snare = body * 0.4 + noise * 0.6
    fade_n = int(0.002 * sr)
    snare[:fade_n] *= np.linspace(0, 1, fade_n)
    return snare / np.max(np.abs(snare) + 1e-10)
```

### Hi-Hat

```python
def synth_hihat(duration=0.1, open_hat=False, sr=SR):
    """Hi-hat: inharmonic square waves (TR-808 ratios) + highpass > 6kHz.
    Closed: ~25-50ms decay. Open: ~150-250ms decay."""
    if open_hat: duration = max(duration, 0.4)
    t = np.arange(int(sr * duration)) / sr
    # TR-808 metallic partials (NOT harmonically related = metallic clang)
    freqs = [204.77, 297.64, 366.47, 410.21, 517.28, 626.48]
    freqs += [f * 2 for f in freqs[:3]]  # higher partials for shimmer
    metal = sum(np.sign(np.sin(2*np.pi*f*t)) for f in freqs if f < SR/2) / len(freqs)
    sos = butter(4, 6000, btype='high', fs=sr, output='sos')
    hat = sosfilt(sos, metal)
    env = np.exp(-t / (0.15 if open_hat else 0.025))
    hat *= env
    fade_n = int(0.002 * sr)
    hat[:fade_n] *= np.linspace(0, 1, fade_n)
    return hat / np.max(np.abs(hat) + 1e-10)
```

### Clap

```python
def synth_clap(duration=0.3, sr=SR):
    """Clap: 3-4 noise microbursts (10-15ms apart) + longer noise tail.
    Bandpass 1-3.5kHz."""
    t = np.arange(int(sr * duration)) / sr
    n = len(t)
    clap = np.zeros(n)
    hits = [(0.0, 0.005, 0.3), (0.015, 0.005, 0.4), (0.03, 0.005, 0.5), (0.045, 0.08, 1.0)]
    for ht, hd, ha in hits:
        start = int(ht * sr)
        clap[start:] += np.random.randn(n - start) * ha * np.exp(-np.arange(n - start) / (hd * sr))
    sos = butter(2, [1000, 3500], btype='band', fs=sr, output='sos')
    clap = sosfilt(sos, clap)
    return clap / np.max(np.abs(clap) + 1e-10)
```

### 808 Bass

```python
def synth_808(freq=55, duration=2.0, pitch_drop=True, saturate=False, sr=SR):
    """TR-808 bass: long sustaining sine with optional pitch drop and saturation.
    Pitch drop: ~2 semitones over ~50ms at start."""
    t = np.arange(int(sr * duration)) / sr
    if pitch_drop:
        penv = freq + (freq * 2**(2/12) - freq) * np.exp(-t * 40)
        phase = 2*np.pi * np.cumsum(penv) / sr
    else:
        phase = 2*np.pi * freq * t
    bass = np.sin(phase)
    env = np.exp(-t / (duration * 0.6))
    fade_n = int(0.005 * sr)
    env[:fade_n] *= np.linspace(0, 1, fade_n)
    bass *= env
    if saturate: bass = np.tanh(bass * 2.5) * 0.7
    return bass / np.max(np.abs(bass) + 1e-10)
```

### Cowbell, Rimshot, Conga, Cymbal

```python
def synth_cowbell(duration=0.15, sr=SR):
    """Cowbell: two inharmonic square waves (~540Hz + ~845Hz). TR-808 recipe."""
    t = np.arange(int(sr * duration)) / sr
    sig = (np.sign(np.sin(2*np.pi*560*t)) + np.sign(np.sin(2*np.pi*845*t))) / 2
    sos = butter(2, [500, 5000], btype='band', fs=sr, output='sos')
    sig = sosfilt(sos, sig) * (0.7*np.exp(-t/0.01) + 0.3*np.exp(-t/0.15))
    return sig / np.max(np.abs(sig) + 1e-10)

def synth_rimshot(duration=0.1, sr=SR):
    """Rimshot: high sine ping (~1700Hz) + short noise crack."""
    t = np.arange(int(sr * duration)) / sr
    sig = np.sin(2*np.pi*400*t) * np.exp(-t/0.02) * 0.5
    noise = np.random.randn(len(t)) * np.exp(-t/0.01)
    sos = butter(2, [2000, 8000], btype='band', fs=sr, output='sos')
    sig += sosfilt(sos, noise) * 0.5
    return sig / np.max(np.abs(sig) + 1e-10)

def synth_conga(duration=0.4, pitch='high', sr=SR):
    """Conga: sine + pitch envelope + body resonance.
    High: 300Hz, Low: 200Hz, Bongo high: 420Hz."""
    freqs = {'high': 300, 'low': 200, 'bongo': 420}
    f0 = freqs.get(pitch, 300)
    t = np.arange(int(sr * duration)) / sr
    freq = f0 + f0*0.5 * np.exp(-t / 0.008)
    phase = 2*np.pi * np.cumsum(freq) / sr
    body = np.sin(phase) + 0.3*np.sin(phase*2.02) + 0.1*np.sin(phase*3.05)
    decay = 0.35 if 'low' in pitch else 0.2
    env = np.exp(-t / decay)
    env[:int(0.003*sr)] *= np.linspace(0, 1, int(0.003*sr))
    slap = np.random.randn(len(t)) * np.exp(-t/0.003) * 0.15
    sig = body * env + slap
    return sig / np.max(np.abs(sig) + 1e-10)

def synth_cymbal(duration=2.0, n_partials=25, sr=SR):
    """Crash/ride cymbal: many inharmonic partials. Higher partials decay slower."""
    t = np.arange(int(sr * duration)) / sr
    sig = np.zeros(len(t))
    base = 340
    for i in range(n_partials):
        f = base * (1.0 + i * 1.1347)
        if f >= SR/2: break
        amp = 0.5 / (1 + i*0.3)
        decay = 0.3 + i*0.05
        sig += amp * np.sin(2*np.pi*f*t + np.random.uniform(0, 2*np.pi)) * np.exp(-t/min(decay, duration))
    # Noise burst
    sig += np.random.randn(len(t)) * np.exp(-t/0.05) * 0.3
    sos = butter(2, 800, btype='high', fs=sr, output='sos')
    sig = sosfilt(sos, sig)
    return sig / np.max(np.abs(sig) + 1e-10)

def synth_shaker(duration=0.08, sr=SR):
    """Shaker: HP-filtered noise with fast tremolo (120Hz = rattling beads)."""
    t = np.arange(int(sr * duration)) / sr
    noise = np.random.randn(len(t))
    sos = butter(2, 5000, btype='high', fs=sr, output='sos')
    noise = sosfilt(sos, noise)
    tremolo = 0.5 + 0.5 * np.sin(2*np.pi*120*t)
    env = np.exp(-t / 0.04)
    sig = noise * tremolo * env
    return sig / np.max(np.abs(sig) + 1e-10)

def synth_tambourine(duration=0.25, sr=SR):
    """Tambourine: noise body + metallic jingles (6.5-12.3kHz partials)."""
    t = np.arange(int(sr * duration)) / sr
    noise = np.random.randn(len(t)) * np.exp(-t/0.03) * 0.4
    jingles = sum(0.12 * np.sin(2*np.pi*f*t + np.random.uniform(0, 2*np.pi))
                  for f in [6500, 7200, 8100, 9400, 10800, 12300])
    jingles *= np.exp(-t / 0.12) * 0.6
    sig = noise + jingles
    return sig / np.max(np.abs(sig) + 1e-10)
```

### Percussion Synthesis Reference Table

| Sound | Key Frequencies | Attack/Decay | Waveform | Character |
|-------|----------------|-------------|----------|-----------|
| Kick | Body 40-60Hz, Attack 150-300Hz | 3ms / 80-400ms | Sine + pitch env | Pitch sweep essential |
| Snare | Body 150-250Hz, Wires broadband | 2ms / 80ms + 150ms | Sine + noise | Body/noise ratio = character |
| Closed HH | 320Hz * [1, 1.47, 1.83...] | 1ms / 25ms | Noise + squares | Very short |
| Open HH | Same as closed | 1ms / 250ms | Noise + squares | Longer decay only |
| Clap | Band 1000-3500Hz | 4x5ms bursts + 100ms | Filtered noise | Microbursts = clap |
| Cowbell | 540Hz + 845Hz | <1ms / 10ms + 80ms | Two squares | Detuned pair |
| Conga | Hi 300Hz, Lo 200Hz | 3ms / 200-350ms | Sine + harmonics | Fast pitch drop |
| Crash | 300Hz * inharmonic ratios | 2ms / 0.3-1.5s | Many sines + noise | Inharmonic = metallic |
| 808 | 30-50Hz sustained | 5ms / 0.8-3s | Sine, optional saturation | Long decay defines it |

## Synth Instruments

### Supersaw / Detuned Saw

```python
def synth_supersaw(freq, duration, n_voices=7, detune_cents=20, sr=SR):
    """Supersaw: multiple detuned sawtooth waves. The core EDM/trance sound."""
    t = np.arange(int(sr * duration)) / sr
    out = np.zeros(len(t))
    for v in range(n_voices):
        cents = detune_cents * (2*v/(n_voices-1) - 1) if n_voices > 1 else 0
        vf = freq * (2 ** (cents / 1200))
        phase = (vf * t) % 1.0
        out += (2.0 * phase - 1.0) / n_voices
    return out
```

### Pad (Detuned + LFO Filter)

```python
def synth_pad(freq, duration, n_voices=5, detune_cents=15, sr=SR):
    """Lush pad: detuned saw+triangle mix with slow LFO filter sweep.
    Use octave 3 root for warmth (not octave 5+)."""
    t = np.arange(int(sr * duration)) / sr
    pad = np.zeros(len(t))
    for v in range(n_voices):
        cents = detune_cents * (2*v/(n_voices-1) - 1) if n_voices > 1 else 0
        vf = freq * (2 ** (cents / 1200))
        phase = (vf * t) % 1.0
        saw = 2.0 * phase - 1.0
        tri = 2.0 * np.abs(2.0 * phase - 1.0) - 1.0
        pad += (saw * 0.6 + tri * 0.4) / n_voices
    # Lowpass with slow LFO movement
    cutoff = 1500 + 500 * np.sin(2*np.pi*0.1*t)
    # Block-based filter sweep
    block = 256
    sos = butter(2, 1000, btype='low', fs=sr, output='sos')
    zi = np.zeros((sos.shape[0], 2))
    out = np.zeros(len(pad))
    for s in range(0, len(pad), block):
        e = min(s + block, len(pad))
        fc = np.clip(cutoff[s], 20, sr/2 - 100)
        sos = butter(2, fc, btype='low', fs=sr, output='sos')
        out[s:e], zi = sosfilt(sos, pad[s:e], zi=zi)
    return out
```

### Sub Bass

```python
def synth_sub_bass(freq, duration, sr=SR):
    """Clean sub bass: pure sine at octave 1-2. Keep below 100Hz."""
    t = np.arange(int(sr * duration)) / sr
    return np.sin(2*np.pi*freq*t)
```

### Pluck / Arp

```python
def synth_pluck(freq, duration=0.5, brightness=0.7, sr=SR):
    """Pluck: saw with fast filter envelope (opens then closes).
    Good for arpeggios. Use octave 3-4."""
    t = np.arange(int(sr * duration)) / sr
    phase = (freq * t) % 1.0
    saw = 2.0 * phase - 1.0
    # Filter envelope: starts bright, drops fast
    filt_start = min(8000, brightness * 12000 + 1000)
    filt_end = freq * 2
    filt_env = filt_end + (filt_start - filt_end) * np.exp(-t * 20)
    # Block-based sweep
    block = 64
    sos = butter(2, 1000, btype='low', fs=sr, output='sos')
    zi = np.zeros((sos.shape[0], 2))
    out = np.zeros(len(saw))
    for s in range(0, len(saw), block):
        e = min(s + block, len(saw))
        fc = np.clip(filt_env[s], 20, sr/2 - 100)
        sos = butter(2, fc, btype='low', fs=sr, output='sos')
        out[s:e], zi = sosfilt(sos, saw[s:e], zi=zi)
    env = np.exp(-t / 0.15)
    env[:int(sr*0.002)] *= np.linspace(0, 1, int(sr*0.002))
    return out * env
```

### Rhodes / Electric Piano (FM)

```python
def synth_rhodes(freq, duration=2.0, velocity=0.8, sr=SR):
    """Rhodes via FM: tine (1:14 ratio) + tone bar (1:1 ratio) + tremolo.
    Soft = warm, loud = bark. Velocity controls brightness."""
    t = np.arange(int(sr * duration)) / sr
    # Tine: 1:14 ratio gives metallic "tine" character
    tine_idx = velocity * 2.5 * np.exp(-t / 0.15)
    tine = np.sin(2*np.pi*freq*t + tine_idx * np.sin(2*np.pi*freq*14*t))
    tine *= np.exp(-t / 0.3)
    # Tone bar: 1:1 ratio gives warm body
    bar_idx = velocity * 1.5 * np.exp(-t / 0.8)
    bar = np.sin(2*np.pi*freq*t + bar_idx * np.sin(2*np.pi*freq*t))
    bar *= np.exp(-t / (duration * 0.4))
    rhodes = tine * 0.4 + bar * 0.6
    # Tremolo (~4.8Hz)
    rhodes *= 1.0 + 0.15 * np.sin(2*np.pi*4.8*t)
    rhodes[:int(sr*0.003)] *= np.linspace(0, 1, int(sr*0.003))
    return rhodes / np.max(np.abs(rhodes) + 1e-10)
```

### Piano (Additive + Inharmonicity)

```python
def synth_piano(freq, duration=2.0, velocity=0.8, sr=SR):
    """Piano: additive synthesis with inharmonicity (B~0.0003).
    Higher partials slightly sharp + faster decay. Hammer noise burst."""
    t = np.arange(int(sr * duration)) / sr
    piano = np.zeros(len(t))
    B = 0.0003  # inharmonicity coefficient
    n_partials = min(40, int((SR/2) / freq))
    for i in range(1, n_partials + 1):
        pf = i * freq * np.sqrt(1 + B * i * i)
        if pf >= SR/2: break
        amp = velocity / (i ** 0.7)
        decay = duration * 0.5 / (1 + i * 0.15)
        piano += amp * np.sin(2*np.pi*pf*t) * np.exp(-t / decay)
    piano[:int(sr*0.005)] *= np.linspace(0, 1, int(sr*0.005))
    # Hammer noise
    hammer = np.random.randn(len(t)) * np.exp(-t/0.003) * 0.05 * velocity
    sos = butter(2, [1000, 5000], btype='band', fs=sr, output='sos')
    piano += sosfilt(sos, hammer)
    return piano / np.max(np.abs(piano) + 1e-10)
```

### Organ (Hammond Drawbars)

```python
def synth_organ(freq, duration, drawbars=(8,8,8,0,0,0,0,0,0), sr=SR):
    """Hammond organ: 9 drawbars control harmonic levels.
    Multipliers: [0.5, 1.5, 1, 2, 3, 4, 5, 6, 8]
    Classic registrations: (88 8000 000)=full, (00 8800 000)=gospel,
    (88 8800 000)=Jimmy Smith, (84 8848 448)=rock organ"""
    t = np.arange(int(sr * duration)) / sr
    multipliers = [0.5, 1.5, 1, 2, 3, 4, 5, 6, 8]
    organ = np.zeros(len(t))
    for val, mult in zip(drawbars, multipliers):
        if val > 0 and freq * mult < SR/2:
            organ += (val / 8.0) * np.sin(2*np.pi * freq * mult * t)
    # Key click
    click_n = int(sr * 0.003)
    if click_n < len(organ):
        organ[:click_n] += np.random.randn(click_n) * 0.1
    return organ / np.max(np.abs(organ) + 1e-10)
```

### Strings (Ensemble)

```python
def synth_strings(freq, duration, n_voices=7, sr=SR):
    """String ensemble: detuned saws + vibrato (5.5Hz, delayed onset 300ms).
    Slow attack for realistic bowing."""
    t = np.arange(int(sr * duration)) / sr
    strings = np.zeros(len(t))
    vib_onset = np.clip(t / 0.3, 0, 1)
    vibrato = 0.003 * vib_onset * np.sin(2*np.pi*5.5*t)
    for v in range(n_voices):
        cents = 15 * (2*v/(n_voices-1) - 1) if n_voices > 1 else 0
        vf = freq * (2 ** (cents / 1200))
        phase = np.cumsum(vf * (1 + vibrato)) / sr
        strings += (2.0 * (phase % 1.0) - 1.0) / n_voices
    sos = butter(2, min(5000, freq * 8), btype='low', fs=sr, output='sos')
    strings = sosfilt(sos, strings)
    return strings
```

### Guitar (Karplus-Strong)

```python
def karplus_strong(freq, duration=2.0, damping=0.996, brightness=0.5, sr=SR):
    """Karplus-Strong plucked string. Fill delay line with noise, average adjacent samples.
    damping: 0.990=muted, 0.996=normal, 0.999=nylon (long sustain)."""
    delay_len = int(sr / freq)
    n = int(sr * duration)
    out = np.zeros(n)
    noise = np.random.uniform(-1, 1, delay_len)
    if brightness < 1.0:
        sos = butter(1, 2000 + brightness * 8000, btype='low', fs=sr, output='sos')
        noise = sosfilt(sos, noise)
    out[:delay_len] = noise
    for i in range(delay_len, n):
        out[i] = damping * 0.5 * (out[i - delay_len] + out[i - delay_len - 1])
    return out / np.max(np.abs(out) + 1e-10)
```

### Brass Stab

```python
def synth_brass(freq, duration=0.5, sr=SR):
    """Brass stab: detuned saws + fast filter envelope (mimics embouchure).
    High resonance for brassy character."""
    t = np.arange(int(sr * duration)) / sr
    saw1 = 2.0 * ((freq * t) % 1.0) - 1.0
    saw2 = 2.0 * ((freq * 1.003 * t) % 1.0) - 1.0
    source = (saw1 + saw2) / 2
    filt_env = 300 + 4000 * np.exp(-t / 0.05)
    block = 64
    sos = butter(2, 1000, btype='low', fs=sr, output='sos')
    zi = np.zeros((sos.shape[0], 2))
    out = np.zeros(len(source))
    for s in range(0, len(source), block):
        e = min(s + block, len(source))
        fc = np.clip(filt_env[s], 20, sr/2 - 100)
        sos = butter(2, fc, btype='low', fs=sr, output='sos')
        out[s:e], zi = sosfilt(sos, source[s:e], zi=zi)
    return out
```

### Acid Bass (TB-303)

```python
def synth_acid(freq, duration=0.3, cutoff_start=4000, cutoff_end=200,
               resonance=15.0, accent=False, sr=SR):
    """TB-303 acid: saw through resonant lowpass with fast cutoff envelope.
    HIGH resonance (Q=10-20), fast filter sweep = the acid sound."""
    t = np.arange(int(sr * duration)) / sr
    saw = 2.0 * ((freq * t) % 1.0) - 1.0
    if accent: cutoff_start *= 1.5; resonance *= 1.3
    filt_env = cutoff_end + (cutoff_start - cutoff_end) * np.exp(-t * 15)
    block = 64
    sos = butter(2, 1000, btype='low', fs=sr, output='sos')
    zi = np.zeros((sos.shape[0], 2))
    out = np.zeros(len(saw))
    for s in range(0, len(saw), block):
        e = min(s + block, len(saw))
        fc = np.clip(filt_env[s], 20, sr/2 - 100)
        sos = butter(2, fc, btype='low', fs=sr, output='sos')
        out[s:e], zi = sosfilt(sos, saw[s:e], zi=zi)
    env = np.exp(-t / 0.2)
    env[:int(sr*0.002)] *= np.linspace(0, 1, int(sr*0.002))
    return np.tanh(out * env * 1.5)
```

### Flute

```python
def synth_flute(freq, duration=2.0, sr=SR):
    """Flute: mostly sine + weak 2nd harmonic + breath noise + delayed vibrato."""
    t = np.arange(int(sr * duration)) / sr
    vib_onset = np.clip(t / 0.4, 0, 1)
    vibrato = 1.0 + 0.005 * vib_onset * np.sin(2*np.pi*5*t)
    phase = 2*np.pi * np.cumsum(freq * vibrato) / sr
    tone = np.sin(phase) + 0.1 * np.sin(2 * phase)
    noise = np.random.randn(len(t)) * 0.05
    sos = butter(2, [freq*0.8, min(freq*3, SR/2-100)], btype='band', fs=sr, output='sos')
    breath = sosfilt(sos, noise)
    return tone + breath
```

### Clarinet

```python
def synth_clarinet(freq, duration=2.0, sr=SR):
    """Clarinet: odd harmonics dominant (like square but with specific roll-off)."""
    t = np.arange(int(sr * duration)) / sr
    clar = np.zeros(len(t))
    amps = {1: 1.0, 3: 0.75, 5: 0.5, 7: 0.14, 9: 0.5, 11: 0.12, 13: 0.17}
    for h, amp in amps.items():
        if h * freq < SR/2:
            clar += amp * np.sin(2*np.pi*h*freq*t)
    return clar
```

### Choir / Vocal Pad (Formant)

```python
def synth_choir(freq, duration=4.0, vowel='a', n_voices=6, sr=SR):
    """Choir pad: detuned saws through formant filter bank.
    Add breathiness (noise) and per-voice vibrato."""
    FORMANTS = {
        'a': [(800,80), (1150,70), (2800,100), (3500,120)],
        'e': [(400,70), (1600,80), (2700,100), (3300,120)],
        'i': [(270,60), (2300,90), (3000,100), (3700,120)],
        'o': [(500,70), (700,80), (2800,100), (3500,120)],
        'u': [(300,60), (640,70), (2700,100), (3500,120)],
    }
    GAINS = {'a': [1,.5,.3,.2], 'e': [1,.5,.25,.15], 'i': [1,.4,.2,.15],
             'o': [1,.6,.2,.15], 'u': [1,.5,.15,.1]}
    t = np.arange(int(sr * duration)) / sr
    choir = np.zeros(len(t))
    for v in range(n_voices):
        cents = 20 * (2*v/(n_voices-1) - 1) if n_voices > 1 else 0
        vf = freq * (2 ** (cents / 1200))
        vib = 1.0 + 0.004 * np.sin(2*np.pi*(5+np.random.uniform(-.5,.5))*t + np.random.uniform(0, 2*np.pi))
        phase = np.cumsum(vf * vib) / sr
        choir += (2.0*(phase%1.0) - 1.0 + np.random.randn(len(t))*0.03) / n_voices
    # Formant filter
    out = np.zeros(len(choir))
    for (fc, bw), gain in zip(FORMANTS[vowel], GAINS[vowel]):
        sos = butter(2, [max(20,fc-bw/2), min(SR/2-100,fc+bw/2)], btype='band', fs=sr, output='sos')
        out += gain * sosfilt(sos, choir)
    return out

def morphing_vowel(sig, sequence='aeiou', sr=SR):
    """Morph between vowels by interpolating formant-filtered outputs."""
    FORMANTS = {'a':[(800,80),(1150,70),(2800,100)],'e':[(400,70),(1600,80),(2700,100)],
                'i':[(270,60),(2300,90),(3000,100)],'o':[(500,70),(700,80),(2800,100)],
                'u':[(300,60),(640,70),(2700,100)]}
    filtered = {}
    for v in sequence:
        if v not in filtered:
            f = np.zeros(len(sig))
            for fc, bw in FORMANTS[v]:
                sos = butter(2, [max(20,fc-bw/2), min(SR/2-100,fc+bw/2)], btype='band', fs=sr, output='sos')
                f += sosfilt(sos, sig)
            filtered[v] = f
    out = np.zeros(len(sig))
    nv = len(sequence)
    for i in range(len(sig)):
        pos = i / len(sig) * (nv - 1)
        idx = min(int(pos), nv - 2)
        frac = pos - idx
        out[i] = filtered[sequence[idx]][i]*(1-frac) + filtered[sequence[idx+1]][i]*frac
    return out
```

### FM Bell

```python
def synth_bell(freq, duration=3.0, sr=SR):
    """FM bell: 1:1.4 ratio with high mod index (inharmonic = bell-like).
    Long decay, no sustain."""
    t = np.arange(int(sr * duration)) / sr
    mod_idx = 8.0 * np.exp(-t / 1.0)
    sig = np.sin(2*np.pi*freq*t + mod_idx * np.sin(2*np.pi*freq*1.4*t))
    sig *= np.exp(-t / (duration * 0.4))
    sig[:int(sr*0.002)] *= np.linspace(0, 1, int(sr*0.002))
    return sig
```

## FM Synthesis Timbre Table

Use with: `sig = sin(2*pi*fc*t + index * sin(2*pi*fc*ratio*t))`

| Timbre | C:M Ratio | Mod Index | Envelope | Notes |
|--------|-----------|-----------|----------|-------|
| Bell/Chime | 1:1.4 | 5-10, decaying | Long decay | Inharmonic = bell |
| Metallic bell | 1:1.414 | 8-15 | Long decay | Irrational ratio |
| Electric piano (tine) | 1:14 | 1.5-3, fast decay | Med decay | DX7 tine character |
| E-piano (warm body) | 1:1 | 1-2 | Med decay | Warmth layer |
| Bass (warm) | 1:1 | 1-3 | Sustained | Adds harmonics |
| Bass (sub) | 1:2 | 0.5-2 | Sustained | Octave harmonics |
| Brass | 1:1 | 3-5 | Med attack | High index = bright |
| Brass stab | 1:1 | 0->5 envelope | Fast attack | Index env = filter opening |
| Clarinet | 1:3 | 2-4 | Sustained | Odd harmonic emphasis |
| Plucked string | 1:1 | 5->0 fast | Fast decay | Index decay = pluck |
| Marimba | 1:4 | 3-8, very fast | Very short | Percussive |
| Vibraphone | 1:3.5 | 1-3 | Med-long | Similar to bell, lower index |
| Glass/Crystal | 1:1.5 | 3-7 | Long | Near-integer = beating |

## Octave/Register Guidelines

Favor lower/mid registers for warmth:
- Sub bass: octave 1-2 (30-120 Hz)
- Bass: octave 2-3 (60-250 Hz)
- Pads: root in octave 3 (130-260 Hz)
- Arps: octave 3-4 (130-520 Hz)
- Leads: octave 4 (260-520 Hz)
- Bells/FX: octave 4-5

High-pitched synths (octave 5+) sound shrill and cheap. Warmth lives in the mid-range.
