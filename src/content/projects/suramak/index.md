---
title: "Suramak"
summary: "A local-first, on-device audio transcription application built in native Rust with whisper.cpp, featuring multi-source recording, live transcription, and Core ML acceleration."
date: "Oct 18 2026"
draft: false
tags:
- Rust
- whisper.cpp
- Core ML
- egui
- Audio / ASR
- macOS
repoUrl: https://github.com/LoLyeah/suramak
---

**Suramak** is a high-performance, local-first transcription application built natively in **Rust** using `egui` and `whisper.cpp` (via `whisper-rs`). Designed for complete privacy and offline operation, it processes audio entirely on-device without telemetry or cloud dependencies.

### Key Features
- ⚡ **Neural Engine Hardware Acceleration:** Fast path inference on Apple Silicon via Core ML encoder packages (`--engine coreml`), alongside Metal and multi-threaded CPU fallback tiers.
- 🎙️ **Multi-Source Audio Mixing:** Mix multiple microphones or combine microphone and system audio (ideal for meeting transcription) with measured-clock drift correction.
- 🎯 **Per-App Process Audio Taps:** Capture audio exclusively from a specific running application via macOS CoreAudio process taps without capturing unwanted background sounds.
- 🔴 **Live Provisional Transcription:** Incoming audio streams are decoded in ~4-second overlapping sliding windows during capture for instantaneous feedback, followed by an automatic full-precision pass upon completion.
- 🎬 **Zero-FFmpeg Native Media Decoding:** Import video (`.mp4`, `.mov`, `.mkv`, `.webm`) and Opus voice notes with native decoders compiled directly into the binary—no external `ffmpeg` binary dependencies required.
- ✨ **Audio-Aware AI Polish:** Optional post-processing via OpenAI-compatible endpoints that passes chunked audio segments alongside text to accurately resolve names, homophones, and industry terminology.
- 📝 **Comprehensive Export Options:** Export timestamped transcripts in Markdown, TXT, JSON, and standard subtitle formats (SRT and VTT).
- 💧 **Modern Native Interface:** Liquid glass interface integration on macOS with floating record controls and interactive waveform playback with segment-following auto-scroll.
