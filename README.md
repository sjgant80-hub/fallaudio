# FallAudio

**Sovereign single-file audio recording and editing.** Replaces Adobe Audition for the everyday work — record, edit, fade, normalize, filter, export WAV. One HTML file. No install. No account. No telemetry. Runs from `file://`.

- **prime** 461
- **version** 1.0.0
- **seal** ◊·κ=1 · MIT
- **phase** FallStudio · phase 2 (the Audition wedge)

---

## For people who just want to use it

1. Download `index.html`
2. Double-click it. Your browser opens it.
3. Click the red ● button to record. Or drop an audio file (mp3 / wav / ogg / webm) anywhere on the page.
4. Drag on the waveform to select a region. Cut, copy, fade, normalize, reverse.
5. Click **export wav** to download a 16-bit PCM WAV.

### Keyboard

| key | action |
| --- | --- |
| **space** | play / pause |
| **esc** | clear selection |
| **ctrl+k** | open Ω autopilot |
| **ctrl+z** | undo |
| **ctrl+s** | export WAV |
| mouse wheel on waveform | zoom horizontally |
| shift + drag | extend selection |

### Microphone permission — important

Browsers only allow `getUserMedia()` (microphone access) over **`https://`** or directly from **`file://`** (the html opened locally). On plain `http://`, the record button will be denied by the browser, but loading / editing / exporting all still work.

If you're running this from GitHub Pages, that's `https://` — recording works. If you've double-clicked `index.html` from your downloads folder, that's `file://` — recording works. If you serve it over plain `http://` from some local dev server without TLS, recording will fail until you switch.

### Features

- **Record** from microphone, with live VU meter and pause
- **Load** mp3 / wav / ogg / webm by drop or picker
- **Waveform** canvas with playhead, zoom, scroll, time ruler
- **Select** a region by drag (shift+drag to extend, esc to clear)
- **Edit**: cut, copy, paste, trim, silence, fade in/out, normalize, reverse, trim silence
- **Effects**: gain (±24 dB), speed (0.5×–2×), lowpass (200 Hz–20 kHz), highpass (20 Hz–2 kHz). Each applies to selection or whole buffer.
- **Presets**: podcast voice · de-harsh · telephone
- **Clips** side panel — save the current buffer to a clip, switch between them to assemble pieces
- **Export** WAV (16-bit PCM, header written inline — no library). Raw webm/opus blob also exposed for download when you've recorded.
- **Ω autopilot** (ctrl+K): natural-language commands. T0 keywords work offline ("record 30s", "normalize", "fade out", "trim silence"). T3 (bring-your-own-key) parses things like "make this sound less harsh".

---

## For developers

### Architecture

Single HTML file. Vanilla JS. Web Audio API for decoding / filtering / resampling. MediaRecorder for capture. Canvas 2D for the waveform. No frameworks, no npm, no build step, no CDN.

```
index.html
├── inline CSS  (estate dark palette + brass accents)
├── inline HTML (header · transport · clips · waveform · ops · fx)
└── inline JS
    ├── state (buffer, selection, view, settings, clips)
    ├── audio ops (cut/copy/paste/trim/silence/fade/norm/reverse)
    ├── DSP (filter / speed via OfflineAudioContext)
    ├── recording (getUserMedia + MediaRecorder)
    ├── waveform render (canvas 2D, per-pixel min/max)
    ├── WAV export (44-byte RIFF header written inline)
    ├── Cascade (T0/T2/T3 LLM routing)
    ├── Ω palette (T0 keyword router + T3 NL parser)
    ├── KONOMI sovereign shim
    ├── fallmesh BroadcastChannel hook
    └── postMessage API
```

### postMessage API

Other tools in the FallStudio suite can drive FallAudio across windows:

```js
const w = window.open('fallaudio/index.html');
w.postMessage({target:'fallaudio', source:'mytool', action:'startRecord'}, '*');
// later:
w.postMessage({target:'fallaudio', source:'mytool', action:'stopRecord'}, '*');
w.postMessage({target:'fallaudio', source:'mytool', action:'exportWav'}, '*');
w.postMessage({target:'fallaudio', source:'mytool', action:'getInfo'}, '*');
window.addEventListener('message', e => console.log(e.data));
```

Supported actions: `ping`, `startRecord`, `stopRecord`, `exportWav`, `getInfo`.

### fallmesh BroadcastChannel

Joins the `fall-signal` channel on load and announces presence. Responds to `ping`. This is how the FallStudio suite tools discover each other in the same browser.

### Cascade tiers

- **T0** — fully offline. Keyword router handles "record 30s", "normalize", "fade out", "trim silence", etc.
- **T2** — local Ollama at `127.0.0.1:11434`. Probed once on boot.
- **T3** — BYOK to Anthropic / OpenAI / Gemini / OpenRouter. Keys stored only in `localStorage` on your device. Enables natural-language commands like "make this sound less harsh" (parsed into `{action:'lowpass', value:6500}`).

### WAV export

The 44-byte RIFF/WAVE header is written inline in `bufToWav(buffer, sampleRate)`. Mono, 16-bit PCM, little-endian. No library.

### Estate constants

```js
const TOOLNAME='fallaudio', PRIME=461, VERSION='1.0.0';
```

### FallStudio plan

FallAudio is **phase 2** of FallStudio — the sovereign creative suite that replaces Adobe wedge-by-wedge. Each tool is one HTML file. Each ships against the same 14-point gate (single file · sovereign · file:// · <400KB · Konomi shim · fallmesh hook · PWA manifest · `.nojekyll` · MIT). FallPDF, FallMage, FallVector, FallOffice are siblings. Source-of-truth doctrine lives in `SHARED_BUILD_DOCTRINE.md`.

### License

MIT. See `LICENSE`. Copyright (c) 2026 Simon Gant · sjgant80-hub.

Seal: ◊·κ=1
