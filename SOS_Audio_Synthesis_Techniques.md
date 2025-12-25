# SOUNDS OF SEISMIC (SOS) AUDIO SYNTHESIS TECHNIQUES
## A Comprehensive Technical Reference for Seismic-to-Electronica Transformation

---

## Table of Contents

1. [Granular Synthesis](#1-granular-synthesis)
2. [Wavetable Synthesis](#2-wavetable-synthesis)
3. [Additive Synthesis](#3-additive-synthesis)
4. [Frequency Modulation (FM) Synthesis](#4-frequency-modulation-fm-synthesis)
5. [Subtractive Synthesis](#5-subtractive-synthesis)
6. [JavaScript Libraries & Resources](#6-javascript-libraries--resources)
7. [SOS Implementation Considerations](#7-sos-implementation-considerations)

---

## 1. GRANULAR SYNTHESIS

**Wikipedia Reference:** https://en.wikipedia.org/wiki/Granular_synthesis

Granular synthesis is a method of sound synthesis based on splitting audio into tiny fragments called "grains" (typically 1-100ms in duration) and reassembling them with various transformations. This technique is particularly powerful for the SOS project as seismic waveforms can be treated as source material for granulation, creating ethereal textures from geological data.

### 1a: Classic Granular Synthesis

**Description:** The foundational approach to granular synthesis involves breaking an audio source into small grains, each windowed with an amplitude envelope (typically Gaussian, Hann, or triangular), and playing them back with controlled density, pitch, and position.

**Key Parameters:**
- **Grain Size:** Duration of each grain (1-100ms typical)
- **Grain Density:** Number of grains per second
- **Position/Spread:** Where in the source buffer grains are extracted
- **Pitch Variation:** Transposition of individual grains
- **Envelope Shape:** Attack/decay characteristics of each grain

**JavaScript Implementation Pattern:**

```javascript
// Basic Granular Synthesis with Web Audio API
class GranularSynthesizer {
    constructor(audioContext, buffer) {
        this.ctx = audioContext;
        this.buffer = buffer;
        this.grainSize = 0.05; // 50ms grains
        this.density = 20; // grains per second
        this.spread = 0.1; // position randomization
        this.pitch = 1.0;
    }

    playGrain(position) {
        const now = this.ctx.currentTime;
        const source = this.ctx.createBufferSource();
        const gain = this.ctx.createGain();
        
        source.buffer = this.buffer;
        source.playbackRate.value = this.pitch;
        
        // Gaussian-like envelope
        gain.gain.setValueAtTime(0, now);
        gain.gain.linearRampToValueAtTime(1, now + this.grainSize * 0.5);
        gain.gain.linearRampToValueAtTime(0, now + this.grainSize);
        
        source.connect(gain);
        gain.connect(this.ctx.destination);
        
        const startPos = Math.max(0, position + (Math.random() - 0.5) * this.spread);
        source.start(now, startPos, this.grainSize);
    }

    start(position = 0) {
        this.interval = setInterval(() => {
            this.playGrain(position);
        }, 1000 / this.density);
    }

    stop() {
        clearInterval(this.interval);
    }
}
```

**GitHub Resources:**
- [granular-js](https://github.com/philippfromme/granular-js) - Simple granular synthesis library
- [zya/granular](https://github.com/zya/granular) - HTML5 Granular Synthesizer with multi-touch support
- [Google Experiments: Granular Synthesiser](https://experiments.withgoogle.com/granular-synthesiser)

### 1b: Synchronous Granular Synthesis

**Description:** Grains are emitted at regular, predictable intervals synchronized to a fundamental frequency. This creates pitched tones where the grain emission rate determines the perceived pitch. Essential for creating tonal textures from seismic data.

**Key Characteristics:**
- Grain emission locked to a rhythmic pulse
- Produces pitched output based on grain rate
- Creates more coherent, less chaotic textures than asynchronous methods

**JavaScript Implementation Pattern:**

```javascript
class SynchronousGranular {
    constructor(audioContext, buffer) {
        this.ctx = audioContext;
        this.buffer = buffer;
        this.fundamentalFreq = 100; // Hz - determines grain rate
        this.grainDuration = 0.02; // 20ms
    }

    scheduleGrains(startTime, duration, position) {
        const grainInterval = 1 / this.fundamentalFreq;
        const numGrains = Math.floor(duration / grainInterval);
        
        for (let i = 0; i < numGrains; i++) {
            const grainTime = startTime + i * grainInterval;
            this.emitGrain(grainTime, position);
        }
    }

    emitGrain(time, position) {
        const source = this.ctx.createBufferSource();
        const envelope = this.ctx.createGain();
        
        source.buffer = this.buffer;
        
        // Hann window envelope
        envelope.gain.setValueAtTime(0, time);
        envelope.gain.linearRampToValueAtTime(1, time + this.grainDuration / 2);
        envelope.gain.linearRampToValueAtTime(0, time + this.grainDuration);
        
        source.connect(envelope);
        envelope.connect(this.ctx.destination);
        source.start(time, position, this.grainDuration);
    }
}
```

### 1c: Pulsar Synthesis

**Description:** Developed by Curtis Roads and Alberto de Campo, pulsar synthesis generates trains of sonic particles called "pulsars." Each pulsar consists of a brief waveform (pulsaret) followed by silence. The technique bridges rhythm and pitch as the pulsar rate crosses the ~20Hz threshold between individual events and continuous tone.

**Key Parameters:**
- **Fundamental Frequency:** Rate of pulsar emission (determines pitch above 20Hz)
- **Formant Frequency:** Internal frequency of the pulsaret waveform
- **Pulsaret Waveform:** Shape of the sonic particle (sine, cosine, complex)
- **Window Function:** Envelope applied to each pulsaret (Gaussian, triangular, exponential)
- **Duty Cycle:** Ratio of pulsaret to silence (pulsaret-width modulation/PulWM)

**Theoretical Foundation (from Curtis Roads' "Microsound"):**
- Pulsars operate in the microsound time domain (less than 100ms)
- Capable of producing both rhythmic patterns (below 20Hz) and pitched tones
- Independent control of fundamental and formant frequencies enables unique timbral manipulation

**JavaScript Implementation Pattern:**

```javascript
class PulsarSynthesizer {
    constructor(audioContext) {
        this.ctx = audioContext;
        this.fundamentalFreq = 100; // Pulsar rate
        this.formantFreq = 400; // Pulsaret internal frequency
        this.dutyCycle = 0.5; // Pulsaret width ratio
    }

    generatePulsaret(duration) {
        const sampleRate = this.ctx.sampleRate;
        const length = Math.floor(duration * sampleRate);
        const buffer = this.ctx.createBuffer(1, length, sampleRate);
        const data = buffer.getChannelData(0);
        
        for (let i = 0; i < length; i++) {
            const t = i / sampleRate;
            const pulsaretDuration = duration * this.dutyCycle;
            
            if (t < pulsaretDuration) {
                // Pulsaret with Gaussian window
                const phase = 2 * Math.PI * this.formantFreq * t;
                const window = Math.exp(-0.5 * Math.pow((t - pulsaretDuration/2) / (pulsaretDuration/4), 2));
                data[i] = Math.sin(phase) * window;
            } else {
                // Silence period
                data[i] = 0;
            }
        }
        return buffer;
    }

    play(duration) {
        const pulsarPeriod = 1 / this.fundamentalFreq;
        const pulsaretBuffer = this.generatePulsaret(pulsarPeriod);
        const numPulsars = Math.floor(duration * this.fundamentalFreq);
        
        for (let i = 0; i < numPulsars; i++) {
            const source = this.ctx.createBufferSource();
            source.buffer = pulsaretBuffer;
            source.connect(this.ctx.destination);
            source.start(this.ctx.currentTime + i * pulsarPeriod);
        }
    }
}
```

**Reference:** Curtis Roads, "Sound Composition with Pulsars," Journal of the Audio Engineering Society, March 2001

### 1d: Spectral Granular Synthesis

**Description:** Applies granular techniques in the frequency domain using FFT/IFFT processing. Audio is analyzed via STFT (Short-Time Fourier Transform), manipulated in the spectral domain, and resynthesized. This allows for frequency-dependent grain manipulation.

**Key Characteristics:**
- Grains are spectral frames rather than time-domain samples
- Enables frequency-selective processing
- Powerful for spectral morphing and cross-synthesis

**JavaScript Resources:**
- [PhaseVocoderJS](https://github.com/echo66/PhaseVocoderJS) - Phase vocoder with identity phase locking
- [dsp-kit](https://github.com/oramics/dsp-kit) - DSP algorithms including phase vocoder

### 1e: Granular Resynthesis

**Description:** Uses granular techniques to resynthesize audio that has been analyzed, allowing for dramatic time-stretching and pitch-shifting without artifacts typical of other methods.

**Applications for SOS:**
- Time-stretch seismic waveforms to audible durations
- Pitch-shift geological rumbles to musical frequencies
- Create evolving textures from static seismic snapshots

---

## 2. WAVETABLE SYNTHESIS

**Wikipedia Reference:** https://en.wikipedia.org/wiki/Wavetable_synthesis

Wavetable synthesis stores single-cycle waveforms in lookup tables and reads through them at variable rates to produce different pitches. By morphing between different wavetables, complex evolving timbres can be created.

### 2a: Classic Wavetable Synthesis

**Description:** The foundational technique uses a single wavetable (array of samples representing one cycle of a waveform) that is read at different speeds to produce different pitches.

**Historical Context:** The PPG Wave synthesizer (1981) pioneered this approach, later used extensively by Waldorf and other manufacturers.

**JavaScript Implementation with Web Audio PeriodicWave:**

```javascript
// Classic Wavetable using Web Audio API's createPeriodicWave
class WavetableSynth {
    constructor(audioContext) {
        this.ctx = audioContext;
        this.oscillators = [];
    }

    createWavetable(harmonics) {
        // harmonics = array of {frequency, amplitude, phase}
        const n = 4096;
        const real = new Float32Array(n);
        const imag = new Float32Array(n);
        
        // Build Fourier series coefficients
        harmonics.forEach((h, i) => {
            if (i < n) {
                real[i] = h.amplitude * Math.cos(h.phase || 0);
                imag[i] = h.amplitude * Math.sin(h.phase || 0);
            }
        });
        
        return this.ctx.createPeriodicWave(real, imag);
    }

    playNote(frequency, wavetable, duration) {
        const osc = this.ctx.createOscillator();
        const gain = this.ctx.createGain();
        
        osc.setPeriodicWave(wavetable);
        osc.frequency.value = frequency;
        
        gain.gain.setValueAtTime(0.5, this.ctx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, this.ctx.currentTime + duration);
        
        osc.connect(gain);
        gain.connect(this.ctx.destination);
        
        osc.start();
        osc.stop(this.ctx.currentTime + duration);
    }

    // Create common waveforms
    createSawtoothTable() {
        const harmonics = [];
        for (let i = 1; i <= 64; i++) {
            harmonics.push({ amplitude: 1 / i, phase: 0 });
        }
        return this.createWavetable(harmonics);
    }

    createSquareTable() {
        const harmonics = [];
        for (let i = 1; i <= 64; i += 2) { // Odd harmonics only
            harmonics.push({ amplitude: 1 / i, phase: 0 });
        }
        return this.createWavetable(harmonics);
    }
}
```

**GitHub Resources:**
- [wavetable-synth-2](https://github.com/looshi/wavetable-synth-2) - Full wavetable synth with LFO, chorus, arpeggiator
- [cwilso/web-audio-samples](https://github.com/cwilso/web-audio-samples) - Chrome Labs wavetable examples

### 2b: Vector Synthesis

**Description:** Multiple wavetables are positioned at points in a 2D or 3D space, and a "joystick" position determines the mix between them. Moving through the vector space creates smooth timbral transitions.

**Historical Context:** Pioneered by Sequential Circuits Prophet VS (1986) and later refined in the Korg Wavestation.

**JavaScript Implementation Pattern:**

```javascript
class VectorSynthesizer {
    constructor(audioContext) {
        this.ctx = audioContext;
        this.wavetables = []; // Array of 4 wavetables (corners of 2D space)
        this.oscillators = [];
        this.gains = [];
    }

    setWavetables(wt1, wt2, wt3, wt4) {
        this.wavetables = [wt1, wt2, wt3, wt4];
    }

    createVoice(frequency) {
        // Create 4 oscillators, one for each wavetable
        this.oscillators = this.wavetables.map(wt => {
            const osc = this.ctx.createOscillator();
            osc.setPeriodicWave(wt);
            osc.frequency.value = frequency;
            return osc;
        });

        this.gains = this.oscillators.map(() => {
            return this.ctx.createGain();
        });

        const merger = this.ctx.createChannelMerger(1);
        
        this.oscillators.forEach((osc, i) => {
            osc.connect(this.gains[i]);
            this.gains[i].connect(merger);
        });

        merger.connect(this.ctx.destination);
        this.oscillators.forEach(osc => osc.start());
    }

    setVectorPosition(x, y) {
        // x, y range: 0-1
        // Bilinear interpolation between 4 corners
        const gains = [
            (1 - x) * (1 - y), // Bottom-left
            x * (1 - y),       // Bottom-right
            (1 - x) * y,       // Top-left
            x * y              // Top-right
        ];

        gains.forEach((g, i) => {
            this.gains[i].gain.setTargetAtTime(g, this.ctx.currentTime, 0.01);
        });
    }
}
```

### 2c: Sample-Based Wavetable Synthesis

**Description:** Instead of mathematically generated waveforms, single-cycle samples extracted from real recordings serve as wavetables. This brings organic character to the synthesis.

**SOS Application:** Extract single cycles from seismic waveforms to create unique wavetables reflecting the character of different earthquake events.

### 2d: Morphing Wavetable Synthesis

**Description:** Smooth interpolation between different wavetables over time, creating evolving timbres. Can interpolate between arbitrary waveforms.

**JavaScript Implementation Pattern:**

```javascript
class MorphingWavetable {
    constructor(audioContext) {
        this.ctx = audioContext;
        this.wavetableA = null;
        this.wavetableB = null;
        this.morphPosition = 0; // 0 = fully A, 1 = fully B
    }

    interpolateWavetables(tableA, tableB, position) {
        const length = tableA.length;
        const result = new Float32Array(length);
        
        for (let i = 0; i < length; i++) {
            result[i] = tableA[i] * (1 - position) + tableB[i] * position;
        }
        return result;
    }

    setMorphPosition(position) {
        this.morphPosition = Math.max(0, Math.min(1, position));
        // In practice, you'd regenerate the PeriodicWave here
        // or use multiple oscillators with crossfading gains
    }
}
```

### 2e: Wavetable Resynthesis

**Description:** Analyze existing sounds via FFT, extract harmonic content, and store as wavetables. Allows recreation of complex timbres in a computationally efficient form.

---

## 3. ADDITIVE SYNTHESIS

**Wikipedia Reference:** https://en.wikipedia.org/wiki/Additive_synthesis

Additive synthesis builds complex timbres by summing multiple sine wave oscillators (partials). Based on Fourier theory, any periodic sound can be reconstructed from a series of sine waves with specific frequencies, amplitudes, and phases.

### 3a: Classic Additive Synthesis

**Description:** The foundational approach uses banks of sine wave oscillators at harmonic or inharmonic frequency ratios, each with independent amplitude and frequency control.

**JavaScript Implementation:**

```javascript
class AdditiveSynth {
    constructor(audioContext, numPartials = 16) {
        this.ctx = audioContext;
        this.numPartials = numPartials;
        this.oscillators = [];
        this.gains = [];
        this.masterGain = this.ctx.createGain();
        this.masterGain.connect(this.ctx.destination);
    }

    createVoice(fundamentalFreq, partialAmplitudes) {
        for (let i = 0; i < this.numPartials; i++) {
            const osc = this.ctx.createOscillator();
            const gain = this.ctx.createGain();
            
            osc.type = 'sine';
            osc.frequency.value = fundamentalFreq * (i + 1); // Harmonic series
            
            gain.gain.value = partialAmplitudes[i] || (1 / (i + 1)); // Default to sawtooth-like
            
            osc.connect(gain);
            gain.connect(this.masterGain);
            
            this.oscillators.push(osc);
            this.gains.push(gain);
        }
        
        // Normalize volume
        this.masterGain.gain.value = 1 / Math.sqrt(this.numPartials);
    }

    start() {
        this.oscillators.forEach(osc => osc.start());
    }

    stop() {
        this.oscillators.forEach(osc => osc.stop());
    }

    setPartialAmplitude(partialIndex, amplitude) {
        if (this.gains[partialIndex]) {
            this.gains[partialIndex].gain.setTargetAtTime(
                amplitude, 
                this.ctx.currentTime, 
                0.01
            );
        }
    }

    setFundamental(freq) {
        this.oscillators.forEach((osc, i) => {
            osc.frequency.setTargetAtTime(
                freq * (i + 1), 
                this.ctx.currentTime, 
                0.01
            );
        });
    }
}
```

**GitHub Resources:**
- [teropa's Additive Synthesis Tutorial](https://teropa.info/blog/2016/09/20/additive-synthesis.html) - Comprehensive Web Audio tutorial
- [Additive-harmonics](http://kindohm.github.io/additive-harmonics/) - Interactive visualization

### 3b: Spectral Resynthesis

**Description:** Uses FFT analysis to extract the spectral content of a sound, then resynthesizes it using banks of oscillators following the analyzed amplitude and frequency trajectories.

**Applications for SOS:**
- Analyze seismic waveforms to extract dominant frequencies
- Resynthesize with independent control over each spectral component
- Create "spectral freeze" effects capturing a moment of seismic activity

### 3c: Harmonic Additive Synthesis

**Description:** Partials are constrained to integer multiples of the fundamental frequency (1f, 2f, 3f, 4f...). This produces musical tones with clear pitch.

**Waveform Recipes:**
- **Sawtooth:** All harmonics, amplitude = 1/n
- **Square:** Odd harmonics only, amplitude = 1/n
- **Triangle:** Odd harmonics only, amplitude = 1/n²

### 3d: Inharmonic Additive Synthesis

**Description:** Partials have non-integer frequency relationships, producing bell-like, metallic, or noise-like timbres. Essential for synthesizing percussion and natural sounds.

**JavaScript Implementation:**

```javascript
class InharmonicSynth extends AdditiveSynth {
    createBellVoice(fundamentalFreq) {
        // Inharmonic ratios typical of bells
        const bellRatios = [1, 2.4, 3.0, 4.5, 5.2, 6.8, 8.0, 9.5];
        const bellAmps = [1, 0.6, 0.4, 0.3, 0.2, 0.15, 0.1, 0.08];
        
        bellRatios.forEach((ratio, i) => {
            const osc = this.ctx.createOscillator();
            const gain = this.ctx.createGain();
            
            osc.type = 'sine';
            osc.frequency.value = fundamentalFreq * ratio;
            gain.gain.value = bellAmps[i];
            
            // Bell-like decay envelope
            const now = this.ctx.currentTime;
            const decayTime = 2 + Math.random() * 2;
            gain.gain.setValueAtTime(bellAmps[i], now);
            gain.gain.exponentialRampToValueAtTime(0.001, now + decayTime);
            
            osc.connect(gain);
            gain.connect(this.masterGain);
            
            osc.start(now);
            osc.stop(now + decayTime);
        });
    }
}
```

### 3e: Karplus-Strong Synthesis

**Description:** A form of physical modeling that simulates plucked strings using a delay line with filtered feedback. Technically a hybrid of additive and subtractive principles, it produces remarkably realistic string sounds from simple algorithms.

**Algorithm:**
1. Fill a delay buffer with noise (represents string excitation)
2. Read samples from buffer and output
3. Average adjacent samples (lowpass filter simulating string damping)
4. Feed back into the buffer

**JavaScript Implementation:**

```javascript
class KarplusStrong {
    constructor(audioContext) {
        this.ctx = audioContext;
    }

    pluck(frequency, duration = 2) {
        const sampleRate = this.ctx.sampleRate;
        const bufferLength = Math.floor(sampleRate / frequency);
        const outputLength = Math.floor(sampleRate * duration);
        
        const audioBuffer = this.ctx.createBuffer(1, outputLength, sampleRate);
        const data = audioBuffer.getChannelData(0);
        
        // Initialize delay line with noise (pluck excitation)
        const delayLine = new Float32Array(bufferLength);
        for (let i = 0; i < bufferLength; i++) {
            delayLine[i] = Math.random() * 2 - 1;
        }
        
        // Karplus-Strong algorithm
        let delayIndex = 0;
        const dampingFactor = 0.996; // Controls decay rate
        
        for (let i = 0; i < outputLength; i++) {
            // Output current sample
            data[i] = delayLine[delayIndex];
            
            // Lowpass filter (average of current and next sample)
            const nextIndex = (delayIndex + 1) % bufferLength;
            const filtered = dampingFactor * 0.5 * 
                            (delayLine[delayIndex] + delayLine[nextIndex]);
            
            // Write back to delay line
            delayLine[delayIndex] = filtered;
            
            delayIndex = nextIndex;
        }
        
        // Play the buffer
        const source = this.ctx.createBufferSource();
        source.buffer = audioBuffer;
        source.connect(this.ctx.destination);
        source.start();
        
        return source;
    }
}
```

**GitHub Resources:**
- [javascript-karplus-strong](https://github.com/mrahtz/javascript-karplus-strong) - asm.js implementation
- [luciopaiva/karplus](https://github.com/luciopaiva/karplus) - Clean implementation with demo

### 3f: Formant Additive Synthesis

**Description:** Groups of partials are emphasized to simulate vocal formants or resonant characteristics of acoustic instruments. By controlling formant frequencies and bandwidths, vowel-like qualities can be added to synthetic sounds.

---

## 4. FREQUENCY MODULATION (FM) SYNTHESIS

**Wikipedia Reference:** https://en.wikipedia.org/wiki/Frequency_modulation_synthesis

FM synthesis creates complex timbres by using one oscillator (modulator) to modulate the frequency of another oscillator (carrier). Discovered by John Chowning at Stanford in 1967, it became the basis for the legendary Yamaha DX7 synthesizer.

### 4a: Linear FM Synthesis

**Description:** The modulator signal is added directly to the carrier frequency. The resulting sidebands are distributed symmetrically around the carrier frequency.

**Mathematical Foundation:**
```
output = sin(2π × carrier_freq × t + modulation_index × sin(2π × modulator_freq × t))
```

**JavaScript Implementation:**

```javascript
class FMSynthesizer {
    constructor(audioContext) {
        this.ctx = audioContext;
    }

    createVoice(carrierFreq, modFreq, modIndex) {
        // Carrier oscillator
        const carrier = this.ctx.createOscillator();
        carrier.type = 'sine';
        carrier.frequency.value = carrierFreq;
        
        // Modulator oscillator
        const modulator = this.ctx.createOscillator();
        modulator.type = 'sine';
        modulator.frequency.value = modFreq;
        
        // Modulation depth (modulation index × modulator frequency)
        const modGain = this.ctx.createGain();
        modGain.gain.value = modIndex * modFreq;
        
        // Connect modulator to carrier's frequency
        modulator.connect(modGain);
        modGain.connect(carrier.frequency);
        
        // Output
        const output = this.ctx.createGain();
        output.gain.value = 0.3;
        carrier.connect(output);
        output.connect(this.ctx.destination);
        
        return { carrier, modulator, modGain, output };
    }

    playNote(carrierFreq, modRatio, modIndex, duration) {
        const modFreq = carrierFreq * modRatio;
        const voice = this.createVoice(carrierFreq, modFreq, modIndex);
        
        const now = this.ctx.currentTime;
        
        voice.carrier.start(now);
        voice.modulator.start(now);
        
        voice.carrier.stop(now + duration);
        voice.modulator.stop(now + duration);
    }

    // Classic DX7-style bell sound
    playBell(freq) {
        // C:M ratio of 1:1.4 with high modulation index
        this.playNote(freq, 1.4, 4, 3);
    }

    // Electric piano-like sound
    playEPiano(freq) {
        // C:M ratio of 1:1 with moderate index
        this.playNote(freq, 1, 2, 1);
    }
}
```

**GitHub Resources:**
- [greweb FM with Web Audio](https://greweb.me/2013/08/FM-audio-api) - Interactive FM tutorial
- [mohayonao/fm-synth](https://github.com/mohayonao/fm-synth) - Configurable FM synth library
- [cprimozic FM Synth](https://cprimozic.net/blog/fm-synth-rust-wasm-simd/) - Rust/WASM FM implementation

### 4b: Phase Modulation Synthesis

**Description:** Technically, the DX7 and most "FM" synthesizers actually use phase modulation (PM), which is mathematically related but not identical to true FM. The modulator affects the phase rather than the frequency directly.

**Distinction:**
- FM: Modulator signal added to frequency input
- PM: Modulator signal added to phase input

In practice, the sonic results are very similar, and PM has implementation advantages.

### 4c: Exponential FM Synthesis

**Description:** The modulation is applied exponentially (1V/octave style), causing the modulation depth to scale with the carrier frequency. This maintains consistent timbral character across the keyboard.

### 4d: Dynamic FM Synthesis

**Description:** The modulation index changes over time, typically controlled by an envelope. This creates evolving timbres where brightness changes independently of amplitude.

**JavaScript Implementation:**

```javascript
class DynamicFM {
    playDynamicNote(carrierFreq, modRatio, maxModIndex, duration) {
        const modFreq = carrierFreq * modRatio;
        
        const carrier = this.ctx.createOscillator();
        const modulator = this.ctx.createOscillator();
        const modGain = this.ctx.createGain();
        const output = this.ctx.createGain();
        
        carrier.frequency.value = carrierFreq;
        modulator.frequency.value = modFreq;
        
        // Dynamic modulation envelope
        const now = this.ctx.currentTime;
        modGain.gain.setValueAtTime(maxModIndex * modFreq, now);
        modGain.gain.exponentialRampToValueAtTime(0.1, now + duration);
        
        modulator.connect(modGain);
        modGain.connect(carrier.frequency);
        carrier.connect(output);
        output.connect(this.ctx.destination);
        
        carrier.start(now);
        modulator.start(now);
        carrier.stop(now + duration);
        modulator.stop(now + duration);
    }
}
```

### 4e: Feedback FM Synthesis

**Description:** An operator modulates itself, creating self-modifying feedback that produces noise-like or harsh timbres. The DX7's operators 1-6 all have self-feedback capability.

### 4f: Formant FM Synthesis

**Description:** FM parameters are tuned to produce vowel-like formants. By careful selection of carrier:modulator ratios and modulation indices, speech-like qualities emerge.

---

## 5. SUBTRACTIVE SYNTHESIS

**Wikipedia Reference:** https://en.wikipedia.org/wiki/Subtractive_synthesis

Subtractive synthesis starts with harmonically rich waveforms and uses filters to remove (subtract) specific frequencies, sculpting the timbre. This is the most common analog synthesis paradigm.

### 5a: Classic Subtractive Synthesis

**Description:** The fundamental approach: oscillator → filter → amplifier, with optional LFO modulation and envelope control.

**Signal Flow:**
1. **Oscillator:** Generates harmonically rich waveform (saw, square, pulse)
2. **Filter:** Removes frequencies to shape timbre
3. **Amplifier:** Controls volume envelope (ADSR)

**JavaScript Implementation:**

```javascript
class SubtractiveSynth {
    constructor(audioContext) {
        this.ctx = audioContext;
    }

    createVoice() {
        // Oscillator with rich harmonics
        const osc = this.ctx.createOscillator();
        osc.type = 'sawtooth';
        
        // Filter
        const filter = this.ctx.createBiquadFilter();
        filter.type = 'lowpass';
        filter.frequency.value = 2000;
        filter.Q.value = 5;
        
        // Amplitude envelope
        const ampEnv = this.ctx.createGain();
        ampEnv.gain.value = 0;
        
        // Connect signal chain
        osc.connect(filter);
        filter.connect(ampEnv);
        ampEnv.connect(this.ctx.destination);
        
        return { osc, filter, ampEnv };
    }

    playNote(frequency, filterCutoff, filterQ, attack, decay, sustain, release) {
        const voice = this.createVoice();
        const now = this.ctx.currentTime;
        
        voice.osc.frequency.value = frequency;
        voice.filter.frequency.value = filterCutoff;
        voice.filter.Q.value = filterQ;
        
        // ADSR envelope
        voice.ampEnv.gain.setValueAtTime(0, now);
        voice.ampEnv.gain.linearRampToValueAtTime(1, now + attack);
        voice.ampEnv.gain.linearRampToValueAtTime(sustain, now + attack + decay);
        
        voice.osc.start(now);
        
        return voice;
    }

    releaseNote(voice, releaseTime) {
        const now = this.ctx.currentTime;
        voice.ampEnv.gain.cancelScheduledValues(now);
        voice.ampEnv.gain.setValueAtTime(voice.ampEnv.gain.value, now);
        voice.ampEnv.gain.linearRampToValueAtTime(0, now + releaseTime);
        voice.osc.stop(now + releaseTime);
    }
}
```

**GitHub Resources:**
- [subtract.one](https://jakichen.github.io/subtract.one/) - Analog-inspired subtractive synth
- [oliverbaptiste/soundsurgeon](https://github.com/oliverbaptiste/soundsurgeon) - Keyboard-based subtractive synth
- [dobrian's Web Audio tutorials](https://dobrian.github.io/cmp/topics/building-a-synthesizer-with-web-audio-api/)

### 5b: Low-Pass Filter Subtractive Synthesis

**Description:** The most common subtractive approach. A low-pass filter removes high frequencies, with cutoff frequency and resonance (Q) as primary timbral controls.

**Filter Characteristics:**
- **Cutoff Frequency:** Frequencies above this point are attenuated
- **Resonance (Q):** Emphasis at cutoff frequency
- **Slope:** Rate of attenuation (12dB/oct, 24dB/oct, etc.)

**JavaScript with Filter Envelope:**

```javascript
class LowPassSubtractive {
    playWithFilterEnvelope(freq, options) {
        const { 
            filterAttack = 0.1, 
            filterDecay = 0.3, 
            filterSustain = 0.3,
            envAmount = 3000,
            baseCutoff = 200 
        } = options;

        const osc = this.ctx.createOscillator();
        const filter = this.ctx.createBiquadFilter();
        const amp = this.ctx.createGain();
        
        osc.type = 'sawtooth';
        osc.frequency.value = freq;
        
        filter.type = 'lowpass';
        filter.Q.value = 8;
        
        // Filter envelope
        const now = this.ctx.currentTime;
        filter.frequency.setValueAtTime(baseCutoff, now);
        filter.frequency.linearRampToValueAtTime(
            baseCutoff + envAmount, 
            now + filterAttack
        );
        filter.frequency.linearRampToValueAtTime(
            baseCutoff + envAmount * filterSustain, 
            now + filterAttack + filterDecay
        );
        
        osc.connect(filter);
        filter.connect(amp);
        amp.connect(this.ctx.destination);
        
        osc.start(now);
    }
}
```

### 5c: High-Pass Filter Subtractive Synthesis

**Description:** Removes low frequencies, useful for thinning sounds or creating telephone/radio effects.

### 5d: Band-Pass Filter Subtractive Synthesis

**Description:** Allows only a narrow band of frequencies through, creating resonant, vowel-like tones. Useful for formant synthesis and wah effects.

### 5e: Notch Filter Subtractive Synthesis

**Description:** Removes a specific frequency band while passing everything else. Creates phase-cancellation effects similar to flanging.

### 5f: Resonance-Enhanced Subtractive Synthesis

**Description:** High resonance values cause self-oscillation at the cutoff frequency, adding a pitched component to the filter. Classic technique for acid-style basslines.

**Web Audio BiquadFilter Types:**
- `lowpass` - Low-pass filter
- `highpass` - High-pass filter
- `bandpass` - Band-pass filter
- `notch` - Notch filter
- `lowshelf` - Low shelf EQ
- `highshelf` - High shelf EQ
- `peaking` - Parametric EQ
- `allpass` - All-pass filter (for phasing)

---

## 6. JAVASCRIPT LIBRARIES & RESOURCES

### Primary Framework: Tone.js

**Website:** https://tonejs.github.io/
**GitHub:** https://github.com/Tonejs/Tone.js

Tone.js is the most comprehensive Web Audio framework, providing:
- Pre-built synthesizers (Synth, FMSynth, AMSynth, NoiseSynth, PluckSynth, etc.)
- Effects (Reverb, Delay, Distortion, Filter, Chorus, Phaser, etc.)
- Scheduling and transport (sample-accurate timing)
- Signal processing primitives
- Polyphony management

**Key Synthesizers in Tone.js:**

```javascript
// Basic synthesizer
const synth = new Tone.Synth().toDestination();
synth.triggerAttackRelease("C4", "8n");

// FM Synthesizer
const fmSynth = new Tone.FMSynth({
    harmonicity: 3,
    modulationIndex: 10,
    oscillator: { type: "sine" },
    modulation: { type: "square" }
}).toDestination();

// AM Synthesizer
const amSynth = new Tone.AMSynth().toDestination();

// Plucked String (Karplus-Strong)
const pluckSynth = new Tone.PluckSynth({
    attackNoise: 1,
    dampening: 4000,
    resonance: 0.98
}).toDestination();

// Membrane Synth (drums)
const membraneSynth = new Tone.MembraneSynth().toDestination();

// Metal Synth (bells, cymbals)
const metalSynth = new Tone.MetalSynth().toDestination();

// Polyphonic
const polySynth = new Tone.PolySynth(Tone.Synth).toDestination();
```

### Additional Libraries

| Library | Description | GitHub |
|---------|-------------|--------|
| **granular-js** | Simple granular synthesis | philippfromme/granular-js |
| **fm-synth** | Configurable FM synthesis | mohayonao/fm-synth |
| **genish.js** | Per-sample DSP processing | charlieroberts/genish.js |
| **WAAClock** | Precise scheduling | sebpiq/WAAClock |
| **tuna** | Audio effects library | Theodeus/tuna |
| **pizzicato** | Simplified Web Audio | alemangui/pizzicato |
| **howler.js** | Audio playback with spatial audio | goldfire/howler.js |
| **dsp-kit** | FFT, filters, analysis | oramics/dsp-kit |
| **PhaseVocoderJS** | Time stretching/pitch shifting | echo66/PhaseVocoderJS |

### Native Web Audio API Key Nodes

| Node | Purpose |
|------|---------|
| `OscillatorNode` | Generate waveforms (sine, square, sawtooth, triangle, custom) |
| `GainNode` | Amplitude control, mixing |
| `BiquadFilterNode` | Various filter types (LP, HP, BP, notch, etc.) |
| `AudioBufferSourceNode` | Play audio buffers |
| `ConvolverNode` | Convolution reverb |
| `DelayNode` | Time delay |
| `WaveShaperNode` | Distortion, saturation |
| `DynamicsCompressorNode` | Compression/limiting |
| `AnalyserNode` | FFT analysis, visualization |
| `AudioWorkletNode` | Custom DSP processing |
| `PeriodicWave` | Custom wavetables for oscillators |

---

## 7. SOS IMPLEMENTATION CONSIDERATIONS

### Mapping Seismic Data to Synthesis Parameters

The SOS project transforms real-time seismic waveform data into electronica music. Here are recommended mappings:

| Seismic Parameter | Synthesis Target | Rationale |
|-------------------|------------------|-----------|
| **Magnitude** | Modulation depth, filter intensity | Higher magnitude = more dramatic processing |
| **Depth** | Filter cutoff, reverb amount | Deep events = darker, more reverberant |
| **Distance** | Spatial positioning, delay time | Epicentral distance affects perceived location |
| **P-wave/S-wave ratio** | Harmonic content, waveform selection | Different wave types suggest different timbres |
| **Frequency content** | Direct pitch mapping, formant frequencies | Seismic spectra inform musical spectra |
| **Waveform amplitude** | Grain density, envelope dynamics | Direct energy mapping |

### Recommended Synthesis Approaches for SOS

1. **Granular Synthesis** - Ideal for transforming long seismic waveforms into musical textures. Seismic data can serve as source material for granulation.

2. **Pulsar Synthesis** - Bridges the gap between seismic "events" and continuous tones. Perfect for representing the transition from individual earthquakes to ongoing seismic activity.

3. **FM Synthesis** - Excellent for creating bell-like and percussive sounds that can represent sudden seismic events. Modulation index can map to magnitude.

4. **Spectral Resynthesis** - Analyze seismic waveforms via FFT and resynthesize with additive oscillators, allowing selective emphasis of specific frequency bands.

5. **Karplus-Strong** - The delay-based physical model could be extended to simulate wave propagation through earth materials.

### Technical Architecture for Real-Time Processing

```javascript
// SOS Seismic Sonification Pipeline Example
class SeismicSonifier {
    constructor(audioContext) {
        this.ctx = audioContext;
        this.granular = new GranularSynthesizer(audioContext);
        this.fm = new FMSynthesizer(audioContext);
        this.filter = this.ctx.createBiquadFilter();
        this.reverb = new ConvolutionReverb(audioContext);
    }

    processSeismicData(waveformData, metadata) {
        const { magnitude, depth, distance } = metadata;
        
        // Map seismic parameters to synthesis
        const grainDensity = this.mapRange(magnitude, 0, 9, 5, 100);
        const filterCutoff = this.mapRange(depth, 0, 700, 8000, 200);
        const reverbMix = this.mapRange(distance, 0, 10000, 0.1, 0.9);
        
        // Configure synthesizers
        this.granular.setDensity(grainDensity);
        this.filter.frequency.value = filterCutoff;
        this.reverb.setWetLevel(reverbMix);
        
        // Convert seismic waveform to audio buffer
        const audioBuffer = this.seismicToAudioBuffer(waveformData);
        
        // Process through synthesis chain
        this.granular.loadBuffer(audioBuffer);
        this.granular.start();
    }

    mapRange(value, inMin, inMax, outMin, outMax) {
        return outMin + (value - inMin) * (outMax - outMin) / (inMax - inMin);
    }
}
```

---

## References

### Academic Papers
- Roads, Curtis. "Sound Composition with Pulsars." *Journal of the Audio Engineering Society* 49.3 (2001): 134-147.
- Chowning, John. "The Synthesis of Complex Audio Spectra by Means of Frequency Modulation." *Journal of the Audio Engineering Society* 21.7 (1973): 526-534.
- Karplus, Kevin, and Alex Strong. "Digital Synthesis of Plucked-String and Drum Timbres." *Computer Music Journal* 7.2 (1983): 43-55.

### Books
- Roads, Curtis. *Microsound*. MIT Press, 2001.
- Roads, Curtis. *The Computer Music Tutorial*. MIT Press, 1996.
- Smus, Boris. *Web Audio API*. O'Reilly Media, 2013.
- Smith, Julius O. *Spectral Audio Signal Processing*. W3K Publishing, 2011.

### Online Resources
- [MDN Web Audio API Documentation](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)
- [Web Audio API Specification](https://www.w3.org/TR/webaudio/)
- [Tone.js Documentation](https://tonejs.github.io/docs/)
- [teropa's Web Audio Series](https://teropa.info/blog/2016/08/19/what-is-the-web-audio-api.html)
- [Web Audio Weekly Newsletter](https://www.webaudioweekly.com/)

---

*Document prepared for the Sounds of Seismic (SOS) project at https://sos.allshookup.org/*
*Electronica Earthwork: Transforming Real-Time Seismic Data into Generative Music*
