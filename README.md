# Signal Acquisition & Frequency Analysis (TC1)

A small Python toolkit for simulating pin signal acquisition, computing the FFT of a time-domain signal, and estimating the average frequency of a periodic signal via threshold-crossing detection.

## Overview

This project demonstrates a typical signal-processing pipeline:

1. **Acquire** a simulated signal from a virtual pin ([acquire_pin.py](acquire_pin.py)).
2. **Analyze** the signal in the frequency domain using an FFT ([compute_fft.py](compute_fft.py)).
3. **Measure** the average frequency directly in the time domain using rising-edge detection ([find_average_frequency.py](find_average_frequency.py)).

## Modules

### `acquire_pin.py`

`acquire_pin(pin_number, sampling_period)` generates a synthetic time-domain signal for a given virtual pin:

- **Pin 1**: Sum of a 50 Hz and a 120 Hz sine wave.
- **Pin 2**: A 250 Hz sine wave.
- **Other**: A 600 Hz sine wave.

Returns a 1-second array of samples spaced by `sampling_period` seconds.

### `compute_fft.py`

`compute_fft(samples, sampling_period)` computes the single-sided FFT of a real-valued signal.

Returns:
- `freqs`: frequency bins in Hz (0 .. Nyquist).
- `spectrum`: complex FFT coefficients, amplitude-scaled so the magnitude directly reflects each sinusoid's amplitude.

### `find_average_frequency.py`

`average_frequency(samples, sampling_period, threshold, hysteresis=0.0, interpolate=True)` detects rising-edge crossings of a threshold and computes the average frequency over the span of detected edges.

Returns a dictionary containing:
- `frequency`, `period` — the estimated average frequency (Hz) and period (s).
- `edge_times`, `edge_indices` — crossing times and sample indices.
- `cycles`, `periods` — number of measured cycles and their individual periods.
- `jitter` — standard deviation of the measured periods.
- `span` — trimmed index range used for the measurement.

Optional `hysteresis` suppresses retriggering on noisy signals, and `interpolate` linearly interpolates the sub-sample crossing time for higher precision.

## Requirements

- Python 3.8+
- [NumPy](https://numpy.org/)
- [Matplotlib](https://matplotlib.org/) (used by `acquire_pin.py`)

Install dependencies:

```bash
pip install numpy matplotlib
```

## Usage

```python
from acquire_pin import acquire_pin
from compute_fft import compute_fft
from find_average_frequency import average_frequency

sampling_period = 1 / 1000  # 1 kHz sampling rate

signal = acquire_pin(pin_number=1, sampling_period=sampling_period)

freqs, spectrum = compute_fft(signal, sampling_period)

result = average_frequency(signal, sampling_period, threshold=0.0)
print(f"Average frequency: {result['frequency']:.2f} Hz")
```

## License

This project is provided as-is for demonstration purposes.
