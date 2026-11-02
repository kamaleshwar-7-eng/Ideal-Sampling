# IDEAL-NATURAL-AND-FLAT-TOP-SAMPLING

## AIM:
To write a Python program for the construction and reconstruction of ideal, natural, and flat-top sampling techniques and observe their corresponding waveforms.

## TOOLS REQUIRED:

### SOFTWARE:
- Python 3.x
- Google Colab / Jupyter Notebook
- NumPy
- Matplotlib
- SciPy

## THEORY:

### 1. Ideal Sampling
Ideal sampling is the process of multiplying a continuous-time signal with an impulse train. The sampled signal consists of equally spaced impulses whose amplitudes correspond to the instantaneous values of the original signal.

### 2. Natural Sampling
Natural sampling is performed by multiplying the input signal with a periodic rectangular pulse train. The tops of the sampled pulses follow the shape of the original signal during the sampling interval.

### 3. Flat-Top Sampling
Flat-top sampling is a sampling technique in which the sampled signal is held constant during each sampling interval. A sample-and-hold circuit is used to maintain the sampled amplitude until the next sampling instant.

### 4. Reconstruction
The original signal can be reconstructed from its samples using a low-pass reconstruction filter. Proper reconstruction requires the sampling frequency to satisfy the Nyquist sampling criterion.

## PROGRAM:
```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, filtfilt

# ==================================================
# COMMON PARAMETERS
# ==================================================
fs = 1000
T = 1
t = np.arange(0, T, 1/fs)

fm = 5
message_signal = np.sin(2 * np.pi * fm * t)

pulse_rate = 50
sample_interval = int(fs / pulse_rate)
pulse_width_samples = int(fs / (2 * pulse_rate))

sample_indices = np.arange(0, len(t), sample_interval)


# ==================================================
# LOW PASS FILTER
# ==================================================
def lowpass_filter(signal, cutoff, fs, order=5):
    nyquist = fs / 2
    b, a = butter(order, cutoff / nyquist, btype="low")
    return filtfilt(b, a, signal)


# ==================================================
# 1. IDEAL (IMPULSE) SAMPLING
# ==================================================
ideal_signal = np.zeros_like(t)

ideal_signal[sample_indices] = message_signal[sample_indices]

# Reconstruction
ideal_reconstructed = np.interp(
    t, t[sample_indices], message_signal[sample_indices]
)

ideal_reconstructed = lowpass_filter(
    ideal_reconstructed, 10, fs
)

plt.figure(figsize=(12, 8))

plt.subplot(3, 1, 1)
plt.plot(t, message_signal, label="Original Signal")
plt.title("Original Message Signal")
plt.ylabel("Amplitude")
plt.grid(True)
plt.legend()

plt.subplot(3, 1, 2)
plt.stem(
    t[sample_indices],
    message_signal[sample_indices],
    linefmt="r-",
    markerfmt="ro",
    basefmt=" "
)
plt.title("Ideal (Impulse) Sampling")
plt.ylabel("Amplitude")
plt.grid(True)

plt.subplot(3, 1, 3)
plt.plot(t, message_signal, label="Original Signal")
plt.plot(t, ideal_reconstructed, "--",
         label="Reconstructed Signal")
plt.title("Ideal Sampling Reconstruction")
plt.xlabel("Time (s)")
plt.ylabel("Amplitude")
plt.legend()
plt.grid(True)

plt.tight_layout()
plt.show()


# ==================================================
# 2. NATURAL SAMPLING
# ==================================================
pulse_train = np.zeros_like(t)

for index in sample_indices:
    end_index = min(index + pulse_width_samples, len(t))
    pulse_train[index:end_index] = 1

natural_signal = message_signal * pulse_train

natural_reconstructed = lowpass_filter(
    natural_signal, 10, fs
)

plt.figure(figsize=(12, 8))

plt.subplot(3, 1, 1)
plt.plot(t, message_signal)
plt.title("Original Message Signal")
plt.ylabel("Amplitude")
plt.grid(True)

plt.subplot(3, 1, 2)
plt.plot(t, pulse_train)
plt.title("Rectangular Pulse Train")
plt.ylabel("Amplitude")
plt.grid(True)

plt.subplot(3, 1, 3)
plt.plot(t, natural_signal)
plt.title("Natural Sampling")
plt.xlabel("Time (s)")
plt.ylabel("Amplitude")
plt.grid(True)

plt.tight_layout()
plt.show()

# Reconstruction
plt.figure(figsize=(12, 4))

plt.plot(t, message_signal,
         label="Original Signal", alpha=0.7)

plt.plot(t, natural_reconstructed,
         "--", label="Reconstructed Signal")

plt.title("Natural Sampling Reconstruction")
plt.xlabel("Time (s)")
plt.ylabel("Amplitude")
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()


# ==================================================
# 3. FLAT-TOP SAMPLING
# ==================================================
flat_top_signal = np.zeros_like(t)

for index in sample_indices:
    sample_value = message_signal[index]
    end_index = min(index + pulse_width_samples, len(t))

    flat_top_signal[index:end_index] = sample_value

flat_top_reconstructed = lowpass_filter(
    flat_top_signal, 10, fs
)

plt.figure(figsize=(12, 8))

plt.subplot(3, 1, 1)
plt.plot(t, message_signal)
plt.title("Original Message Signal")
plt.ylabel("Amplitude")
plt.grid(True)

plt.subplot(3, 1, 2)
plt.stem(
    t[sample_indices],
    message_signal[sample_indices],
    basefmt=" "
)
plt.title("Sampling Instances")
plt.ylabel("Amplitude")
plt.grid(True)

plt.subplot(3, 1, 3)
plt.plot(t, flat_top_signal)
plt.title("Flat-Top Sampling")
plt.xlabel("Time (s)")
plt.ylabel("Amplitude")
plt.grid(True)

plt.tight_layout()
plt.show()

# Reconstruction
plt.figure(figsize=(12, 4))

plt.plot(t, message_signal,
         label="Original Signal", alpha=0.7)

plt.plot(t, flat_top_reconstructed,
         "--", label="Reconstructed Signal")

plt.title("Flat-Top Sampling Reconstruction")
plt.xlabel("Time (s)")
plt.ylabel("Amplitude")
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()


# ==================================================
# COMPARISON OF THREE SAMPLING TECHNIQUES
# ==================================================
plt.figure(figsize=(12, 10))

plt.subplot(3, 1, 1)
plt.stem(t[sample_indices],
         message_signal[sample_indices],
         basefmt=" ")
plt.title("1. Ideal (Impulse) Sampling")
plt.ylabel("Amplitude")
plt.grid(True)

plt.subplot(3, 1, 2)
plt.plot(t, natural_signal)
plt.title("2. Natural Sampling")
plt.ylabel("Amplitude")
plt.grid(True)

plt.subplot(3, 1, 3)
plt.plot(t, flat_top_signal)
plt.title("3. Flat-Top Sampling")
plt.xlabel("Time (s)")
plt.ylabel("Amplitude")
plt.grid(True)

plt.tight_layout()
plt.show()

```   
## OUTPUT WAVEFORM:

<img width="1201" height="985" alt="image" src="https://github.com/user-attachments/assets/6fd9f251-57a2-4a65-9df2-856375a706c0" />

Link To Full Screen Output:
  https://colab.research.google.com/drive/11EATzsEOVC2yKRQjYSSki8M_f8E53Rem#scrollTo=0H3sPd06Eug1&fullscreenOutput=true
## RESULTS:

Thus, ideal, natural, and flat-top sampling techniques were successfully implemented using Python. The corresponding sampling waveforms were generated, and the original signal was reconstructed using sinc interpolation.
