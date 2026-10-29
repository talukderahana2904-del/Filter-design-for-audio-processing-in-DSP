# Microphone-Based Audio Acquisition and Filtering Using an 8th-Order Butterworth Filter

**ALSA Audio Capture | Analog Signal Conditioning | Octave-Band Analysis | MATLAB Signal Processing**

![Filter](https://img.shields.io/badge/Filter-8th--Order%20Butterworth-blue)
![Audio](https://img.shields.io/badge/Audio-ALSA%20Capture-green)
![Analysis](https://img.shields.io/badge/Analysis-MATLAB-orange)
![Signal Processing](https://img.shields.io/badge/Signal%20Processing-Octave%20Bands-purple)

---

## 1. Project Overview

This project focuses on microphone-based audio acquisition, analog signal conditioning, and frequency-domain analysis using an **8th-order Butterworth band-pass filter**. The system combines audio capture through the Advanced Linux Sound Architecture (ALSA), microphone input, hardware-based filtering, and MATLAB-based signal analysis to investigate the frequency characteristics of captured audio.

The analog filter is designed to attenuate unwanted low-frequency and high-frequency components while preserving signals within the intended passband. The project also explores the relationship between frequency-selective filtering and **octave-band analysis**, a fundamental technique in acoustic measurement, noise characterization, and audio signal processing.

MATLAB is used to evaluate the filter response, perform frequency-domain calculations, analyse audio signals, and interpret magnitude spectra using decibel (dB) scaling. Together, these stages demonstrate the relationship between analog electronics, Linux-based audio acquisition, and digital signal analysis.

## 2. Objectives

- Design and evaluate an 8th-order Butterworth band-pass filter for audio signal conditioning.
- Capture microphone audio using ALSA on a Linux-based system.
- Investigate the attenuation of unwanted frequency components through analog filtering.
- Relate the filter's frequency response to octave-band theory and acoustic frequency analysis.
- Use MATLAB to perform signal analysis, frequency-response calculations, and spectral visualisation.
- Evaluate signal magnitude in decibels and compare frequency-domain characteristics before and after filtering.
- Understand the practical interaction between microphone acquisition, analog filtering, sampling, and digital signal processing.

## 3. System Architecture

The project follows a signal-processing workflow that connects acoustic input to frequency-domain evaluation.

```text
          Acoustic Environment
                   |
                   v
             Microphone
                   |
                   v
        Analog Audio Signal
                   |
                   v
       8th-Order Butterworth
          Band-Pass Filter
                   |
                   v
       Filtered Audio Signal
                   |
             +-----+------+
             |            |
             v            v
      Audio Acquisition   Analog Response
       and ALSA Setup      Verification
             |            |
             v            v
      Captured Samples   SPICE Analysis
             |
             v
       MATLAB Analysis
             |
       +-----+---------+
       |               |
       v               v
  Frequency Spectrum  dB Analysis
       |
       v
  Octave-Band Interpretation
```

**Implementation note:** ALSA provides the Linux audio capture interface; it does not itself implement the analog filter. The microphone, analog filter, and capture device must be connected in a way that supports the intended measurement. The diagram represents the functional workflow, with the precise hardware and capture arrangement determined by the implementation.

## 4. Technical Specifications

| Parameter | Description |
|---|---|
| Filter response | Butterworth |
| Filter order | 8th order |
| Filter topology | Active Sallen–Key |
| Lower cutoff frequency | 500 Hz |
| Upper cutoff frequency | 50 kHz |
| Filter implementation | Cascaded high-pass and low-pass sections |
| Audio input | Microphone |
| Audio capture interface | ALSA (Advanced Linux Sound Architecture) |
| Analysis environment | MATLAB |
| Circuit verification | SPICE-based AC analysis |
| Spectral analysis | Frequency-domain and FFT analysis |
| Magnitude representation | Decibel (dB) scale |
| Acoustic interpretation | Octave-band frequency analysis |

*The filter specifications describe the original circuit design. The usable frequency range of the complete audio system is also constrained by the microphone, capture hardware, sampling rate, and analog front-end.*

## 5. Eighth-Order Butterworth Filter Design

### 5.1 Design rationale

A Butterworth filter is characterised by a maximally flat magnitude response in its passband. This makes it useful when the objective is to preserve the relative amplitudes of in-band frequency components without introducing passband ripple.

The eighth-order band-pass response is realised by cascading:

- A fourth-order high-pass filter to attenuate low-frequency components below the lower cutoff.
- A fourth-order low-pass filter to attenuate high-frequency components above the upper cutoff.

Each fourth-order section consists of two second-order Sallen–Key stages, resulting in four active filter stages overall.

The intended passband extends from 500 Hz to 50 kHz. The design therefore provides frequency-selective conditioning over a wide frequency range, subject to the actual circuit response and component tolerances.

### 5.2 Key design calculations

The centre frequency is defined using the geometric mean of the cutoff frequencies:

\[
f_0=\sqrt{f_L f_H}
\]

For \(f_L=500\,\text{Hz}\) and \(f_H=50{,}000\,\text{Hz}\):

\[
f_0=\sqrt{500\times50{,}000}
=5{,}000\,\text{Hz}
\]

The bandwidth is:

\[
BW=f_H-f_L
\]

\[
BW=50{,}000-500=49{,}500\,\text{Hz}
\]

The ratio of the cutoff frequencies is:

\[
\frac{f_H}{f_L}=\frac{50{,}000}{500}=100
\]

These calculations establish the frequency range and nominal centre frequency of the filter.

### 5.3 Sallen–Key implementation

The Sallen–Key topology uses operational amplifiers with resistor-capacitor networks to realise second-order active filters. Component values determine the cutoff frequency and quality factor of each stage.

For the standard Sallen–Key configuration, the cutoff-frequency relationship is:

\[
f_c=\frac{1}{2\pi\sqrt{R_1R_2C_1C_2}}
\]

The quality factor \(Q\) depends on the component ratios and amplifier gain. The individual second-order stages are selected to approximate the required Butterworth pole distribution.

The combined response is verified using circuit-level AC analysis, with particular attention to cutoff frequencies, passband gain, roll-off, and phase response.

## 6. Microphone Audio Acquisition Using ALSA

The project uses the **Advanced Linux Sound Architecture (ALSA)**, an open-source Linux audio subsystem, to interface with the audio capture hardware and acquire microphone signals.

ALSA provides access to capture devices and supports configuration of audio parameters such as sample rate, channel count, and sample format. The captured audio can subsequently be processed and analysed in MATLAB.

The acquisition workflow includes:

1. Identifying the available audio capture device.
2. Configuring the microphone input and capture parameters.
3. Recording audio samples through ALSA-compatible tools or applications.
4. Saving the captured signal in a suitable format for analysis.
5. Importing the recorded audio into MATLAB.
6. Evaluating the signal in the time and frequency domains.

The sampling configuration is particularly important because the digital representation must adequately cover the frequency range being investigated.

For a maximum frequency of 50 kHz, the theoretical Nyquist criterion requires:

\[
f_s>2f_{\max}=100\,\text{kHz}
\]

A practical implementation should select a supported sampling rate above this minimum and allow appropriate margin for anti-aliasing and transition-band attenuation. A nominal rate of 200 kHz may be considered where supported by the microphone, capture interface, and acquisition software.

**Important:** Standard consumer audio interfaces often do not support 200 kHz capture. The actual sample rate must be verified from the selected ALSA device. If the hardware supports only conventional audio rates, the usable analysis bandwidth must be restricted accordingly.

The ALSA capture path and the analog-filter output path must also be distinguished. If the objective is to measure the filtered signal, the capture interface must receive the filter output through a compatible input stage rather than merely recording the microphone independently.

## 7. Relevance of Octave-Band Theory in Audio Processing

### 7.1 What is an octave band?

An octave band is a frequency interval in which the upper frequency is twice the lower frequency:

\[
f_H=2f_L
\]

Octave-band analysis is widely used in acoustics, environmental noise assessment, audio measurement, and sound-system evaluation because it groups spectral information into perceptually and physically meaningful frequency ranges.

For example, adjacent octave bands may be represented by intervals such as 250–500 Hz, 500 Hz–1 kHz, 1–2 kHz, and 2–4 kHz.

Unlike a linear-frequency division, octave bands use a logarithmic frequency scale. Each band represents the same frequency ratio rather than the same absolute bandwidth.

### 7.2 How octave-band theory relates to this project

The eighth-order Butterworth filter and octave-band analysis serve complementary purposes.

The **analog filter** determines which frequencies are attenuated or passed by the hardware. Octave-band analysis, on the other hand, provides a framework for grouping and interpreting the frequency content of the captured audio.

This relationship is relevant in several ways:

- **Frequency-selective measurement:** The filter limits the frequency range reaching the downstream measurement stage, subject to the response of the complete system.
- **Acoustic noise analysis:** Frequency components can be grouped into octave bands to understand whether measured sound energy is concentrated in low-, mid-, or high-frequency regions.
- **Spectral interpretation:** MATLAB can be used to calculate band-wise signal levels from the captured audio spectrum, making it easier to compare frequency regions.
- **Filter-response evaluation:** The measured or simulated filter response can be compared with the octave-band boundaries to understand the influence of the filter on different frequency regions.
- **Measurement-system design:** Octave-band concepts help connect the circuit's frequency-selective behaviour with practical acoustic instrumentation and sound-level analysis.

### 7.3 Octave-band calculations

For a band with lower and upper frequencies \(f_1\) and \(f_2\), the octave ratio is:

\[
\frac{f_2}{f_1}=2
\]

The centre frequency of a band is commonly defined as:

\[
f_c=\sqrt{f_1f_2}
\]

For a one-octave band spanning 1 kHz to 2 kHz:

\[
f_c=\sqrt{1000\times2000}
\approx1414.2\,\text{Hz}
\]

This geometric-centre relationship is consistent with logarithmic frequency spacing.

For a captured signal, the energy within a selected band can be estimated by summing the squared magnitudes of the relevant FFT components, with suitable scaling and treatment of windowing, frequency resolution, and spectral leakage.

Octave-band levels may then be expressed relative to a defined reference level. For acoustic sound-pressure measurements, the reference is typically \(20\,\mu\text{Pa}\). For ordinary digital audio samples, the result is instead expressed relative to a digital amplitude or power reference unless a calibrated microphone and measurement chain establish the conversion to physical sound pressure.

**Scope clarification:** The eighth-order Butterworth filter is not automatically an octave-band filter. It is a wide-band analog band-pass filter. True octave-band measurements require separate band-wise analysis or a bank of appropriately designed octave-band filters.

## 8. MATLAB-Based Signal Analysis

MATLAB is used as the computational environment for analysing the filter characteristics and captured audio. Its role is to connect the theoretical circuit design with measurable signal behaviour.

Depending on the implemented analysis scripts, the workflow can include:

### 8.1 Time-domain analysis

The recorded audio signal is inspected in the time domain to examine its amplitude variation, duration, transient behaviour, and potential clipping.

For a discrete-time signal \(x[n]\), the root-mean-square amplitude is:

\[
x_{\mathrm{RMS}}=
\sqrt{\frac{1}{N}\sum_{n=0}^{N-1}x[n]^2}
\]

The RMS value provides a measure of the signal's average magnitude over the selected observation window.

### 8.2 Frequency-domain analysis using FFT

The Fast Fourier Transform (FFT) converts the captured time-domain samples into a frequency-domain representation.

For a sampling rate \(f_s\) and FFT length \(N\), the frequency spacing between bins is:

\[
\Delta f=\frac{f_s}{N}
\]

The frequency corresponding to bin \(k\) is:

\[
f_k=\frac{k f_s}{N}
\]

This frequency mapping allows the spectral content of the captured audio to be examined against the designed filter's passband.

A suitable window, such as the Hann window, can reduce spectral leakage when analysing finite-length audio segments.

### 8.3 Filter magnitude and decibel calculations

The magnitude response can be expressed in decibels relative to a reference amplitude:

\[
G_{\mathrm{dB}}=
20\log_{10}\left|\frac{V_{\mathrm{out}}}{V_{\mathrm{in}}}\right|
\]

For an amplitude spectrum normalised to a reference amplitude \(A_{\mathrm{ref}}\):

\[
L_{\mathrm{dB}}=
20\log_{10}\left(\frac{A}{A_{\mathrm{ref}}}\right)
\]

For a power ratio:

\[
G_{\mathrm{dB}}=
10\log_{10}\left(\frac{P_{\mathrm{out}}}{P_{\mathrm{in}}}\right)
\]

The distinction between amplitude and power is important: amplitude ratios use \(20\log_{10}\), whereas power ratios use \(10\log_{10}\).

MATLAB's decibel-domain plots make it easier to visualise attenuation, compare in-band and out-of-band components, and examine the dynamic range of captured audio.

For example, a voltage gain of 0.707 relative to the passband corresponds approximately to a −3 dB point:

\[
20\log_{10}(0.707)\approx-3.01\,\text{dB}
\]

This provides a useful reference when checking the nominal cutoff frequencies.

### 8.4 Filter calculations and response verification

The analytical and MATLAB-based evaluations can be used to examine:

- Lower and upper cutoff frequencies.
- Nominal centre frequency and bandwidth.
- Butterworth magnitude response and expected attenuation.
- Frequency-domain characteristics of captured audio.
- Signal magnitude expressed in dB.
- Differences between the unfiltered and filtered spectra, where both signals are available.
- The distribution of spectral energy across selected octave bands.

The simulated response should be compared with the theoretical response and, where hardware measurements are available, with the measured response.

The actual MATLAB calculations and plots should be documented alongside the corresponding scripts so that the analysis can be reproduced.

## 9. Circuit Simulation and Verification

SPICE-based simulation is used to investigate the analog filter's electrical response before physical testing.

The verification workflow includes:

1. Constructing the cascaded Sallen–Key high-pass and low-pass stages.
2. Performing AC analysis across a frequency range extending below and above the intended passband.
3. Plotting output magnitude in decibels and phase.
4. Checking the lower and upper cutoff regions.
5. Examining passband gain and roll-off.
6. Comparing the simulated response with analytical calculations and MATLAB results.

The expected asymptotic attenuation rate of a fourth-order high-pass or low-pass section is approximately 80 dB per decade on its respective stopband side. The realised response depends on the circuit topology, component values, operational-amplifier characteristics, and loading.

Simulation and hardware measurement should be treated as separate verification stages. A simulated response alone does not establish the performance of the complete microphone and audio-capture system.

## 10. Results and Performance Evaluation

The project can be evaluated using the following metrics, provided that the corresponding simulations or measurements have been completed.

| Metric | Purpose |
|---|---|
| Lower cutoff frequency | Evaluates low-frequency rejection and the lower passband boundary |
| Upper cutoff frequency | Evaluates high-frequency rejection and the upper passband boundary |
| Passband gain | Measures the signal magnitude retained within the intended passband |
| Stopband attenuation | Quantifies the suppression of unwanted frequency components |
| Frequency resolution | Determines the spacing of FFT frequency bins |
| RMS amplitude | Characterises the signal level over the selected interval |
| dB magnitude | Supports comparison of signal and filter-response levels |
| Octave-band levels | Summarise energy within selected logarithmic frequency intervals |

Recommended plots for the project report include:

- Analog filter Bode magnitude and phase response.
- Time-domain waveform of the captured microphone signal.
- FFT magnitude spectrum of the recorded audio.
- Frequency-domain comparison before and after filtering, if both signals are available.
- Octave-band level distribution.
- MATLAB plots showing the relationship between the theoretical and simulated filter response.

Numerical results should be populated from the actual MATLAB output, circuit simulation, or hardware measurements rather than inferred from the nominal design values.

## 11. Tools and Technologies

| Tool or technology | Application |
|---|---|
| ALSA | Linux microphone audio capture and audio-device configuration |
| Microphone | Acoustic signal acquisition |
| Analog electronics | Eighth-order active band-pass filter implementation |
| Sallen–Key topology | Realisation of second-order active filter sections |
| SPICE simulators | AC response and circuit-level verification |
| MATLAB | Signal processing, numerical calculations, spectral analysis, and visualisation |
| FFT | Conversion of audio samples into the frequency domain |
| Octave-band theory | Logarithmic frequency grouping and acoustic interpretation |
| Decibel analysis | Quantitative evaluation of relative signal magnitude |

## 12. Engineering Significance and Applications

This project demonstrates the integration of analog electronics and digital signal analysis in an audio-processing workflow.

The hardware filter provides frequency-selective signal conditioning, while ALSA supports the acquisition of digital audio samples from a Linux-based capture device. MATLAB provides the numerical tools needed to inspect spectral content, evaluate signal magnitude, and relate the measured frequency distribution to octave-band analysis.

The underlying concepts are relevant to:

- Acoustic measurement and noise characterisation.
- Audio front-end design and signal conditioning.
- Frequency-response analysis of analog circuits.
- Embedded Linux audio acquisition.
- Digital signal processing and spectral analysis.
- Measurement-system design and verification.

The project also highlights an important engineering consideration: the overall measurement bandwidth is determined by the complete signal chain, not by the analog filter alone.

## 13. Future Improvements

Potential extensions include:

- Calibrating the microphone and acquisition chain for absolute sound-pressure-level measurements.
- Implementing a verified octave-band analysis routine in MATLAB.
- Comparing the theoretical, simulated, and measured filter responses.
- Evaluating component tolerances and operational-amplifier limitations.
- Investigating noise, clipping, sampling limitations, and aliasing in the acquisition chain.
- Comparing analog filtering with a digital IIR or FIR implementation.
- Automating audio capture, spectral analysis, and report generation.

## 14. References

1. S. Butterworth, “On the Theory of Filter Amplifiers,” *Experimental Wireless and the Wireless Engineer*, vol. 7, pp. 536–541, 1930.
2. R. P. Sallen and E. L. Key, “A Practical Method of Designing RC Active Filters,” *IRE Transactions on Circuit Theory*, vol. 2, no. 1, pp. 74–85, 1955.
3. Texas Instruments, *Analysis of the Sallen-Key Architecture*, Application Report SLOA024B.
4. ALSA Project, *Advanced Linux Sound Architecture*. https://www.alsa-project.org/
5. MathWorks, *Signal Processing Toolbox Documentation*. https://www.mathworks.com/help/signal/
6. A. V. Oppenheim and R. W. Schafer, *Discrete-Time Signal Processing*, 3rd ed., Prentice Hall, 2010.

---

**Author:** Ahana Talukder  
B.Tech, Electronics and Communication Engineering  
Institute of Engineering and Management (IEM), Kolkata


If you find this project useful, consider giving it a ⭐

</div>
