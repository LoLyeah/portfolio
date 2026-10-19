---
title: "Inside Suramak: High-Performance On-Device Audio Transcription in Rust"
summary: "How Suramak achieves private, real-time ASR using Rust, whisper.cpp, Core ML acceleration, and macOS CoreAudio process tapping."
date: "Oct 18 2026"
draft: false
tags:
- Rust
- whisper.cpp
- Core ML
- Audio Engineering
- macOS
---

Automatic Speech Recognition (ASR) has seen tremendous breakthroughs over the last few years, largely driven by models like OpenAI's Whisper. However, the majority of transcription tools send audio directly to external cloud APIs. For sensitive medical consultations, client depositions, executive meetings, or personal voice notes, uploading private audio introduces severe security and privacy compromises.

On the other hand, running local transcription often means dealing with hefty Python runtime environments, multi-gigabyte dependency trees, high thermal throttling, and sluggish UI wrappers.

**[Suramak](https://github.com/LoLyeah/suramak)** (see the [portfolio project showcase](/projects/suramak)) was built to solve this: a native, local-first, privacy-respecting transcription application engineered from the ground up in **Rust** using `egui` and `whisper.cpp` (via `whisper-rs`).

Here is an architectural deep dive into how Suramak delivers instant, private transcription on macOS and beyond.

---

### 1. Hardware Acceleration on Apple Silicon: Core ML & Metal

Running transformer models locally requires maximizing hardware throughput while minimizing battery consumption. On modern Apple Silicon chips (M1 through M4), the Apple Neural Engine (ANE) offers exceptional efficiency for matrix operations.

Suramak integrates hardware acceleration across multiple tiers:

1. **Neural Engine (Core ML):** By compiling Whisper encoder models into Core ML model packages (`.mlmodelc`), the heavy encoder transformer computations are offloaded entirely to the Apple Neural Engine via `--engine coreml`.
2. **Metal Acceleration:** The decoder steps and cross-attention operations leverage customized Metal Shading Language (MSL) compute pipelines.
3. **Multi-Threaded CPU Fallback:** On x86_64 machines or environments without Neural Engine support, Suramak smoothly falls back to multi-threaded CPU execution with AVX2 and NEON vector optimizations.

This multi-tier approach achieves sub-realtime transcription (processing 60 seconds of speech in just 1–2 seconds) without causing the laptop fans to spin or draining the battery.

---

### 2. Multi-Source Audio Mixing & Per-App Process Tapping

Capturing audio for meetings has always been notoriously tricky on desktop operating systems. If you record only your microphone, you miss the other participants; if you record only the system audio, your own voice is omitted.

Suramak implements direct macOS CoreAudio integrations to provide:

- **Multi-Source Audio Mixing:** Mixes your local USB/headset microphone with system audio output into a synchronized 16 kHz mono PCM buffer suitable for Whisper inference.
- **Measured-Clock Drift Correction:** Because hardware clock crystals on independent USB audio interfaces drift relative to the system DAC clock, Suramak continuously measures sample timestamp deltas and applies micro-resampling to prevent audio phase artifacts or desynchronization over long meetings.
- **Per-App Process Taps:** Leveraging macOS CoreAudio process taps, Suramak allows users to isolate audio exclusively from a selected process (such as Zoom, Slack, or Google Chrome). You can capture a lecture or conference call cleanly without picking up background music, keyboard clicks, or notification chimes.

---

### 3. Dual-Pass Streaming: Provisional Feedback with High-Precision Finalization

Whisper is fundamentally an attention-based encoder-decoder model designed to evaluate complete 30-second audio windows, making pure low-latency streaming non-trivial.

Suramak employs an innovative **two-pass real-time engine**:

```rust
// Architecture diagram: Dual-Pass Transcription Pipeline
//
// Mic/System Audio Stream
//         │
//         ▼
// ┌──────────────────────────┐
// │ Sliding Buffer (16 kHz)  │
// └────────────┬─────────────┘
//              │
//              ├──► [Pass 1: ~4s Sliding Window] ──► Live Provisional UI (Italic)
//              │    (Low beam-width, fast inference)
//              │
//              └──► [Pass 2: Complete Segment]   ──► Final Verified Text (Roman)
//                   (Full beam search, context prompt)
```

1. **Pass 1 (Provisional Sliding Windows):** Incoming audio chunks are fed into a 4-second overlapping sliding window. Suramak runs quick greedy inference on the current buffer to provide instant live feedback on the screen, letting users confirm their microphone is capturing speech accurately.
2. **Pass 2 (Context-Aware Final Pass):** When a natural speech pause is detected (or upon ending the session), the entire continuous segment is processed with full beam-search precision. Suramak passes the prior segment's transcription as the prompt context, eliminating hallucinations and ensuring proper punctuation, sentence capitalization, and noun consistency.

---

### 4. Zero-FFmpeg Native Media Decoding

Most desktop transcription tools depend on the user having an external `ffmpeg` binary installed in their system `PATH`. When missing, the application fails cryptographically or errors out with obscure system dialogs.

Suramak avoids external tool dependencies entirely by bundling native pure-Rust audio decoders:
- Direct demuxing and decoding of container formats: `.mp4`, `.mov`, `.mkv`, `.webm`, and `.m4a`.
- Native decompression of Opus voice memos, AAC, and WAV audio streams.

Users can drag and drop any audio or video file directly into the application window and begin transcribing immediately with zero prerequisite installations.

---

### 5. Audio-Aware AI Polish & Comprehensive Exporting

While local Whisper models offer remarkable accuracy, specialized medical terms, non-traditional proper nouns, and domain acronyms can occasionally stumble over acoustic ambiguities.

To solve this, Suramak offers an optional **Audio-Aware AI Polish** layer:
- The transcribed text, alongside acoustic cues and phonetic hints, can be routed through a local LLM or OpenAI-compatible endpoint.
- The model intelligently rectifies homophones (e.g., distinguishing between "there", "their", and "they're" or specialized clinical terminology) without modifying accurate conversational flow.

Finally, transcripts can be exported instantly to timestamped **Markdown**, clean **TXT**, structured **JSON** (with token-level timestamps), or standard **SRT** and **VTT** subtitle formats.

---

### Conclusion & Project Links

Suramak demonstrates that you do not need to sacrifice speed, elegance, or user privacy to build modern AI software. By combining Rust's zero-cost abstractions, native macOS audio hooks, and whisper.cpp's optimized inference kernels, local-first computing can deliver an experience that feels instantaneous, robust, and dependable.

- **GitHub Repository**: [github.com/LoLyeah/suramak](https://github.com/LoLyeah/suramak)
- **Portfolio Showcase**: [View Suramak in Projects](/projects/suramak)
