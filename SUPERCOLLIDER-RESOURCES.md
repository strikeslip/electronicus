# SuperCollider Resources
## Classic Synthesizer Emulation & Synthesis Techniques

*Compiled: December 25, 2024*  
*Claude Sonnet 4.5*

---

## TABLE OF CONTENTS

1. [SuperCollider Overview](#supercollider-overview)
2. [DX7 FM Synthesis](#dx7-fm-synthesis)
3. [Analog Synthesis (Moog, Prophet-5, Jupiter-8, OB-Xa)](#analog-synthesis)
4. [Wavetable Synthesis (PPG Wave, Korg M1)](#wavetable-synthesis)
5. [Physical Modeling (Buchla, ARP 2600)](#physical-modeling)
6. [Granular Synthesis](#granular-synthesis)
7. [Tutorials & Learning Resources](#tutorials-learning-resources)
8. [QUARKS - Extension Packages](#quarks-extension-packages)
9. [Key UGens by Synthesis Type](#key-ugens-by-synthesis-type)
10. [Community Resources](#community-resources)

---

## SUPERCOLLIDER OVERVIEW

### What is SuperCollider?

SuperCollider is a **free, open-source platform** for audio synthesis and algorithmic composition created by James McCartney. It consists of:

**Three Core Components:**
1. **scsynth** - Real-time audio server with 250+ UGens (Unit Generators)
2. **sclang** - Interpreted programming language for controlling the server
3. **scide** - Integrated development environment with help system

**Alternative:**
- **supernova** - Multi-threaded server for multi-core CPUs

**Key Features:**
- Cross-platform (Windows, macOS, Linux, Raspberry Pi, Bela)
- Real-time and non-real-time synthesis
- Pattern-based composition system
- Extensive library of scales/tunings
- Built-in package manager (Quarks)
- Free/Libre Open Source (GPL v3)

**Architecture:**
- Written in C++17
- Uses Qt and Boost libraries
- Client-server architecture
- Supports C/C++ plugin APIs

---

## DX7 FM SYNTHESIS

### Complete DX7 Emulation

**egegonul's DX7 Emulator** ⭐ **ESSENTIAL**
- Platform: sccode.org/egegonul
- GitHub: https://github.com/everyt...
- Features:
  - **16,384 presets** (collection of original DX7 sysex files from internet)
  - Almost perfect emulation of original DX7 unit
  - Blind tests are hard to distinguish from hardware
  - Random preset generator for each node
- SoundCloud Demo: https://soundcloud.com/ewbta/dx-7-..
- File: `dx7.afx` (ready-to-go version with preset file)
- Installation: Download from GitHub repository
- Note: MIDI integration reported with some difficulty

### FM Synthesis Techniques (General)

**Bruno Ruviaro's FM Examples**
- sccode.org (search: "Bruno Ruviaro FM")
- Tutorial Code: FM synthesis techniques, frequency modulation, frequency ratio
- Features: Interactive FM experimentation with contours
  - Carrier Frequency envelope
  - Modulation Index envelope
  - Amplitude Envelope
- Tag: `fm synthesis techniques frequency modulation`

**Key FM UGens:**
- `GrainFM` - Granular synthesis with FM sine tones
- `FMGrain`, `FMGrainI`, `FMGrainB` - Various FM granular synthesis UGens
- `PMOsc` - Phase modulation oscillator
- `SinOsc` - Basic sine oscillator (carrier/modulator)

**FM Synthesis Pattern:**
```supercollider
// Basic 2-operator FM
(
SynthDef(\fm2op, {
    |out=0, freq=440, modfreq=440, modindex=1, amp=0.3|
    var carrier, modulator, sig;
    
    modulator = SinOsc.ar(modfreq, 0, modindex);
    carrier = SinOsc.ar(freq + modulator, 0, amp);
    
    Out.ar(out, carrier ! 2);
}).add;
)

// Trigger
Synth(\fm2op, [\freq, 220, \modfreq, 440, \modindex, 200]);
```

**Eli Fieldsteel FM Tutorial:**
- YouTube Tutorial 22: https://youtu.be/dLMSR2Kjq6Y
- sccode.org/elifieldsteel - Pattern-based FM synthesis sketches
- Tags: `bass complex drone rich waveshaping wavetable`

---

## ANALOG SYNTHESIS

### Moog Filter Emulations ⭐

**MoogVCF** (Moog Diode Ladder Filter)
- Class: `MoogVCF`
- Documentation: doc.sccode.org/Classes/MoogVCF.html
- Based on: CSound opcode by Josep Comajuncosas & Hans Mikelson
- Implementation: Digital emulation of Moog diode ladder configuration
- Reference: Stilson & Smith paper "Analyzing the Moog VCF with Considerations for Digital Implementation"
- Arguments: `MoogVCF.ar(in, ffreq, res, mul, add)`
  - `ffreq` - Filter frequency
  - `res` - Resonance (0-4, self-oscillates at high values)

```supercollider
// Classic Moog bass
{
    var sig = MoogVCF.ar(
        Mix(LFSaw.ar([120, 180], 0, 0.33)),
        LinExp.kr(LFCub.kr(0.1, 0.5*pi), -1, 1, 280, 1500),
        0.75,  // resonance
        mul: 2.0
    );
    sig ! 2
}.play;
```

**MoogLadder** (Alternative Implementation)
- Class: `MoogLadder`
- Source: sc3-plugins (BhobUGens)
- Based on: Code by Victor Lazzarini and Antti Huovilainen
- Generally smoother than MoogVCF

```supercollider
{
    MoogLadder.ar(
        Mix(LFSaw.ar([120, 180], 0, 0.33)),
        LinExp.kr(LFCub.kr(0.1, 0.5*pi), -1, 1, 280, 1500),
        0.75,  // resonance
        mul: 3.5
    ).dup
}.play;
```

**Other Analog-Style Filters:**

**DFM1** (Digitally Modelled Analog Filter)
- Class: `DFM1`
- Features: 
  - Low-pass and high-pass filtering
  - Can be overdriven
  - Self-oscillates at high resonances
  - Noise level parameter
- Usage: `DFM1.ar(in, freq, res, inputgain, type, noiselevel)`

**RLPFD** (TB-303 Style)
- Emulates Roland TB-303 filter
- Arguments: `RLPFD.ar(in, cutoff, res, dist)`
- Resonance and distortion are related
- Can become "nasty" when cutoff, res, and dist are all high

**SVF** (State Variable Filter)
- Class: `SVF`
- Features: Simultaneous outputs for multiple filter types
- Arguments: `SVF.ar(signal, cutoff, res, lowpass, bandpass, highpass, notch, peak)`
- Mix outputs for creative filtering

### Analog Synthesis UGens (sc3-plugins)

**MKPlugins Collection** (Mads Kjeldgaard)
- `AnalogBassDrum` - Analog bass drum synthesis
- `AnalogSnareDrum` - Analog snare drum synthesis
- `AnalogFoldOsc` - Analog-style folding oscillator
- `AnalogPhaser`, `AnalogPhaserMod` - Analog phaser effects
- `AnalogTape` - Analog tape saturation
- `AnalogVintageDistortion` - Vintage distortion
- `LPG` - Low-pass gate (Buchla-style)
- `VadimFilter` - Analog-modeled filter
- Documentation: doc.sccode.org/HelpSource/Overview/MKPlugins.html

**Bandlimited Oscillators** (Better than standard for analog sounds)
- `SawDPW` - Anti-aliased sawtooth (sc3-plugins)
- `PulseDPW` - Anti-aliased pulse wave
- `DPW3Tri` - Anti-aliased triangle
- `DPW4Saw` - 4th-order anti-aliased sawtooth

```supercollider
// Classic subtractive analog synth
(
SynthDef(\analogSynth, {
    |out=0, freq=220, cutoff=1000, res=0.5, amp=0.3|
    var sig, env, fenv;
    
    // Oscillators (3 VCOs like Minimoog)
    sig = Mix([
        Saw.ar(freq),
        Saw.ar(freq * 1.01),  // slight detune
        Pulse.ar(freq * 0.5, 0.5)
    ]) * 0.3;
    
    // Filter envelope
    fenv = EnvGen.kr(Env.adsr(0.01, 0.3, 0.5, 0.5));
    
    // Moog filter
    sig = MoogVCF.ar(sig, cutoff * fenv, res);
    
    // Amp envelope
    env = EnvGen.kr(Env.adsr(0.01, 0.1, 0.7, 0.3), doneAction: 2);
    
    Out.ar(out, sig * env * amp ! 2);
}).add;
)

// Play
Synth(\analogSynth, [\freq, 55.midicps, \cutoff, 2000, \res, 0.8]);
```

### Detuned Pad (Prophet-5 / Jupiter-8 Style)

**Detuned Synth Pad Example**
- sccode.org/1-4YS
- Implementation of synth sound from Attack Magazine
- Article: "http://www.attackmagazine.com/technique/synth-secrets/detuned-pad/"
- Technique: Multiple detuned oscillators for lush pad sounds

```supercollider
// Detuned pad (Jupiter-8 / Prophet-5 style)
(
SynthDef(\detunedPad, {
    |out=0, freq=220, detune=0.05, amp=0.2|
    var sig, env;
    
    // 8 detuned oscillators (like Jupiter-8 polyphony)
    sig = Mix.fill(8, { |i|
        Saw.ar(freq * (1 + (detune * (i - 3.5) / 3.5)))
    }) * 0.125;
    
    // Chorus-like filter movement
    sig = RLPF.ar(sig,
        freq * 4 + SinOsc.kr(0.1, i/8*2pi, freq * 2),
        0.3
    );
    
    env = EnvGen.kr(Env.adsr(0.5, 0.5, 0.7, 1.0), doneAction: 2);
    
    Out.ar(out, sig * env * amp ! 2);
}).add;
)
```

---

## WAVETABLE SYNTHESIS

### Wavetable Classes & Methods

**Wavetable Class**
- Documentation: doc.sccode.org/Classes/Wavetable.html
- Type: FloatArray in special format for interpolating oscillators
- Cannot be created with `new`

**Key Methods:**

`Wavetable.sineFill(size, amplitudes, phases)`
- Fill wavetable with sum of sines
- Normalized automatically
- Example:
```supercollider
b = Buffer.alloc(s, 512);
b.setn(0, Wavetable.sineFill(512, [1, 0.5, 0.25, 0.125]));
```

`Wavetable.chebyFill(size, amplitudes, normalize, zeroOffset)`
- Fill with Chebyshev polynomials for waveshaping
- Used with `Shaper` UGen

**Wavetable Oscillators:**
- `Osc` - Wavetable lookup oscillator
- `VOsc` - Variable wavetable oscillator (single buffer select)
- `VOsc3` - Three VOscs summed (wavetable morphing)
- `COsc` - Chorusing wavetable oscillator

**Important:** Wavetables use a special format - not linear interpolation, but "integer part -1" and "1+frac" for efficient C code float conversion.

### Comprehensive Wavetable Synthesis (Eli Fieldsteel)

**Wavetable Synthesis Patterns** ⭐
- sccode.org/1-5bF
- YouTube Tutorial 23: https://youtu.be/8EK9sq_9gFI
- Tags: `rhythmic patterns endless dark synthesis pentatonic wavetable synthesis futuristic dystopian`

**Features:**
- 10 wavetables with increasing complexity
- Envelope-based wavetable generation
- Progressive timbral evolution (simple → complex)
- Pattern-based sequencing
- Reverb and effects routing

**Code Excerpt:**
```supercollider
// Create 10 wavetables with increasing complexity
~wt_sig = 10.collect({ |i|
    var numSegs = i.linexp(0, 9, 4, 40).round;
    Env(
        [0] ++ ({1.0.rand}.dup(numSegs-1) * [1,-1]).scramble ++ [0],
        {exprand(1, i.linexp(0, 9, 1, 50))}.dup(numSegs),
        {[\sine, 0, exprand(1,20) * [1,-1].choose]
            .wchoose([9-i, 3, i].normalizeSum)
        }.dup(numSegs)
    ).asSignal(1024);
});

// Load into buffers as wavetables
~wt_buf = Buffer.allocConsecutive(10, s, 2048, 1, { |buf, index|
    buf.setnMsg(0, ~wt_sig[index].asWavetable);
});
```

### Wavetable Morphing/Interpolation

**Example: Morphing Between Two Wavetables**
- sccode.org/1-4V1
- Author: totalgee
- Technique: Single-buffering with incremental updates
- Avoids: "Pulsing" effect of double-buffering with XFade2

```supercollider
// Morph between two envelopes/wavetables
SynthDef(\oscplayer, {
    arg out = 0, bufnum;
    var signal;
    signal = Osc.ar(bufnum, MouseX.kr(60, 300));
    Out.ar(out, signal ! 2);
}).add;

// Create morphing routine
fork {
    var steps = 50;
    var env1 = Env([0,1,0], [1,1]).asSignal(1024);
    var env2 = Env([0,-1,0], [1,1]).asSignal(1024);
    
    steps.do { |i|
        var blend = i / steps;
        var morphed = env1.blend(env2, blend);
        ~buf.loadCollection(morphed.asWavetable);
        0.1.wait;
    };
};
```

### PADsynth Algorithm (Spectral Wavetable)

**PADsynth SuperCollider Implementation**
- sccode.org/1-58B
- Algorithm: Paul Nasca's PADsynth for rich, organic timbres
- Features:
  - Spectral smearing of partials
  - Bandwidth parameter
  - Spread parameter (generates extra partials)
  - FFT-based synthesis

```supercollider
// PADsynth generates wavetables from partial specifications
prepareSingleBuffer = { |partials, min_length, spread, reference_note|
    // partials: [partial_idx, amplitude, partial_idx, amplitude, ...]
    // Creates rich, inharmonic timbres
    // Returns wavetable buffer
};
```

---

## PHYSICAL MODELING

### Digital Waveguide Models (sc3-plugins)

**DWGPlucked** - Karplus-Strong Plucked String
- Class: `DWGPlucked`
- Documentation: doc.sccode.org/Classes/DWGPlucked.html
- Source: sc3-plugins (DWGUGens)
- Model: Digital waveguide of plucked string

**DWGPlucked2** - Enhanced Version
- Improved plucked string model
- Used in: Digital Harp examples (sccode.org)
- Author examples: akselai (24 Mar'24)

**Other DWG UGens:**
- `DWGBowed` - Bowed string
- `DWGBowedSimple` - Simplified bowed string
- `DWGBowedTor` - Torsional bowed string
- `DWGPluckedStiff` - Stiff string model
- `DWGSoundBoard` - Resonant soundboard

```supercollider
// Simple plucked string (Karplus-Strong)
{
    var sig = DWGPlucked.ar(
        freq: 440,
        amp: 0.5,
        gate: 1,
        pos: 0.14,  // pluck position
        c1: 0.1,    // damping coefficient 1
        c3: 0.1     // damping coefficient 3
    );
    sig ! 2
}.play;
```

**Buchla-Style Low-Pass Gate:**
- UGen: `LPG` (in MKPlugins)
- Combines: VCA + low-pass filter (characteristic of Buchla systems)
- Use: Natural, organic attack/decay envelopes

### Formant Synthesis

**DynKlang** - Bank of Sine Oscillators
- Perfect for: Vowel synthesis, bell-like timbres
- sccode.org examples: "Vowel synthesis even/odd Harmonics with DynKlang"
- Paper reference: "A SUPERCOLLIDER CLASS FOR VOWEL SYNTHESIS"

```supercollider
// Vowel formant synthesis
{
    DynKlang.ar(`[
        [400, 800, 2600],      // frequencies (formants)
        [1, 0.5, 0.1],         // amplitudes
        nil                     // phases
    ]) * 0.1 ! 2
}.play;
```

**Klank** - Bank of Resonators
- Modal synthesis (struck/plucked objects)
- Example: Bell synthesis (Bruno Ruviaro, sccode.org)

```supercollider
// Bell using Klank (inharmonic partials)
{
    var freqs = [1, 1.67, 2.01, 2.67, 3.01, 4.16];
    var amps = [1, 0.9, 0.8, 0.7, 0.6, 0.5];
    var rings = [1, 0.9, 0.8, 0.7, 0.6, 0.5];
    
    Klank.ar(
        `[freqs, amps, rings],
        Impulse.ar(0),  // excitation
        440
    ) * 0.1 ! 2
}.play;
```

---

## GRANULAR SYNTHESIS

### Granular UGens (Built-in)

**GrainBuf** - Buffer-based Granular Synthesis
- Documentation: doc.sccode.org/Classes/GrainBuf.html
- Features: Reads from audio buffer with granular sampling
- Arguments: numChannels, trigger, dur, bufnum, rate, pos, interp, pan, envbuf

**GrainSin** - Sine Grain Synthesis
**GrainFM** - FM Grain Synthesis
**GrainIn** - Live input granulation

**TGrains** - Triggered Buffer Granulation
- Arguments: numChannels, trigger, bufnum, rate, centerPos, dur, pan, amp, interp
- Simpler than GrainBuf for basic granulation

```supercollider
// Basic granular synthesis
(
SynthDef(\grains, {
    |out=0, buf=0, rate=1, pos=0, dur=0.1, density=20|
    var sig = GrainBuf.ar(
        2,                              // stereo
        Impulse.ar(density),           // grain trigger
        dur,                            // grain duration
        buf,
        rate,
        pos,                            // buffer position
        2,                              // interpolation
        Pan2.ar(WhiteNoise.ar, LFNoise1.kr(2))  // random pan
    );
    Out.ar(out, sig * 0.5);
}).add;
)
```

### Advanced Granular Examples

**Paulstretch for SuperCollider**
- sccode.org
- Extreme time-stretching algorithm
- Comments: "Wonderful, adapted for live looping" (jimmy, 21 Oct'25)

**Graphic Granular Time-Stretching**
- sccode.org/1-4Ye (jamshark70, 26 Apr'15)
- Tags: `granular synthesis techniques pedagogy`
- Features: Visual representation of grain windows
- Interactive "ratio" slider for time-stretching demonstration
- Educational: Shows time-domain granular process graphically

**GUI Granular Module**
- sccode.org (blueprint, 15 Feb'18)
- Tags: `live loop grains granular`
- Controls: amplitude, trigger rate, grain duration, simultaneous grains, frequency

**Granular Categories on sccode.org:**
- Tag: `category/granular`
- Examples: Drone synthesis, live recording loops, swarm effects
- Notable: "Hello World, I Am Lonely Too" by Schemawound
  - Ambient generative granular piece

---

## TUTORIALS & LEARNING RESOURCES

### Primary Tutorial Series ⭐

**1. Eli Fieldsteel - Video Tutorials** ⭐⭐⭐ **HIGHLY RECOMMENDED**
- Platform: YouTube
- Channel: University of Illinois
- Series: SuperCollider Tutorial (2013+)
- Recent Course: MUS 499C Coding with SuperCollider (Fall 2020)
- Website: http://www.elifieldsteel.com/projects/
- sccode.org Profile: sccode.org/elifieldsteel

**Highlighted Tutorials:**
- Tutorial 22: Pattern-based FM Synthesis (https://youtu.be/dLMSR2Kjq6Y)
- Tutorial 23: Wavetable Synthesis (https://youtu.be/8EK9sq_9gFI)
- Topics: Patterns, JITLib, GUI, synthesis techniques

**Eli Fieldsteel Projects:**
- **GranulatorEF** - Granular synthesis class with MIDI learn
- **MashupMachine** - Live loop synchronization tool
  - Requirements: Max 5 subfolders, files ending in "-2", "-4", or "-8" for measure length

**2. Bruno Ruviaro - A Gentle Introduction to SuperCollider** ⭐⭐⭐
- Format: Free ebook (Open Access)
- Host: CCRMA, Stanford University
- URL: https://ccrma.stanford.edu/~ruviaro/texts/A_Gentle_Introduction_To_SuperCollider.pdf
- Audience: Complete beginners
- Topics: Basics, SynthDefs, Patterns, GUI
- sccode.org Profile: Search "Bruno Ruviaro"

**Notable Examples:**
- Bell synthesis with Ndef (19 Nov'25)
- FM synthesis techniques
- Guitar/plucked string (Karplus-Strong)

**3. Nick Collins - 12-Week Tutorial**
- Format: HTML browseable + downloadable ZIP
- Website: ComposerProgrammer.com
- Content: Comprehensive 12-week course
- Status: Classic reference

**4. David Michael Cottle - Computer Music with Examples**
- Free resource
- Available: SC Wiki
- Format: Extensive written examples

### Books

**The SuperCollider Book** (Wilson, Cottle & Collins, 2011)
- Publisher: MIT Press
- Essential Chapters:
  - Ch. 1: Beginner's Tutorial (David Michael Cottle)
  - Ch. 2: The Unit Generator (Joshua Parmenter)
  - Ch. 3: Composition with SuperCollider (Scot Wilson & Julio d'Escrivn)
  - Ch. 5: Programming in SuperCollider (Iannis Zannos)
  - Ch. 6: Events and Patterns (Ron Kuivila)
  - Ch. 13: Sonification and Auditory Display (Alberto deCampo et al.)
  - Ch. 17: Alternative Tunings (Fabrice Mogini)

**Introduction to SuperCollider** (Andrea Valle, 2016)
- Publisher: Logos
- Berlin

### Community Tutorials

**Getting Started with SC** (Scott Wilson & James Harkins)
- Location: SC Wiki
- Official getting started guide

**howto_co34pt_liveCode** (co34pt)
- Focus: Live coding in SuperCollider
- GitHub: Companion repo with code, examples, samples
- URL: https://theseanco.github.io/howto_co34pt_liveCode/

**Fun Programming.org**
- Video tutorials
- Topics: Creative coding with SuperCollider and Processing

**miSCellaneous lib**
- Tutorials for miSCellaneous library
- Focus: Patterns and advanced techniques

### Academic Papers

**McCartney, James (2002)**
- "Rethinking the Computer Music Language: SuperCollider"
- Computer Music Journal Vol. 26, No. 4, Winter
- Pages: 61-68
- JSTOR

---

## QUARKS - EXTENSION PACKAGES

### What are Quarks?

**Definition:** Community-contributed packages containing:
- Classes (language extensions)
- Extension methods
- Documentation
- UGen plugins

**Installation:**
```supercollider
// Install from directory
Quarks.install("QuarkName");

// Or use GUI
Quarks.gui;

// From GitHub URL
Quarks.install("https://github.com/user/repo.git");

// From local folder
Quarks.install("~/path/to/quark");

// Uninstall
Quarks.uninstall("QuarkName");
```

**After Installation:** Recompile Class Library (Language > Recompile)

### Essential Quarks ⭐

**SCLOrkSynths** ⭐ **RECOMMENDED**
- Repository: https://github.com/SCLOrkHub/SCLOrkSynths
- Description: Collection of ready-to-use SynthDefs for Patterns
- Installation: `Quarks.install("SCLOrkSynths");`
- Features:
  - Standardized arguments (freq, amp, out, pan, gate, att, rel)
  - doneAction: 2 by default
  - Pattern-ready
  - Metadata model from SynthDefPool

**Source Credits:**
- SynthDefPool (@crucialfelix) - https://github.com/supercollider-quarks/SynthDefPool
- sccode.org - Many community SynthDefs
- Original SynthDefs by project contributors

**Authors:**
- Bruno Ruviaro (@brunoruviaro) - Project lead
- Josh Mitchell (@joshmit) - Coding standards, new SynthDefs
- Meha Gupta (@mehagupta) - Initial gathering
- Diya Menon - Pattern demos

**BenoitLib** (Benoît and the Mandelbrots)
- Key Tool: **Pkr** - Pattern proxy for control-rate UGens in patterns
  - Essential for live performances
  - Synchronizes control signals
- Other: MandelHub, MandelClock (collaborative performance tools)

**Bjorklund** ⭐
- Implementation: Euclidean Rhythms
- Paper: "The Euclidean Algorithm Generates Traditional Musical Rhythms"
- Class: `Pbjorklund2` - Returns array of durations
- Usage: Dynamic, metric dance music rhythms
- Visualization: Use web apps to understand concept

**BatLib**
- **StageLimiter** ⭐ - Essential safety tool
  - Basic limiter across all server sounds
  - Prevents clipping beyond +/- 1
  - Creative sidechain effects when pushed hard
  - Recommendation: Always use unless specific reason not to

**Wavesets** (T. Wishart)
- Repository: https://github.com/supercollider-quarks/Wavesets
- Technique: Analysis and synthesis with soundfile fragments
- Based on: Trevor Wishart's compositional techniques

**Concat** / **Concat2**
- Concatenative synthesis tools
- Excellent for: Speech reconstruction, sampling
- Technique: Database-driven synthesis

### sc3-plugins (NOT a Quark - Separate Installation)

**Overview:**
- Repository: http://supercollider.github.io/sc3-plugins/
- Community collection of UGen plugins
- Extends SuperCollider with additional synthesis/analysis capabilities

**Installation:**
```bash
# Clone with submodules
git clone --recursive https://github.com/supercollider/sc3-plugins.git
git clone --recursive https://github.com/supercollider/supercollider.git

# Build
cd sc3-plugins
mkdir build && cd build
cmake -DSC_PATH=/path/to/sc/ -DCMAKE_BUILD_TYPE=Release ..
cmake --build . --config Release
sudo cmake --build . --config Release --target install
```

**Essential UGens from sc3-plugins:**
- `MoogLadder` - Moog filter emulation
- `DWGPlucked`, `DWGPlucked2` - Physical modeling
- `Decimator`, `SmoothDecimator` - Bitcrushing
- `SawDPW`, `PulseDPW` - Anti-aliased oscillators
- `DFM1` - Analog filter model
- `CrossoverDistortion` - Savage distortion
- MKPlugins (entire collection)

**Note on Linux:** May need to build without Ladspa if server boot issues occur

### Additional Quarks

**JITLibExtensions**
- Extensions for Just-In-Time library
- Live coding enhancements

**wslib**
- Various language additions
- GUI classes
- Features: SimpleMIDIFile, SVG, RoundButton, SmoothSlider, AutoBackup

**DissonanceLib**
- Psychoacoustic dissonance curves
- Harmonic analysis functions

**adclib**
- Small, general, or experimental classes

**Influx**
- "Lose Control, Gain Influence"
- Performance-oriented control

**SpeakersCorner**
- Multi-speaker setups
- Features: Latency compensation, EQ, MasterFX

**ListeningClocks**
- Interacting clocks
- Developed for: Virtual Gamelan Graz

---

## KEY UGENS BY SYNTHESIS TYPE

### Oscillators

**Basic:**
- `SinOsc` - Sine wave (wavetable-based, 8192 samples)
- `Saw` - Sawtooth (aliasing at high frequencies)
- `Pulse` - Pulse/square wave
- `LFSaw`, `LFTri`, `LFPar` - Low-frequency variants

**Band-Limited (No Aliasing):**
- `Blip` - Band-limited impulse train
- `SawDPW` - Digitally Perfect Waveform sawtooth (sc3-plugins)
- `PulseDPW` - DPW pulse wave

**Wavetable:**
- `Osc` - General wavetable oscillator
- `VOsc` - Variable wavetable (single buffer select)
- `VOsc3` - Three wavetables summed
- `COsc` - Chorusing wavetable

**Special:**
- `Gendy1` - Xenakis' dynamic stochastic synthesis
- `PMOsc` - Phase modulation
- `FSinOsc` - Fast sine oscillator

### Filters

**Moog-Style:**
- `MoogVCF` (built-in)
- `MoogLadder` (sc3-plugins)
- `BMoog` (alternative)

**Analog Models:**
- `DFM1` - Digitally modeled analog (sc3-plugins)
- `VadimFilter` (MKPlugins)
- `RLPFD` - TB-303 style

**Standard:**
- `RLPF` - Resonant low-pass
- `RHPF` - Resonant high-pass
- `BPF` - Band-pass
- `BRF` - Band-reject
- `SVF` - State variable (multi-output)

**High-Order:**
- `BLowPass`, `BLowPass4` - Butterworth low-pass (4-pole)
- `BHiPass`, `BHiPass4` - Butterworth high-pass

**Formant:**
- `Formlet` - FOF-like grain filter
- `Ringz` - Ringing filter

### Envelopes

**Standard:**
- `EnvGen` - General envelope generator
- `Env` - Envelope specification class
  - `Env.adsr(attack, decay, sustain, release)`
  - `Env.perc(attack, release)`
  - `Env.triangle(dur, level)`

**Demand:**
- `DemandEnvGen` - Demand-rate envelopes

### Effects

**Delay:**
- `DelayN`, `DelayL`, `DelayC` - No/linear/cubic interpolation
- `CombN`, `CombL`, `CombC` - Comb filters
- `AllpassN`, `AllpassL`, `AllpassC` - Allpass filters

**Reverb:**
- `FreeVerb` - Freeverb algorithm
- `GVerb` - Reverb with room size

**Distortion:**
- `CrossoverDistortion` (sc3-plugins)
- `Shaper` - Waveshaping
- `AnalogVintageDistortion` (MKPlugins)

**Modulation:**
- `AnalogPhaser` (MKPlugins)
- `Chorus` (requires custom implementation)

### Analysis

**FFT:**
- `FFT` - Fast Fourier Transform
- `IFFT` - Inverse FFT
- PV_* UGens - Phase vocoder processing

**Amplitude:**
- `Amplitude` - Amplitude follower
- `Amplitude Tracker

**Pitch:**
- `Pitch` - Pitch tracker

---

## COMMUNITY RESOURCES

### Official Channels

**Forum:**
- URL: https://scsynth.org
- Active community discussions
- Technical support

**Slack:**
- Real-time chat
- Development discussions

**Mailing Lists (Archives):**
- sc-users: User discussions archive
- sc-dev: Development discussions archive

**GitHub:**
- Main Repository: https://github.com/supercollider/supercollider
- Issue tracking
- RFC (Request for Comments) system
- Pull requests

### Code Repositories

**sccode.org** ⭐⭐⭐ **ESSENTIAL**
- URL: https://sccode.org
- User-submitted code examples
- Browseable by:
  - Tags (synthesis techniques, instruments)
  - Authors (elifieldsteel, brunoruviaro, etc.)
  - Categories (granular, fm, wavetable)
  - Reception (educational, awesome, instructive)

**Notable Authors on sccode.org:**
- elifieldsteel - Wavetable, FM, patterns
- brunoruviaro - Educational examples
- jamshark70 - Advanced techniques
- LFSaw - Creative synthesis
- totalgee - Wavetable morphing

**GitHub Organizations:**
- supercollider-quarks - Official Quark repository
- SCLOrkHub - Santa Clara Laptop Orchestra tools

### Resources by Synthesis Type

**FM Synthesis:**
- egegonul's DX7 emulator (sccode.org/egegonul)
- Eli Fieldsteel Tutorial 22
- Bruno Ruviaro FM examples

**Wavetable:**
- Eli Fieldsteel Tutorial 23 (sccode.org/1-5bF)
- Wavetable morphing (totalgee, sccode.org/1-4V1)
- PADsynth algorithm (sccode.org/1-58B)

**Analog:**
- Filter comparisons (doc.sccode.org/Guides/FilterComparisons.html)
- Detuned pads (sccode.org/1-4YS)
- MKPlugins analog collection

**Granular:**
- Granular category (sccode.org/tag/category/granular)
- Paulstretch implementation
- Graphic time-stretching (sccode.org/1-4Ye)

**Physical Modeling:**
- DWGPlucked examples
- Digital Harp (akselai)
- Karplus-Strong tutorials

---

## GETTING STARTED

### Installation

**Download:**
- Website: https://supercollider.github.io/downloads.html
- Platforms: Windows, macOS, Linux, Raspberry Pi, Bela
- Version: Latest stable (3.14.0+)

**First Steps:**
1. Install SuperCollider
2. Launch SuperCollider IDE
3. Boot server: `s.boot;`
4. Try first sound:
```supercollider
{ SinOsc.ar(440) * 0.1 ! 2 }.play;  // Stereo sine at 440 Hz
```

### Basic Workflow

**1. Boot Server:**
```supercollider
s.boot;  // Start audio server
```

**2. Create SynthDef:**
```supercollider
(
SynthDef(\simple, {
    |out=0, freq=440, amp=0.3|
    var sig = SinOsc.ar(freq) * amp;
    Out.ar(out, sig ! 2);
}).add;
)
```

**3. Create Synth:**
```supercollider
x = Synth(\simple, [\freq, 220]);
x.set(\freq, 330);
x.free;
```

**4. Use Patterns:**
```supercollider
(
Pbind(
    \instrument, \simple,
    \freq, Pseq([220, 330, 440], inf),
    \dur, 0.5
).play;
)
```

### Recommended Learning Path

1. **Week 1-2:** Bruno Ruviaro's "Gentle Introduction"
2. **Week 3-4:** Nick Collins Tutorial (weeks 1-4)
3. **Week 5-8:** Eli Fieldsteel videos (basics through patterns)
4. **Week 9-12:** Synthesis techniques (FM, wavetable, granular)
5. **Ongoing:** Explore sccode.org examples
6. **Advanced:** sc3-plugins, Quarks, live coding

---

## SYNTHESIS RECIPES

### Minimoog Bass

```supercollider
(
SynthDef(\moogBass, {
    |out=0, freq=55, cutoff=800, res=0.7, amp=0.5, gate=1|
    var sig, env, fenv;
    
    // 3 oscillators
    sig = Mix([
        Saw.ar(freq),
        Saw.ar(freq * 1.01),
        Pulse.ar(freq * 0.5, 0.5)
    ]) * 0.3;
    
    // Filter envelope
    fenv = EnvGen.kr(
        Env([0, 1, 0.2], [0.01, 0.2], \exp),
        gate
    );
    
    // Moog ladder filter
    sig = MoogLadder.ar(sig, cutoff * fenv + 100, res);
    
    // Amp envelope
    env = EnvGen.kr(
        Env.adsr(0.001, 0.1, 0.8, 0.3),
        gate,
        doneAction: 2
    );
    
    Out.ar(out, sig * env * amp ! 2);
}).add;
)

// Play
Synth(\moogBass, [\freq, 55, \cutoff, 1200, \res, 0.85]);
```

### DX7-Style FM Bell

```supercollider
(
SynthDef(\fmBell, {
    |out=0, freq=440, modFreq=1.4, modIndex=8, amp=0.3|
    var carrier, modulator, sig, env;
    
    // Modulator envelope
    env = EnvGen.kr(
        Env.perc(0.001, 3, 1, -4),
        doneAction: 2
    );
    
    // FM synthesis
    modulator = SinOsc.ar(freq * modFreq) * modIndex * env;
    carrier = SinOsc.ar(freq + modulator) * amp * env;
    
    Out.ar(out, carrier ! 2);
}).add;
)

// Play chord
[60, 64, 67, 72].do { |midi|
    Synth(\fmBell, [\freq, midi.midicps]);
};
```

### PPG Wave-Style Wavetable

```supercollider
(
// Create wavetable with 8 harmonics
var wt = Signal.sineFill(512, Array.fill(8, { |i| 1/(i+1) }));
b = Buffer.sendCollection(s, wt.asWavetableNoWrap, 1);

SynthDef(\ppgWave, {
    |out=0, freq=220, detune=0.1, amp=0.3|
    var sig;
    
    sig = Mix([
        VOsc.ar(b, freq),
        VOsc.ar(b, freq * (1 + detune)),
        VOsc.ar(b, freq * (1 - detune))
    ]) * 0.3;
    
    sig = RLPF.ar(sig, freq * 4, 0.5);
    
    Out.ar(out, sig * amp ! 2);
}).add;
)
```

---

## ADVANCED TOPICS

### JITLib (Just-In-Time Library)

**For Live Coding:**
```supercollider
// Ndef - Node proxy
Ndef(\sound, {
    SinOsc.ar([400, 404]) * 0.2
}).play;

// Modify on the fly
Ndef(\sound, {
    Saw.ar([200, 202]) * 0.1
});

// Clear
Ndef(\sound).clear;
```

### Event Patterns (Advanced Sequencing)

```supercollider
(
Pdef(\seq,
    Pbind(
        \instrument, \moogBass,
        \degree, Pseq([0, 3, 5, 7], inf),
        \dur, Pwrand([0.25, 0.5, 1], [0.5, 0.3, 0.2], inf),
        \cutoff, Pexprand(500, 3000),
        \res, Pwhite(0.5, 0.9)
    )
).play;
)
```

### ProxySpace (Live Coding Environment)

```supercollider
p = ProxySpace.push(s.boot);

~out = { SinOsc.ar([400, 404]) * 0.2 };
~out.play;

~out = { Saw.ar(\freq.kr(200) * [1, 1.01]) * 0.1 };
```

---

## TROUBLESHOOTING

### Common Issues

**Server Won't Boot:**
- Check audio device settings
- Try: `Server.killAll;` then `s.boot;`
- Inspect: `s.options.device = "device name";`

**Class Not Found After Installing Quark:**
- Recompile: Language > Recompile Class Library
- Check install: `Quarks.installed;`

**Duplicate Class Definition:**
- Conflicting Quarks installed
- Uninstall conflicts: `Quarks.uninstall("QuarkName");`

**sc3-plugins Not Loading:**
- Check Extensions folder path
- Rebuild without Ladspa on Linux if needed

---

## USEFUL LINKS

**Official:**
- Main Site: https://supercollider.github.io
- Documentation: https://doc.sccode.org
- GitHub: https://github.com/supercollider/supercollider
- Forum: https://scsynth.org

**Learning:**
- sccode.org: https://sccode.org
- Eli Fieldsteel: http://www.elifieldsteel.com
- Bruno Ruviaro: https://ccrma.stanford.edu/~ruviaro

**Community:**
- Slack: (see forum for invite)
- Reddit: r/supercollider
- YouTube: Search "SuperCollider tutorial"

---

**DOCUMENT END**

*For questions or contributions to this resource:*  
*Sounds of Seismic: https://sos.allshookup.org/*
