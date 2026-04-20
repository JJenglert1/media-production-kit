# Reference Track Analysis

When the user provides an audio file as a reference ("make something like this"), analyze it to extract musical parameters before composing. All analysis uses only numpy and scipy — no librosa or other audio libraries.

## Load Audio

```python
import numpy as np
from scipy.io import wavfile
from scipy.signal import resample, butter, sosfilt

def load_audio(filename, sr=44100):
    """Load an audio file, convert to mono float64 normalized to [-1, 1].
    Supports .wav natively; tries soundfile as fallback for other formats.
    Resamples to target sample rate if needed.
    """
    try:
        orig_sr, data = wavfile.read(filename)
    except Exception:
        try:
            import soundfile as sf
            data, orig_sr = sf.read(filename, dtype='float64')
        except ImportError:
            raise RuntimeError(
                f"Cannot read {filename}: scipy only supports .wav. "
                "Install soundfile (`pip install soundfile`) for mp3/flac/ogg."
            )

    # Convert to float64 in [-1, 1]
    data = np.array(data, dtype=np.float64)
    if data.dtype == np.float64 and np.max(np.abs(data)) <= 1.0:
        pass  # already normalized float
    elif np.issubdtype(data.dtype, np.integer) or np.max(np.abs(data)) > 1.0:
        max_val = np.iinfo(np.int16).max if np.max(np.abs(data)) > 1.0 else 1.0
        # Determine bit depth from original range
        abs_max = np.max(np.abs(data))
        if abs_max > 2**23:
            max_val = 2**31 - 1  # 32-bit int
        elif abs_max > 2**15:
            max_val = 2**23 - 1  # 24-bit int
        elif abs_max > 1.0:
            max_val = 2**15 - 1  # 16-bit int
        else:
            max_val = 1.0
        data = data / max_val

    # Convert to mono by averaging channels
    if data.ndim > 1:
        data = np.mean(data, axis=1)

    # Resample if sample rates differ
    if orig_sr != sr:
        num_samples = int(len(data) * sr / orig_sr)
        data = resample(data, num_samples)

    return data, sr
```

## BPM Detection

```python
def detect_bpm(signal, sr=44100):
    """Detect tempo via onset strength autocorrelation.
    Returns (bpm, confidence) where confidence is 0-1.
    Uses spectral flux onset detection and autocorrelation peak-picking.
    """
    # --- Onset strength envelope ---
    n_fft = 2048
    hop = 512
    # Compute STFT magnitudes
    num_frames = 1 + (len(signal) - n_fft) // hop
    if num_frames < 2:
        return 120.0, 0.0  # signal too short, return default

    window = np.hanning(n_fft)
    magnitudes = np.zeros((n_fft // 2 + 1, num_frames))
    for i in range(num_frames):
        start = i * hop
        frame = signal[start:start + n_fft] * window
        spectrum = np.abs(np.fft.rfft(frame))
        magnitudes[:, i] = spectrum

    # Spectral flux: half-wave rectified difference between consecutive frames
    flux = np.zeros(num_frames)
    for i in range(1, num_frames):
        diff = magnitudes[:, i] - magnitudes[:, i - 1]
        flux[i] = np.sum(np.maximum(diff, 0.0))

    # Normalize onset envelope
    if np.max(flux) > 0:
        flux = flux / np.max(flux)

    # --- Autocorrelation ---
    # BPM range 60-200 -> lag range in onset frames
    frame_rate = sr / hop  # frames per second
    min_lag = int(frame_rate * 60.0 / 200.0)  # lag for 200 BPM
    max_lag = int(frame_rate * 60.0 / 60.0)   # lag for 60 BPM
    max_lag = min(max_lag, len(flux) - 1)

    if min_lag >= max_lag or max_lag < 1:
        return 120.0, 0.0

    # Compute autocorrelation for the relevant lag range
    autocorr = np.zeros(max_lag + 1)
    n = len(flux)
    for lag in range(min_lag, max_lag + 1):
        autocorr[lag] = np.sum(flux[:n - lag] * flux[lag:n])

    # Normalize by zero-lag (energy)
    zero_lag = np.sum(flux * flux)
    if zero_lag > 0:
        autocorr = autocorr / zero_lag

    # Find peaks in the autocorrelation
    search = autocorr[min_lag:max_lag + 1]
    if len(search) < 3:
        return 120.0, 0.0

    # Simple peak picking: find local maxima
    peaks = []
    for i in range(1, len(search) - 1):
        if search[i] > search[i - 1] and search[i] > search[i + 1]:
            lag = i + min_lag
            bpm = 60.0 * frame_rate / lag
            peaks.append((search[i], bpm, lag))

    if not peaks:
        # Fallback: take the maximum value in the range
        best_idx = np.argmax(search)
        lag = best_idx + min_lag
        bpm = 60.0 * frame_rate / lag
        confidence = float(search[best_idx])
        return round(bpm, 1), min(confidence, 1.0)

    # Sort by autocorrelation strength
    peaks.sort(key=lambda x: x[0], reverse=True)
    best_corr, best_bpm, best_lag = peaks[0]

    # Half-time / double-time check: if a peak at 2x BPM is nearly as strong,
    # prefer the higher tempo (more likely the true beat rate)
    for corr, bpm, lag in peaks[1:]:
        ratio = bpm / best_bpm
        if 1.9 < ratio < 2.1 and corr > best_corr * 0.8:
            best_bpm = bpm
            best_corr = corr
            break
        if 0.48 < ratio < 0.52 and corr > best_corr * 0.8:
            # The best is already double; keep it
            break

    confidence = float(min(best_corr, 1.0))
    return round(best_bpm, 1), round(confidence, 3)
```

## Key Detection

```python
# Krumhansl-Schmuckler key profiles (Krumhansl 1990)
# Index 0 = C, 1 = C#, ..., 11 = B (relative to the tonic)
MAJOR_PROFILE = np.array([
    6.35, 2.23, 3.48, 2.33, 4.38, 4.09,
    2.52, 5.19, 2.39, 3.66, 2.29, 2.88
])
MINOR_PROFILE = np.array([
    6.33, 2.68, 3.52, 5.38, 2.60, 3.53,
    2.54, 4.75, 3.98, 2.69, 3.34, 3.17
])

PITCH_NAMES = ['C', 'C#', 'D', 'D#', 'E', 'F',
               'F#', 'G', 'G#', 'A', 'A#', 'B']


def _compute_chromagram(signal, sr=44100, n_fft=4096, hop=2048):
    """Compute a 12-bin chromagram from the signal's STFT.
    Maps each FFT bin to one of 12 pitch classes, then normalizes.
    """
    num_frames = 1 + (len(signal) - n_fft) // hop
    if num_frames < 1:
        return np.ones(12) / 12.0  # uniform fallback

    window = np.hanning(n_fft)
    n_bins = n_fft // 2 + 1
    freqs = np.fft.rfftfreq(n_fft, d=1.0 / sr)

    # Precompute bin-to-chroma mapping
    # Map each frequency bin to the nearest pitch class (ignore DC and very low bins)
    bin_to_chroma = np.full(n_bins, -1, dtype=int)
    for b in range(1, n_bins):
        f = freqs[b]
        if f < 27.5 or f > 5000.0:
            continue  # skip sub-bass and very high frequencies
        # MIDI note number (A4=440Hz=69)
        midi = 69 + 12 * np.log2(f / 440.0)
        chroma_idx = int(round(midi)) % 12
        bin_to_chroma[b] = chroma_idx

    chroma = np.zeros(12)
    for i in range(num_frames):
        start = i * hop
        frame = signal[start:start + n_fft] * window
        spectrum = np.abs(np.fft.rfft(frame)) ** 2  # power spectrum
        for b in range(n_bins):
            if bin_to_chroma[b] >= 0:
                chroma[bin_to_chroma[b]] += spectrum[b]

    # Normalize
    total = np.sum(chroma)
    if total > 0:
        chroma = chroma / total
    return chroma


def detect_key(signal, sr=44100):
    """Detect musical key using the Krumhansl-Schmuckler algorithm.
    Returns (key_name, mode, correlation) e.g. ('A', 'minor', 0.82).
    """
    chroma = _compute_chromagram(signal, sr)

    best_key = 'C'
    best_mode = 'major'
    best_corr = -1.0

    for shift in range(12):
        # Rotate chroma so that index 0 aligns with the candidate tonic
        rotated = np.roll(chroma, -shift)

        # Correlate with major profile
        corr_maj = np.corrcoef(rotated, MAJOR_PROFILE)[0, 1]
        if corr_maj > best_corr:
            best_corr = corr_maj
            best_key = PITCH_NAMES[shift]
            best_mode = 'major'

        # Correlate with minor profile
        corr_min = np.corrcoef(rotated, MINOR_PROFILE)[0, 1]
        if corr_min > best_corr:
            best_corr = corr_min
            best_key = PITCH_NAMES[shift]
            best_mode = 'minor'

    return best_key, best_mode, round(float(best_corr), 3)
```

## Spectral Profile

```python
def spectral_profile(signal, sr=44100):
    """Analyze spectral energy distribution across 5 frequency bands.
    Returns dict with band_energies (dB), centroid_hz, rolloff_hz.
    """
    bands = {
        'sub':  (20, 60),
        'low':  (60, 250),
        'mid':  (250, 4000),
        'high': (4000, 12000),
        'air':  (12000, 20000),
    }
    nyquist = sr / 2.0
    band_energies = {}

    for name, (lo, hi) in bands.items():
        # Clamp to Nyquist and ensure valid range
        lo_n = max(lo / nyquist, 0.001)
        hi_n = min(hi / nyquist, 0.999)
        if lo_n >= hi_n:
            band_energies[name] = -120.0
            continue
        sos = butter(4, [lo_n, hi_n], btype='bandpass', output='sos')
        filtered = sosfilt(sos, signal)
        rms = np.sqrt(np.mean(filtered ** 2))
        band_energies[name] = round(20 * np.log10(rms + 1e-12), 1)

    # Spectral centroid from STFT
    n_fft = 2048
    hop = 512
    num_frames = 1 + (len(signal) - n_fft) // hop
    freqs = np.fft.rfftfreq(n_fft, d=1.0 / sr)

    centroid_sum = 0.0
    rolloff_sum = 0.0
    window = np.hanning(n_fft)

    for i in range(max(num_frames, 1)):
        start = i * hop
        frame = signal[start:start + n_fft]
        if len(frame) < n_fft:
            frame = np.pad(frame, (0, n_fft - len(frame)))
        mag = np.abs(np.fft.rfft(frame * window))
        total_energy = np.sum(mag)
        if total_energy > 0:
            centroid_sum += np.sum(freqs * mag) / total_energy
            # Rolloff: frequency below which 95% of energy is concentrated
            cumsum = np.cumsum(mag)
            rolloff_idx = np.searchsorted(cumsum, 0.95 * total_energy)
            rolloff_idx = min(rolloff_idx, len(freqs) - 1)
            rolloff_sum += freqs[rolloff_idx]

    n = max(num_frames, 1)
    return {
        'band_energies': band_energies,
        'centroid_hz': round(centroid_sum / n, 1),
        'rolloff_hz': round(rolloff_sum / n, 1),
    }
```

## Loudness Measurement

```python
def measure_loudness(signal, sr=44100):
    """Measure peak, RMS, estimated LUFS, and dynamic range.
    LUFS estimation uses a simplified K-weighting filter and gating.
    """
    # Peak level
    peak = np.max(np.abs(signal))
    peak_db = round(20 * np.log10(peak + 1e-12), 1)

    # RMS level
    rms = np.sqrt(np.mean(signal ** 2))
    rms_db = round(20 * np.log10(rms + 1e-12), 1)

    # Simplified LUFS: K-weighted loudness with absolute gating
    # K-weighting stage 1: high shelf at ~1500 Hz (+4 dB)
    nyquist = sr / 2.0
    b_shelf, a_shelf = _k_weight_shelf(sr)
    weighted = np.convolve(signal, b_shelf, mode='same')
    # K-weighting stage 2: high-pass at ~38 Hz
    sos_hp = butter(2, 38.0 / nyquist, btype='highpass', output='sos')
    weighted = sosfilt(sos_hp, weighted)

    # Gate: split into 400ms blocks with 75% overlap
    block_len = int(0.4 * sr)
    hop_gate = block_len // 4
    block_loudness = []
    for start in range(0, len(weighted) - block_len + 1, hop_gate):
        block = weighted[start:start + block_len]
        bl = np.mean(block ** 2)
        if bl > 0:
            block_loudness.append(bl)

    if not block_loudness:
        return {'peak_db': peak_db, 'rms_db': rms_db,
                'lufs_estimate': -70.0, 'dynamic_range_db': 0.0}

    block_loudness = np.array(block_loudness)
    # Absolute gate at -70 LUFS
    abs_gate = 10 ** (-70 / 10)
    gated = block_loudness[block_loudness > abs_gate]
    if len(gated) == 0:
        lufs = -70.0
    else:
        # Relative gate: -10 dB below ungated mean
        ungated_mean = np.mean(gated)
        rel_gate = ungated_mean * 10 ** (-10 / 10)
        final = gated[gated > rel_gate]
        if len(final) == 0:
            final = gated
        lufs = round(-0.691 + 10 * np.log10(np.mean(final) + 1e-12), 1)

    # Dynamic range: difference between 95th and 5th percentile block loudness
    all_db = 10 * np.log10(block_loudness + 1e-12)
    dynamic_range = round(float(np.percentile(all_db, 95) - np.percentile(all_db, 5)), 1)

    return {
        'peak_db': peak_db,
        'rms_db': rms_db,
        'lufs_estimate': lufs,
        'dynamic_range_db': dynamic_range,
    }


def _k_weight_shelf(sr):
    """Approximate K-weighting high-shelf filter as a simple FIR.
    Boosts frequencies above ~1500 Hz by ~4 dB.
    """
    # Simple 3-tap FIR approximation of the high-shelf boost
    # This is a pragmatic approximation; exact ITU-R BS.1770 uses biquads
    fc = 1500.0 / (sr / 2.0)
    gain = 10 ** (4.0 / 20.0)  # +4 dB
    # Create a short highpass kernel and scale it
    n_taps = 65
    t = np.arange(n_taps) - n_taps // 2
    t[n_taps // 2] = 1e-10  # avoid division by zero
    lp = np.sin(2 * np.pi * fc * t / 2) / (np.pi * t)
    lp[n_taps // 2] = fc
    lp *= np.hanning(n_taps)
    lp /= np.sum(lp)
    hp = -lp
    hp[n_taps // 2] += 1.0
    # Shelf = original + (gain-1) * highpass
    shelf = np.zeros(n_taps)
    shelf[n_taps // 2] = 1.0
    shelf += (gain - 1.0) * hp
    return shelf, np.array([1.0])
```

## Section Detection

```python
def detect_sections(signal, sr=44100, min_section_sec=8):
    """Detect structural sections based on energy and spectral changes.
    Returns list of dicts: [{start_sec, end_sec, duration_sec, energy, brightness, label}].
    Energy is on a 1-10 scale; sections are auto-labeled heuristically.
    """
    # Compute RMS and spectral centroid in overlapping windows
    win_sec = 2.0
    hop_sec = 0.5
    win_samples = int(win_sec * sr)
    hop_samples = int(hop_sec * sr)

    rms_values = []
    centroid_values = []
    times = []

    n_fft = 2048
    freqs = np.fft.rfftfreq(n_fft, d=1.0 / sr)

    for start in range(0, len(signal) - win_samples + 1, hop_samples):
        chunk = signal[start:start + win_samples]
        times.append((start + win_samples / 2) / sr)

        # RMS
        rms_values.append(np.sqrt(np.mean(chunk ** 2)))

        # Average spectral centroid over the chunk
        n_sub = max(1, len(chunk) // n_fft)
        c_sum = 0.0
        for j in range(n_sub):
            s = j * n_fft
            frame = chunk[s:s + n_fft]
            if len(frame) < n_fft:
                frame = np.pad(frame, (0, n_fft - len(frame)))
            mag = np.abs(np.fft.rfft(frame * np.hanning(n_fft)))
            total = np.sum(mag)
            if total > 0:
                c_sum += np.sum(freqs * mag) / total
        centroid_values.append(c_sum / n_sub)

    if len(rms_values) < 3:
        duration = len(signal) / sr
        return [{'start_sec': 0.0, 'end_sec': round(duration, 2),
                 'duration_sec': round(duration, 2),
                 'energy': 5, 'brightness': 5, 'label': 'full'}]

    rms_arr = np.array(rms_values)
    cent_arr = np.array(centroid_values)

    # Normalize both features to [0, 1]
    def norm(x):
        mn, mx = np.min(x), np.max(x)
        return (x - mn) / (mx - mn + 1e-12)

    rms_norm = norm(rms_arr)
    cent_norm = norm(cent_arr)

    # Novelty function: absolute change in normalized RMS + spectral centroid
    novelty = np.zeros(len(rms_arr))
    for i in range(1, len(rms_arr)):
        novelty[i] = abs(rms_norm[i] - rms_norm[i - 1]) + \
                      0.5 * abs(cent_norm[i] - cent_norm[i - 1])

    # Smooth novelty with a short moving average
    kernel_size = max(1, int(1.0 / hop_sec))  # ~1 second kernel
    if kernel_size > 1:
        kernel = np.ones(kernel_size) / kernel_size
        novelty = np.convolve(novelty, kernel, mode='same')

    # Peak-pick: find peaks with minimum distance enforced
    min_dist_frames = int(min_section_sec / hop_sec)
    threshold = np.mean(novelty) + 0.5 * np.std(novelty)

    boundaries = [0]  # always start at the beginning
    last_peak = -min_dist_frames
    for i in range(1, len(novelty) - 1):
        if (novelty[i] > novelty[i - 1] and
                novelty[i] > novelty[i + 1] and
                novelty[i] > threshold and
                i - last_peak >= min_dist_frames):
            boundaries.append(i)
            last_peak = i

    # Build section list
    total_duration = len(signal) / sr
    boundary_times = [times[b] - win_sec / 2 for b in boundaries]
    boundary_times[0] = 0.0  # ensure we start at 0
    boundary_times.append(total_duration)

    sections = []
    for k in range(len(boundary_times) - 1):
        t0 = boundary_times[k]
        t1 = boundary_times[k + 1]
        # Get average RMS and centroid for this section
        frames_in_section = [i for i, t in enumerate(times) if t0 <= t < t1]
        if not frames_in_section:
            frames_in_section = [boundaries[k] if k < len(boundaries) else 0]
        avg_rms = np.mean(rms_arr[frames_in_section])
        avg_cent = np.mean(cent_arr[frames_in_section])
        sections.append({
            'start_sec': round(t0, 2),
            'end_sec': round(t1, 2),
            'duration_sec': round(t1 - t0, 2),
            '_rms': avg_rms,
            '_centroid': avg_cent,
        })

    # Compute energy (1-10) based on RMS relative to track range
    all_rms = [s['_rms'] for s in sections]
    rms_min, rms_max = min(all_rms), max(all_rms)
    all_cent = [s['_centroid'] for s in sections]
    cent_min, cent_max = min(all_cent), max(all_cent)

    for s in sections:
        rel_rms = (s['_rms'] - rms_min) / (rms_max - rms_min + 1e-12)
        s['energy'] = max(1, min(10, round(1 + 9 * rel_rms)))
        rel_cent = (s['_centroid'] - cent_min) / (cent_max - cent_min + 1e-12)
        s['brightness'] = max(1, min(10, round(1 + 9 * rel_cent)))
        del s['_rms']
        del s['_centroid']

    # Auto-label sections heuristically
    if len(sections) == 1:
        sections[0]['label'] = 'full'
    else:
        energies = [s['energy'] for s in sections]
        max_e = max(energies)
        min_e = min(energies)
        for i, s in enumerate(sections):
            if i == 0 and s['energy'] <= min_e + 2:
                s['label'] = 'intro'
            elif i == len(sections) - 1 and s['energy'] <= min_e + 2:
                s['label'] = 'outro'
            elif s['energy'] >= max_e - 1:
                s['label'] = 'chorus'
            elif s['energy'] <= min_e + 1:
                s['label'] = 'breakdown'
            else:
                s['label'] = 'verse'

    return sections
```

## Full Analysis Pipeline

```python
def analyze_reference(filename, sr=44100):
    """Run all analysis steps on a reference track and print a formatted report.
    Returns a dict with all results for feeding into the energy map.
    """
    print(f"Loading {filename}...")
    signal, sr = load_audio(filename, sr)
    duration = len(signal) / sr

    print("Detecting BPM...")
    bpm, bpm_conf = detect_bpm(signal, sr)

    print("Detecting key...")
    key, mode, key_corr = detect_key(signal, sr)

    print("Analyzing spectrum...")
    spectrum = spectral_profile(signal, sr)

    print("Measuring loudness...")
    loudness = measure_loudness(signal, sr)

    print("Detecting sections...")
    sections = detect_sections(signal, sr)

    # --- Formatted report ---
    print()
    print(f"Reference Track Analysis: {filename}")
    print("─" * (25 + len(filename)))
    print(f"  Duration:    {int(duration // 60)}:{int(duration % 60):02d}")
    print(f"  BPM:         {bpm} (confidence: {bpm_conf})")
    print(f"  Key:         {key} {mode} (correlation: {key_corr})")
    print(f"  Loudness:    {loudness['lufs_estimate']} LUFS "
          f"(peak: {loudness['peak_db']} dBFS)")
    print(f"  RMS:         {loudness['rms_db']} dBFS")
    print(f"  Dyn. range:  {loudness['dynamic_range_db']} dB")
    print(f"  Brightness:  centroid {spectrum['centroid_hz']} Hz, "
          f"rolloff {spectrum['rolloff_hz']} Hz")
    print()
    print("  Spectral bands:")
    for band, db in spectrum['band_energies'].items():
        print(f"    {band:>4s}: {db:>7.1f} dB")
    print()
    print("  Sections detected:")
    for s in sections:
        t0 = s['start_sec']
        t1 = s['end_sec']
        m0, s0 = int(t0 // 60), int(t0 % 60)
        m1, s1 = int(t1 // 60), int(t1 % 60)
        bar_filled = s['energy']
        bar_empty = 10 - bar_filled
        bar = "█" * bar_filled + "░" * bar_empty
        print(f"    {m0}:{s0:02d}-{m1}:{s1:02d}  "
              f"{s['label']:<12s} {bar}  energy: {s['energy']}")
    print()

    results = {
        'filename': filename,
        'duration_sec': round(duration, 2),
        'bpm': bpm,
        'bpm_confidence': bpm_conf,
        'key': key,
        'mode': mode,
        'key_correlation': key_corr,
        'spectral': spectrum,
        'loudness': loudness,
        'sections': sections,
    }
    return results
```

## Integration with Workflow

After running `analyze_reference()`, map the extracted parameters into the composition constants and energy map:

```python
def apply_reference_to_config(analysis):
    """Convert reference analysis results into composition parameters.
    Returns a config dict ready to drive the generation pipeline.
    """
    # BPM: round to nearest integer
    bpm = int(round(analysis['bpm']))

    # Key and scale
    root = analysis['key']
    scale_type = analysis['mode']  # 'major' or 'minor'

    # EQ target: use spectral band energies to set mix balance
    eq_target = analysis['spectral']['band_energies']

    # Energy map: translate sections into the standard energy map format
    energy_map = []
    for s in analysis['sections']:
        energy_map.append({
            'label': s['label'],
            'start_sec': s['start_sec'],
            'end_sec': s['end_sec'],
            'duration_sec': s['duration_sec'],
            'intensity': s['energy'],
            'brightness': s['brightness'],
        })

    # Mastering target: use loudness measurements
    master_target = {
        'target_lufs': analysis['loudness']['lufs_estimate'],
        'target_peak': analysis['loudness']['peak_db'],
        'dynamic_range': analysis['loudness']['dynamic_range_db'],
    }

    return {
        'BPM': bpm,
        'ROOT': root,
        'SCALE': scale_type,
        'EQ_TARGET': eq_target,
        'ENERGY_MAP': energy_map,
        'MASTER_TARGET': master_target,
        'reference_brightness_hz': analysis['spectral']['centroid_hz'],
    }
```

Usage summary:

- BPM from `detect_bpm` becomes the `BPM` constant
- Key and mode from `detect_key` become `ROOT` and `SCALE`
- Spectral profile from `spectral_profile` sets EQ targets to match the reference tonal balance
- Section structure from `detect_sections` provides the basis for the energy map: match the section count, durations, and relative energy levels
- Loudness from `measure_loudness` sets the mastering target (LUFS, peak ceiling, dynamic range)
