# 8th-Order Butterworth Band-Pass Filter with ALSA Audio Capture, ADC Interfacing, and MATLAB Analysis

## Project Overview

This project focuses on the design, simulation, and analysis of an **8th-order Butterworth band-pass filter** for audio signal processing. The system is designed to pass frequencies between **500 Hz and 50 kHz** while attenuating frequencies outside the specified range.

The project combines analog filter design, microphone-based audio acquisition, Linux ALSA (Advanced Linux Sound Architecture) audio capture, ADC interfacing, and MATLAB-based signal analysis. It also explores the relationship between wideband audio filtering and **octave-band analysis**, a technique commonly used in acoustics and audio engineering to study sound energy across different frequency ranges.

The filter is designed by cascading a fourth-order high-pass filter (HPF) with a fourth-order low-pass filter (LPF). Each fourth-order section consists of two second-order active Sallen–Key stages, resulting in an overall eighth-order response.

## Project Objectives

- Design an 8th-order Butterworth band-pass filter with a passband from 500 Hz to 50 kHz.
- Implement the filter using cascaded high-pass and low-pass sections.
- Capture microphone audio using ALSA, an open-source audio interface for Linux.
- Investigate the relationship between analog filtering, audio sampling, and ADC requirements.
- Analyze filter characteristics, frequency spectra, signal amplitudes, and decibel levels using MATLAB.
- Explore octave-band theory and its application to frequency-domain audio analysis.
- Evaluate filter performance through circuit simulation and mathematical modeling.

## System Specifications

| Parameter | Specification |
|---|---|
| Filter type | Butterworth band-pass |
| Overall filter order | 8th order |
| Lower cutoff frequency | 500 Hz |
| Upper cutoff frequency | 50 kHz |
| Geometric centre frequency | Approximately 5 kHz |
| Bandwidth | 49.5 kHz |
| Upper-to-lower cutoff ratio | 100:1 |
| Filter architecture | Fourth-order HPF cascaded with fourth-order LPF |
| Active filter topology | Sallen–Key |
| Intended supply voltage | ±5 V, subject to component compatibility |
| Audio capture interface | ALSA on Linux |
| Signal analysis platform | MATLAB |
| Theoretical minimum sampling rate | Greater than 100 kHz for capturing the full specified band |

The achievable frequency range depends on the microphone, analog components, operational amplifiers, ADC, and audio interface. A conventional audio interface operating at 44.1 kHz or 48 kHz cannot capture the entire frequency band up to 50 kHz.

## 1. Filter Design and Mathematical Foundation

### 1.1 Centre Frequency

The geometric centre frequency of a band-pass filter is calculated using the lower and upper cutoff frequencies:

**f₀ = √(fL × fH)**

For the selected cutoff frequencies:

**f₀ = √(500 × 50,000)**

**f₀ = 5,000 Hz = 5 kHz**

This value represents the geometric centre of the specified passband. It does not imply that the filter has a narrow bandwidth around 5 kHz.

### 1.2 Bandwidth

The bandwidth is the difference between the upper and lower cutoff frequencies:

**BW = fH − fL**

**BW = 50,000 − 500 = 49,500 Hz**

Therefore, the filter bandwidth is **49.5 kHz**.

The cutoff-frequency ratio is:

**fH / fL = 50,000 / 500 = 100**

### 1.3 Butterworth Filter Response

A Butterworth filter is designed to provide a maximally flat magnitude response in its passband. Its idealized low-pass magnitude response is expressed as:

**|H(jω)|² = 1 / [1 + (ω / ωc)²ⁿ]**

Where:

- H(jω) represents the frequency response.
- ω is the angular frequency.
- ωc is the cutoff angular frequency.
- n is the filter order.

The angular cutoff frequency is related to the cutoff frequency in hertz by:

**ωc = 2πfc**

At the cutoff frequency, the ideal Butterworth magnitude is approximately 0.707 of its passband value, corresponding to approximately −3.01 dB.

The combined response of the cascaded high-pass and low-pass sections is:

**HBP(s) = HHP(s) × HLP(s)**

Where:

- HBP(s) is the overall band-pass transfer function.
- HHP(s) is the high-pass transfer function.
- HLP(s) is the low-pass transfer function.
- s is the complex frequency variable.

The actual response should be verified through circuit simulation and hardware measurements because practical component tolerances and interactions between stages can affect the final response.

### 1.4 Sallen–Key Filter Stage

The second-order Sallen–Key topology is used as the basis for the active filter stages. For a standard second-order stage, the natural frequency is determined by its resistor and capacitor values:

**fc = 1 / [2π√(R₁R₂C₁C₂)]**

The damping and quality factor depend on the circuit topology, component ratios, and amplifier gain. Component values must therefore be selected according to the intended response of each stage rather than using the cutoff equation alone.

The fourth-order high-pass and low-pass sections are each formed from two second-order stages. For a fourth-order Butterworth response, the two second-order sections have approximate quality factors:

- Q₁ ≈ 0.5412
- Q₂ ≈ 1.3066

The high-pass and low-pass sections must be designed separately and then cascaded to obtain the intended overall band-pass behavior.

## 2. Audio Acquisition Using ALSA

ALSA (Advanced Linux Sound Architecture) is an open-source audio subsystem for Linux that provides interfaces for audio capture and playback.

In this project, ALSA is used to acquire microphone audio for subsequent signal processing and analysis. The captured audio can be saved as a digital recording and imported into MATLAB for examination in the time and frequency domains.

The conceptual acquisition workflow is:

1. A microphone converts acoustic pressure variations into an electrical signal.
2. The analog signal is conditioned and filtered by the analog circuit, where the hardware configuration supports this signal path.
3. The ADC converts the analog signal into digital samples.
4. ALSA provides the software interface for capturing audio samples supported by the connected device.
5. MATLAB is used to analyze the recorded signal, frequency spectrum, amplitude, and decibel levels.

**Important hardware consideration:** ALSA is an audio-capture software interface; it does not itself implement the analog Butterworth filter. If the microphone is connected directly to a conventional computer sound card, the recorded signal may not pass through the designed analog filter.

To evaluate the physical filter using ALSA recordings, the filter output must be connected to a suitable acquisition device, with appropriate signal conditioning and protection. The acquisition hardware must also support the required sampling rate and bandwidth.

## 3. ADC Interfacing and Sampling Considerations

The upper cutoff frequency of the designed filter is 50 kHz. To represent this frequency digitally, the sampling frequency must satisfy the Nyquist criterion:

**fs > 2fmax**

For a maximum frequency of 50 kHz:

**fs > 2 × 50,000**

**fs > 100,000 samples/second**

Thus, a sampling rate greater than 100 kHz is required for capturing the entire specified frequency band, with additional margin generally desirable for practical anti-aliasing and measurement requirements.

A sampling rate of 200 kHz may be considered as a design target, provided the ADC, audio interface, driver, and recording configuration support it. The actual configured sampling rate should be verified rather than assumed.

The ADC resolution also affects quantization noise and the dynamic range of the captured signal. For an ideal N-bit ADC, the number of quantization levels is:

**L = 2ᴺ**

The approximate ideal full-scale quantization signal-to-noise ratio for a full-scale sinusoidal input is:

**SNR(dB) ≈ 6.02N + 1.76**

This is an ideal estimate. Actual performance also depends on the ADC architecture, analog noise, input signal level, and acquisition circuitry.

## 4. Octave-Band Theory and Its Relevance

Octave-band analysis is widely used in acoustics, environmental noise measurement, sound-level assessment, and audio engineering. An octave band is a frequency interval in which the upper band-edge frequency is twice the lower band-edge frequency.

The defining relationship is:

**fH = 2fL**

The geometric centre frequency of an octave band is:

**fc = √(fL × fH)**

For example, an octave band with lower and upper edges of 1 kHz and 2 kHz has a centre frequency of:

**fc = √(1,000 × 2,000)**

**fc ≈ 1,414.2 Hz**

The designed 500 Hz–50 kHz Butterworth filter is a wideband band-pass filter, **not an octave-band filter bank by itself**. Its role is to restrict the frequency range of interest before further analysis. Octave-band analysis can then be performed on the captured and filtered signal by dividing the spectrum into octave-width frequency bands and calculating the signal level within each band.

For this project, octave-band theory provides a framework for examining how the recorded audio energy is distributed across logarithmically spaced frequency ranges. This relates the broad passband of the analog filter to more detailed acoustic analysis.

A complete octave-band implementation requires additional digital processing, such as band-specific filtering or frequency-domain integration. The analog 8th-order Butterworth filter alone does not produce separate octave-band outputs.

## 5. MATLAB-Based Signal Analysis

MATLAB is used to analyze the captured audio and evaluate the designed filter mathematically. It can be used to construct a theoretical filter model, examine the frequency response, process recorded samples, and calculate relevant signal parameters.

### 5.1 Time-Domain Analysis

Time-domain analysis helps examine signal amplitude and variations over time. For a sampled signal x[n], the root-mean-square (RMS) amplitude is:

**xRMS = √[(1/N) × Σ x[n]²]**

Where:

- x[n] is the sample at index n.
- N is the total number of samples.
- xRMS is the RMS amplitude of the signal.

The RMS value provides a measure of the signal's effective amplitude and can be used when comparing recordings or evaluating signal levels, provided the signal scaling and measurement conditions are consistent.

### 5.2 Frequency-Domain Analysis Using FFT

The Fast Fourier Transform (FFT) is used to examine the frequency content of the captured audio. It helps identify dominant frequency components and determine how the signal energy is distributed across the spectrum.

For a sampling frequency fs and an FFT length of N, the frequency-bin spacing is:

**Δf = fs / N**

The frequency corresponding to bin k is:

**fk = k × fs / N**

Increasing the FFT length can provide finer frequency-bin spacing, although the effective frequency resolution also depends on the observation duration, windowing, and signal characteristics.

The measured spectrum can be used to investigate the frequency content before and after filtering, where corresponding input and output recordings are available.

### 5.3 Decibel Calculations

Decibels provide a logarithmic representation of signal amplitude or power ratios. For voltage measurements across the same impedance, the gain in decibels is:

**GdB = 20 log₁₀(|Vout / Vin|)**

For an amplitude A referenced to Aref, the level is:

**LdB = 20 log₁₀(A / Aref)**

For a power ratio, the decibel value is:

**GdB = 10 log₁₀(Pout / Pin)**

The reference quantity must be defined when reporting an absolute sound or signal level. For example, a digital audio amplitude expressed in dBFS uses the digital full-scale level as its reference, whereas sound pressure level uses a reference sound pressure.

At the ideal cutoff frequency of a Butterworth response, the magnitude is:

**|H(jωc)| = 1 / √2 ≈ 0.707**

The corresponding gain relative to the passband reference is:

**20 log₁₀(0.707) ≈ −3.01 dB**

When using MATLAB, the decibel calculation must match the quantity being measured. Voltage or amplitude ratios use 20 log₁₀, while power ratios use 10 log₁₀. For FFT plots, the selected amplitude normalization and reference should be stated clearly.

### 5.4 Filter Response Analysis

MATLAB can be used to calculate and plot the theoretical magnitude response of the filter. The analysis helps verify whether the intended passband and attenuation trends are consistent with the design.

Useful analysis tasks include:

- Plotting the magnitude response in decibels.
- Identifying the lower and upper cutoff frequencies.
- Comparing theoretical and simulated responses.
- Examining the FFT of recorded audio.
- Calculating RMS amplitude and signal-level changes.
- Comparing frequency-domain results before and after filtering.
- Evaluating octave-band levels when the corresponding bandwise processing has been implemented.

The MATLAB model and hardware measurements should be treated as separate forms of evidence. A theoretical model does not establish the physical performance of the completed circuit unless supported by simulation or measurement.

## 6. Circuit Simulation and Verification

Circuit simulation is used to examine the designed filter response before hardware implementation. The project workflow includes AC frequency-response analysis and mathematical modeling.

The circuit has been explored using TINA-TI, with MATLAB used for theoretical and signal-processing analysis. Additional cross-verification in PSpice or LTspice can be used to compare the frequency response and check whether the simulated cutoff behavior agrees with the intended design.

Further verification can include:

- Confirming the lower and upper cutoff frequencies.
- Checking passband gain and attenuation.
- Reviewing the response of each second-order stage.
- Assessing sensitivity to resistor and capacitor tolerances.
- Checking operational-amplifier bandwidth and slew-rate limitations.
- Comparing simulation results with measured hardware response.

The status of each verification task should be updated as the work progresses. Simulation, PCB fabrication, and bench testing should only be marked complete after the corresponding work has actually been performed.

## 7. End-to-End Project Workflow

1. **Analog filter design:** Design the fourth-order high-pass and fourth-order low-pass sections and cascade them to obtain the overall band-pass response.
2. **Circuit simulation:** Evaluate the AC response and verify the expected cutoff behavior.
3. **Hardware implementation:** Assemble the circuit using suitable components and verify supply, bias, and signal-path requirements.
4. **Audio acquisition:** Capture microphone audio through ALSA using an acquisition device that supports the intended bandwidth and sampling rate.
5. **MATLAB processing:** Import the recorded data and perform time-domain, FFT, RMS, and decibel analysis.
6. **Octave-band evaluation:** Apply separate octave-band filtering or frequency-domain band integration to calculate bandwise signal levels.
7. **Validation:** Compare theoretical, simulated, and measured results where all three are available.

## 8. Limitations and Practical Considerations

- The microphone's frequency response must cover the frequencies being investigated.
- A conventional sound card may not support sampling above 100 kHz and therefore may not capture the full 50 kHz upper band.
- ALSA provides the capture interface but does not establish that the signal has passed through the analog filter.
- The analog filter's response depends on component tolerances and the bandwidth of the operational amplifiers.
- The filter's actual passband gain and cutoff behavior must be confirmed through simulation or measurement.
- Octave-band analysis requires additional processing beyond the wideband analog filter.
- Decibel values must use a clearly defined reference and the appropriate amplitude or power formula.
- The analog circuit, ADC, and acquisition hardware must be compatible with the intended signal range and sampling requirements.

## 9. Project Status

The project includes the design concept for an 8th-order Butterworth band-pass filter, circuit-level analysis in TINA-TI, and MATLAB-based theoretical and signal-processing work. ALSA is used as the Linux audio-capture interface.

The following items should be marked complete only after implementation and verification:

- PSpice/LTspice cross-validation.
- Component-tolerance or Monte Carlo analysis.
- PCB layout and fabrication.
- Physical filter response measurements.
- Verified audio capture through the filter and ADC path.
- Experimental comparison of MATLAB, simulation, and hardware results.
- Implementation of octave-band calculations on recorded audio.

## 10. Conclusion

This project brings together analog active-filter design, audio acquisition, ADC sampling considerations, and MATLAB-based signal analysis. The 8th-order Butterworth band-pass filter is designed to isolate the 500 Hz–50 kHz frequency range, while ALSA provides a software interface for acquiring supported microphone or device audio.

MATLAB supports the evaluation of time-domain signals, FFT spectra, RMS amplitude, and decibel levels. Octave-band theory extends the analysis by providing a logarithmic framework for studying sound energy across separate frequency bands, provided that the appropriate bandwise processing is implemented.

The combined workflow provides a foundation for studying the relationship between analog filter characteristics and digital audio analysis, while highlighting the importance of validating the complete hardware and acquisition chain.

## References

- ALSA Project: https://www.alsa-project.org/
- MathWorks MATLAB Documentation: https://www.mathworks.com/help/matlab/
- Butterworth filter and active-filter design references.
- Sallen–Key active-filter design references.
- Relevant acoustics and octave-band measurement standards.
