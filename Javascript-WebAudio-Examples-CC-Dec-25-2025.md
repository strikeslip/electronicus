# JavaScript / Web Audio API Examples
## Synthesizer Implementations & Resources

*Compiled: December 25, 2024*  
*Claude Sonnet 4.5*

---

## TABLE OF CONTENTS

1. [General Web Audio Synthesizers](#general-web-audio-synthesizers)
2. [Wavetable Synthesis (PPG Wave Style)](#wavetable-synthesis)
3. [FM Synthesis (DX7 Style)](#fm-synthesis)
4. [Subtractive/Analog Synthesis](#subtractive-analog-synthesis)
5. [npm Packages](#npm-packages)
6. [Tone.js Framework & Instruments](#tonejs-framework)
7. [CodePen Examples](#codepen-examples)
8. [Comprehensive Resource Collections](#comprehensive-resource-collections)

---

## GENERAL WEB AUDIO SYNTHESIZERS

### Full-Featured Synthesizers

**JSS-01 | JavaScript Software Synthesizer**
- Repository: https://github.com/michaelkolesidis/javascript-software-synthesizer
- Type: FM Synthesis (Yamaha-style)
- Stack: Tone.js, NexusUI2, TypeScript, Vite
- Features: 
  - FM synthesis with Carrier + Modulator oscillators
  - Full effects chain: AutoFilter, Phaser, Crusher, Chebyshev, Reverb, Chorus, Tremolo, Vibrato, Distortion
  - MIDI keyboard support via WEBMIDI.js
  - Dark mode UI
  - Modular architecture
- Package: `npm install javascript-software-synthesizer`
- Status: Active (v2.0+)

**wubwub - Web-based Synthesizer**
- Repository: https://github.com/fi4cr/wubwub
- Live Demo: https://fi4cr.github.io/wubwub/
- Features:
  - 3 oscillators with waveform/frequency/gain/detune controls
  - FM synthesis capabilities
  - Filters, LFO, distortion effects
  - Real-time visualizations
  - Built-in presets
  - Audio recording (download as WAV)
- Tech: HTML, CSS, JavaScript, Web Audio API
- Status: Active (2024)

**Modular Synthesizer (Arduino + Web Audio)**
- Repository: https://github.com/mattybrad/modularsynth
- Type: Modular synthesis with hardware interface
- Features:
  - Arduino-controlled patching (physical patch bay)
  - Web Audio API soft synth backend
  - Customizable module system
  - Status: Experimental/Educational

---

## WAVETABLE SYNTHESIS

**looshi/wavetable-synth-2** ⭐ **RECOMMENDED**
- Repository: https://github.com/looshi/wavetable-synth-2
- Type: PPG Wave-inspired wavetable synthesis
- Features:
  - 3 oscillators with wavetable interpolation
  - LFO with custom wave shapes (stored as JSON)
  - Arpeggiator (5-second loop memory)
  - Chorus, glide
  - Adjustable tempo/cycles
  - Virtual keyboard + piano keys UI
- Installation: 
  ```bash
  git clone https://github.com/looshi/wavetable-synth-2.git
  npm install
  npm run dev  # Navigate to http://localhost:9000
  ```
- Tech: Web Audio API, vanilla JavaScript
- Data: Custom LFO coefficients at `src/data/Coefficients.js`

**cwilso/web-audio-samples - Wavetable Synth**
- Repository: https://github.com/cwilso/web-audio-samples/tree/master/samples/audio
- File: `wavetable-synth.html`
- Features: Classic wavetable implementation with GUI knobs
- Status: Reference implementation from Google

**modosc/wavetable-synth**
- Repository: https://github.com/modosc/wavetable-synth
- Type: Non-linear waveshaping synthesis
- Features:
  - Chebyshev polynomials for 16 wavetables
  - Variable amplitude sine wave driver
  - MIDI support (Chrome)
  - Oscilloscope visualization
- Tech: Web Audio API, Chebyshev mathematics
- Status: Experimental

---

## FM SYNTHESIS

### DX7 Implementations

**DX7 Synth JS**
- Mentioned in: https://github.com/Yuan-ManX/audio-development-tools
- Features:
  - DX7 FM synthesis using Web Audio + Web MIDI API
  - Works in Chrome and Firefox
  - MIDI or QWERTY keyboard control
- Status: Referenced (search for specific repo)

### Educational FM Synthesis

**WAC-2016-Tutorial - Percussion Synthesis**
- Repository: https://github.com/irritant/WAC-2016-Tutorial
- Conference: Web Audio Conference 2016, Atlanta
- Topics:
  - FM synthesis for percussion (hi-hats, cymbals)
  - Carrier + modulator operator patterns
  - High frequency (1000+ Hz) with non-integer ratios
  - Metallic sound design
- Projects: `fm-percussion`, `sub-percussion`
- Documentation: Excellent primer on FM percussion techniques

**chrislo/synth_history - FM Synthesis Demo**
- Repository: https://github.com/chrislo/synth_history
- Demo: "brass-like sounds" from John Chowning's seminal FM paper
- Also includes: Additive, Subtractive, Sampler, Granular examples
- Utilities: `keyboard.js` for QWERTY → MIDI note mapping

**gabrieldavison/engine**
- Repository: https://github.com/gabrieldavison/engine
- Type: Yamaha PortaSound-inspired 2-op FM synthesizer
- Stack: Tone.js, Web Audio API
- Features:
  - Lo-fi gratifying sound
  - Polyphonic
  - Broadcast Channel API for inter-app communication
  - Computer keyboard + broadcast channel "notes" input
- Future: MIDI functionality + MIDI learn

---

## SUBTRACTIVE ANALOG SYNTHESIS

**simple-audio-synthesizer**
- Repository: https://github.com/raohmaru/simple-audio-synthesizer
- Features:
  - OscillatorNode or white noise generation
  - Multiple AudioNode manipulation
  - Reverb via ConvolverNode (Base64-encoded impulse responses)
  - Educational focus
- Installation:
  ```javascript
  import SAS from './lib/sasynth.js';
  import Note from './lib/note.js';
  
  const sas = new SAS();
  sas.createNote({ type: "sine", freq: 220 });
  sas.play();
  ```
- Browsers: Chrome, Firefox, Edge, Safari (ES2015+)
- Impulse Response Credits: Samplicty (free)

**Working-with-the-Web-Audio-API**
- Repository: https://github.com/joshreiss/Working-with-the-Web-Audio-API
- Topics:
  - AM and FM synthesis examples
  - Audio parameter modulation
  - Analyser node (time/frequency visualization)
  - DelayNode (comb filtering, vibrato, Karplus-Strong)
  - OfflineAudioContext examples
- Status: Comprehensive educational resource

---

## NPM PACKAGES

### General Synthesizers

**webaudio-tinysynth** ⭐
- Package: `npm install webaudio-tinysynth`
- Repository: https://github.com/g200kg/webaudio-tinysynth
- Features:
  - GM-like timbre map
  - Algorithmic sound generation (no PCM samples)
  - CustomElement: `<webaudio-tinysynth></webaudio-tinysynth>`
  - Quality modes: 0 (chip-tune 1 osc/note), 1 (FM-based 2+ osc/note)
  - Built-in MIDI sequencer (.mid file support)
  - Reverb, polyphony control
  - File drop support
- API: `synth = new WebAudioTinySynth()`
- Wrapper: `jzz-synth-tiny` (for JZZ.js MIDI library)

**js-synthesizer**
- Package: `npm install js-synthesizer`
- Type: FluidSynth WebAssembly port
- Features:
  - SoundFont 2/3 support (.sf2, .sf3)
  - SMF (.mid) sequencer
  - AudioWorklet support
  - Web Worker compatible
- Files:
  - `dist/js-synthesizer.js` (main)
  - `externals/libfluidsynth-2.4.6.js` (LGPL v2.1)
  - `dist/js-synthesizer.worklet.js` (AudioWorklet)
- Demo: https://www.pg-fl.jp/music/js-synthesizer/index.en.htm
- Installation:
  ```javascript
  import * as JSSynth from 'js-synthesizer';
  await JSSynth.waitForReady();
  const synth = new JSSynth.Synthesizer();
  ```

**@logue/sf2synth**
- Package: `npm i @logue/sf2synth`
- Size: 71.4 MB
- Features:
  - SoundFont synthesis
  - WebMidiLink Level 1 compliant
  - GM Level 2 + YAMAHA XG Lite (MU50 equivalent)
  - MIDI keyboard GUI (optional)
  - Cache API for SoundFont data
- Default SoundFont: Yamaha XG Sound Set
- Alternative: A320U (original sf2synth.js compatible)

**wasm-music**
- Package: `npm install wasm-music`
- Type: WebAssembly-based synthesizer
- Features:
  - Written in AssemblyScript
  - Simple oscillators, filters, delay, reverb
  - Pattern sequencer (4klang-style structures)
  - AudioWorklet playback (low latency, Chrome only)
  - Tiny output (<16KB typical)
  - Can run in browser, NodeJS, Wasm3, Wasmer
- Export: Raw audio data (32-bit float, little endian, stereo)
- Video: Export WebM without sound, mux with FFMPEG

**BeepBox**
- Package: `npm install beepbox` (johnnesky)
- Type: Online sketching tool for instrumental music
- Features: Web-based music creation
- Status: v4.2.0 (stable)

### Utility Libraries

**synth-js**
- Package: `npm install synth-js`
- Type: CLI utility + Node module
- Purpose: Generate raw audio data from MIDI files
- File: `dst/synth.min.js` → global `synth` variable

**midi-synth**
- Package: `npm install midi-synth`
- Type: WebAudio MIDI synthesizer (no UI)
- Based on: W3C MIDI example (monophonic sine wave synth)
- Status: v0.0.4 (10 years old)

---

## TONE.JS FRAMEWORK

### Overview
- Website: https://tonejs.github.io/
- Repository: https://github.com/Tonejs/Tone.js
- NPM: `npm install tone` or `npm install tone@next`
- CDN: https://unpkg.com/tone

### Core Concept
High-level Web Audio framework with:
- Global transport for synchronization/scheduling
- Prebuilt synths and effects
- Audio-rate signal control
- Time encoding ("4n" = quarter-note, "8t" = eighth-note triplet)

### Built-in Synthesizers

**Available Instruments:**
- `Tone.Synth` - Basic monophonic
- `Tone.AMSynth` - Amplitude modulation
- `Tone.FMSynth` - Frequency modulation (DX7-style)
- `Tone.MonoSynth` - Monophonic with envelope
- `Tone.DuoSynth` - Two synths combined
- `Tone.MembraneSynth` - Drum/membrane sounds
- `Tone.MetalSynth` - Metallic percussion
- `Tone.NoiseSynth` - Noise-based sounds
- `Tone.PluckSynth` - Karplus-Strong plucked strings
- `Tone.PolySynth` - Polyphonic wrapper for any monophonic synth
- `Tone.Sampler` - Sample-based instrument (polyphonic)

### Key Methods

```javascript
// Basic usage
const synth = new Tone.Synth().toDestination();

// Note triggers
synth.triggerAttack("C4");              // Start note
synth.triggerAttack("C4", time, 0.5);   // With velocity
synth.triggerRelease();                  // Release note
synth.triggerAttackRelease("C4", "8n"); // Combined (8th note)

// Polyphonic
const poly = new Tone.PolySynth(Tone.Synth).toDestination();
poly.triggerAttackRelease(["C4", "E4", "G4"], "4n");

// Sampler
const sampler = new Tone.Sampler({
  urls: { "C4": "piano-C4.mp3" },
  baseUrl: "https://example.com/samples/"
}).toDestination();
```

### Time Encoding

- `"4n"` - Quarter note
- `"8t"` - Eighth note triplet
- `"1m"` - One measure
- `"+0.5"` - Relative to current time
- Numeric values in seconds

### Tone.js Instrument Libraries

**tonejs-instruments** ⭐
- Repository: https://github.com/nbrosowsky/tonejs-instruments
- Features: Sample library with quick-loader
- Instruments: piano, bass-electric, bassoon, cello, clarinet, contrabass, flute, french-horn, guitar-acoustic, guitar-electric, guitar-nylon, harmonium, harp, organ, saxophone, trombone, trumpet, tuba, violin, xylophone
- Usage:
  ```javascript
  // Single instrument
  var piano = SampleLibrary.load({ instruments: "piano" });
  piano.toMaster();
  
  // Multiple instruments
  var instruments = SampleLibrary.load({
    instruments: ["piano", "violin", "trumpet"]
  });
  
  // Wait for loading
  Tone.Buffer.on('load', function() {
    instruments['piano'].triggerAttack("A3");
  });
  
  // Change file extension
  var piano = SampleLibrary.load({
    instruments: "piano",
    ext: ".wav"  // or ".[wav|mp3|ogg]"
  });
  ```
- Demo: https://github.com/nbrosowsky/tonejs-instruments/blob/master/demo.html

### Important Notes

**Browser Autoplay Policy:**
```javascript
// REQUIRED: User interaction before audio
document.querySelector("button")?.addEventListener("click", async () => {
  await Tone.start();
  console.log("audio is ready");
  // Now safe to play audio
});
```

**Signal Automation:**
```javascript
const osc = new Tone.Oscillator().toDestination();
osc.frequency.value = "C4";
osc.frequency.rampTo("C2", 2);  // Ramp over 2 seconds
osc.start().stop("+3");
```

### Tone.js Examples

**Official Examples:**
- https://tonejs.github.io/examples/polySynth.html
- https://github.com/Tonejs/tonejs.github.io/tree/master/examples

**Wiki Resources:**
- Instruments: https://github.com/Tonejs/Tone.js/wiki/Instruments
- Arpeggiator: https://github.com/Tonejs/Tone.js/wiki/Arpeggiator
- Building a Synth: https://github.com/Tonejs/Tone.js/wiki/Build-a-synth
- Envelopes: https://github.com/Tonejs/Tone.js/wiki/Understanding-Envelopes
- Effects: https://github.com/Tonejs/Tone.js/wiki/Effects

---

## CODEPEN EXAMPLES

### FM Synthesis

**FM Synthesis (Audio + Visualization)** by liamr
- URL: https://codepen.io/liamr/pen/vYPNYO
- Features: FM synthesis with real-time waveform visualization
- Controls: Carrier frequency/amplitude, modulator frequency/amplitude, bypass
- Tech: Web Audio API ScriptProcessorNode, dat.GUI controls

**WebAudio FM Synthesis** by marcusstenbeck
- URL: https://codepen.io/marcusstenbeck/pen/DJQKQO
- Features: FM synthesis with oscilloscope-style canvas drawing
- Controls: fc, fc_amplitude, fm, fm_amplitude
- Based on: Wikipedia Frequency Modulation Synthesis

**Web Audio FM Synthesis** by Cthulahoop
- URL: https://codepen.io/Cthulahoop/details/vYzodE
- Purpose: String instrument synthesis via FM
- Features: Dual modulator oscillators for better high-note performance

**Glockenspiel Synthesis** by nicross
- URL: https://codepen.io/nicross/pen/jOWRqNe
- Technique: FM synthesis for percussive metallophone sounds
- Features: 
  - createFmSynth() function with comprehensive parameters
  - Analyser + real-time frequency visualization
  - Prefers-reduced-motion support
- Code structure: Clean, modular FM implementation

### General Synthesis

**Web Audio API Demo (Synth Pad)** by matt-west
- URL: https://codepen.io/matt-west/pen/AzLGWQ
- Full Demo: https://codepen.io/matt-west/full/lAFnx
- Features:
  - Interactive XY pad (frequency + volume control)
  - Mouse drag to generate sound
  - Canvas-based interface
  - Real-time parameter display
- Blog: Treehouse Blog companion post

**Flat Synthesizer** by JaT5
- URL: https://codepen.io/JaT5/pen/DLqqVx
- Type: Full synthesizer implementation
- Features: Complete note frequency mapping (C0-C8)
- Based on: html5rocks.com Web Audio API examples

**Web Audio API Drum Sequencer** by njmcode
- URL: https://codepen.io/njmcode/pen/PwaXwB
- Features:
  - Sample-based drum machine
  - Grid sequencer UI
  - Pattern presets
  - Live editing
  - EQ visualization per channel
- Architecture: SampleBank loader, SequencerView UI
- Tech: Web Audio API decodeAudioData, BufferSource playback

### 8-bit / Chiptune

**Recreating Legendary 8-bit Games Music**
- Blog Post: https://codepen.io/gregh/post/recreating-legendary-8-bit-games-music-with-web-audio-api
- Author: Greg Hovanesyan
- Topics:
  - PSG (Programmable Sound Generator) emulation
  - Pulse, square, triangle, sawtooth waves
  - Yamaha YM3812 techniques
  - Using Tone.js for pitch-octave notation
- Reference: "How Oldschool Sound/Music worked" by The 8-Bit Guy

### Educational / Tutorials

**Fun with Web Audio API** by Johan Karlsson
- Blog Post: https://codepen.io/DonKarlssonSan/post/fun-with-web-audio-api
- Topics:
  - Audio filters with SoundCloud streaming
  - Tone generator (oscillator + analyser visualization)
  - Square wave + Gibbs phenomenon demonstration
  - Custom wave via createPeriodicWave() + setPeriodicWave()
- Concepts: AudioContext as "studio," nodes as "equipment"
- CORS: Important for cross-origin audio streaming

---

## COMPREHENSIVE RESOURCE COLLECTIONS

### awesome-webaudio
- Repository: https://github.com/jcppman/awesome-webaudio
- Curated list of WebAudio packages and resources
- Sections: Libraries, Tools, Examples, Tutorials

**Highlighted Resources:**
- **SSSynthesiser.js** - Wavetable synth for interactive music
- **Bap** - Beat making toolkit (MPC60/2000 inspired)
- **reverbGen** - Artificial reverb generation

### web-audio-hub
- Repository: https://github.com/AmberJBlue/web-audio-hub
- Comprehensive Web Audio API resource collection

**Categories:**

**Synthesizers:**
- Super Oscillator (interactive 3D)
- Zupiter (modular synth, Pure Data-style)
- 808303 (Roland drum machine + synth recreation)
- Bauble FM Drum Machine (Christmas-themed)

**Tutorials:**
- "Fun With Web Audio" course (additive synthesis + visualization)
- "Introduction to Web MIDI"
- "Learn to Code Electronic Music Tools with Javascript" (4-week course)
- "Web Audio API Understandable Reference"
- "Web Audio API Video Tutorial Series" (11 parts)

**Tools:**
- Pizzicato - Simplified Web Audio library
- AudioMass - Full-featured web audio editor
- WebPd - Run Pure Data patches in browser
- WEBMIDI.js - Simplified MIDI interaction

### audio-development-tools
- Repository: https://github.com/Yuan-ManX/audio-development-tools
- Massive collection of audio dev tools

**Web Audio Specific:**
- DX7 Synth JS (FM synthesis)
- Pizzicato (library)
- AudioMass (waveform editor)
- WebPd (Pure Data compiler)
- WEBMIDI.js (MIDI library)

**Related Synthesis:**
- Dexed (DX7 multi-platform plugin)
- Surge (open-source VST)
- VCV Rack (virtual Eurorack)

---

## SYNTHESIS TECHNIQUES BY TYPE

### 1. Wavetable (PPG Wave, Korg M1)
**Best Resources:**
- looshi/wavetable-synth-2 (GitHub)
- cwilso/web-audio-samples (reference)
- Tone.js with custom PeriodicWave

### 2. FM Synthesis (Yamaha DX7)
**Best Resources:**
- Tone.js FMSynth
- WAC-2016-Tutorial (educational)
- CodePen FM examples (interactive learning)
- gabrieldavison/engine (PortaSound-style)

### 3. Subtractive (Minimoog, Prophet-5, Jupiter-8)
**Best Resources:**
- Tone.js MonoSynth/PolySynth
- simple-audio-synthesizer (educational)
- Working-with-the-Web-Audio-API (comprehensive)

### 4. Sampling (Buchla, Mellotron)
**Best Resources:**
- Tone.Sampler
- tonejs-instruments (pre-loaded samples)
- js-synthesizer (SoundFont support)

---

## BROWSER COMPATIBILITY

### Modern Support (2024)
- ✅ Chrome 25+
- ✅ Firefox 25+
- ✅ Safari (with AudioContext)
- ✅ Edge (Chromium-based)
- ✅ Chrome for Android 28+

### Important Notes
- AudioContext requires user interaction (click/tap)
- CORS restrictions for cross-origin audio
- AudioWorklet: Chrome/Edge only (low latency)
- ScriptProcessorNode: Deprecated, use AudioWorklet
- MIDI: Chrome has best support via Web MIDI API

---

## GETTING STARTED

### Quick Start - Tone.js (Recommended)

```html
<!DOCTYPE html>
<html>
<head>
  <script src="https://unpkg.com/tone"></script>
</head>
<body>
  <button id="play">Play</button>
  
  <script>
    const synth = new Tone.PolySynth(Tone.Synth).toDestination();
    
    document.getElementById('play').addEventListener('click', async () => {
      await Tone.start();
      synth.triggerAttackRelease(["C4", "E4", "G4"], "4n");
    });
  </script>
</body>
</html>
```

### Quick Start - Pure Web Audio API

```html
<!DOCTYPE html>
<html>
<body>
  <button id="play">Play</button>
  
  <script>
    const ctx = new (window.AudioContext || window.webkitAudioContext)();
    
    document.getElementById('play').addEventListener('click', () => {
      const osc = ctx.createOscillator();
      const gain = ctx.createGain();
      
      osc.connect(gain);
      gain.connect(ctx.destination);
      
      osc.frequency.value = 440; // A4
      osc.type = 'sine';
      gain.gain.value = 0.3;
      
      osc.start();
      osc.stop(ctx.currentTime + 0.5);
    });
  </script>
</body>
</html>
```

---

## RECOMMENDED LEARNING PATH

1. **Start:** Tone.js examples (easiest entry point)
2. **Explore:** CodePen interactive demos
3. **Learn:** WAC-2016-Tutorial, Working-with-the-Web-Audio-API
4. **Build:** Clone wavetable-synth-2 or JSS-01
5. **Advanced:** Implement custom AudioWorklets
6. **Master:** Contribute to open-source synthesizer projects

---

## ADDITIONAL RESOURCES

### Documentation
- MDN Web Audio API: https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API
- Web Audio API Spec: https://webaudio.github.io/web-audio-api/
- Tone.js Docs: https://tonejs.github.io/docs/

### Communities
- Web Audio Slack: (check awesome-webaudio for invite)
- Web Audio Weekly Newsletter
- WAC (Web Audio Conference) - Annual conference

### Books/Articles
- "Web Audio API" by Boris Smus
- "JavaScript Systems Music" (blog)
- Sound on Sound - "Synth Secrets" series

---

## SYNTHESIS-SPECIFIC IMPLEMENTATIONS

### For Buchla (Modular/West Coast)
- Pure Web Audio with custom oscillators
- Tone.js with extensive modulation routing
- Consider patch-cable UI (like mattybrad/modularsynth)

### For Korg M1 (Sample + Synthesis)
- Tone.Sampler with custom samples
- js-synthesizer with M1 SoundFont
- Hybrid approach: samples + filtering

### For Yamaha DX7 (FM)
- Tone.FMSynth (4-operator capable with nesting)
- Custom 6-operator implementation needed for full DX7
- Algorithms = different routing topologies

### For Minimoog (Subtractive Analog)
- Tone.MonoSynth with:
  - 3 VCOs (oscillator banks)
  - 24dB/oct lowpass filter
  - ADSR envelope
  - Portamento/glide

### For PPG Wave (Wavetable)
- looshi/wavetable-synth-2 as starting point
- 30 wavetables × 64 waves = custom PeriodicWave arrays
- Interpolation between tables over time

---

## PERFORMANCE CONSIDERATIONS

### Optimization Tips
1. **Voice Limiting:** Cap polyphony (6-8 voices typical)
2. **AudioWorklet:** Use instead of ScriptProcessorNode
3. **Sample Rate:** 44.1kHz standard, 48kHz for pro
4. **Buffer Size:** Larger = less CPU, more latency
5. **Caching:** Pre-compute wavetables, save PeriodicWaves
6. **Web Workers:** Offload non-audio computation
7. **Throttle:** Limit UI update rates separate from audio rate

### CPU-Intensive Operations
- Real-time wavetable generation
- High polyphony (>16 voices)
- Multiple effects chains
- Convolution reverb
- FFT analysis

---

**DOCUMENT END**

*For questions or contributions to this resource:*
*Sounds of Seismic: https://sos.allshookup.org/*
