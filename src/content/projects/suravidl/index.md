---
title: "suravidl"
summary: "A universal web-video downloader engine powered by Python, FastAPI, and yt-dlp, with cross-platform browser extensions, desktop, and Android apps."
date: "Oct 08 2026"
draft: false
tags:
- Python
- FastAPI
- yt-dlp
- JavaScript
- Chrome Extension
- Android
repoUrl: https://github.com/LoLyeah/suravidl
---

**suravidl** is a universal web-video downloader built with a modular architecture: a unified core engine (Python with `yt-dlp` as an embedded library and FastAPI on top), a clean responsive web UI, and thin shells across browser extensions (Chrome, Edge, Firefox), desktop (Windows portable and installer), and native Android.

It supports over 1,000 video streaming platforms as well as direct `.mp4`, `.webm`, `.m3u8`, and `.mpd` links without DRM.

### Key Features
- 🔍 **Probe-First Architecture:** Inspect actual audio/video streams, resolutions, and codecs before queuing, ensuring you get exactly the format desired.
- 📋 **Batch Ingestion:** Paste multiple URLs simultaneously; valid links are pre-probed and titled, allowing selective downloading with detailed error reporting.
- 🔴 **Live Stream Recording:** Record live broadcasts with real-time on-air clocks (● REC) and a "Stop & Keep" safety mechanism to preserve all captured footage.
- 📺 **Channel & Playlist Watchlists:** Monitor YouTube channels and playlists for automated downloads, automatically skipping previously fetched media.
- 🎵 **High-Fidelity Audio Extraction:** Keep native streams or transcode into MP3 (192–320 kbps), FLAC, Opus, or M4A with embedded album art.
- 💬 **Subtitle Management:** Download site-native subtitles, embed subtitles directly into video containers, or convert to `.srt` format for TV compatibility.
- 🌐 **Media Sniffing & Authenticated Sites:** Sniff media streams directly from web pages using the extension or Android in-app browser, with full support for `cookies.txt` and browser session imports.
- ⚡ **Production Polish:** Automatic retry with editable configurations, pause/resume download pipelines, SponsorBlock chapter filtering, and in-browser media playback.
