---
title: "Building suravidl: Architecting a Modular Multi-Platform Video Engine"
summary: "A deep dive into building an extensible web-video downloader powered by Python, FastAPI, and yt-dlp across desktop, mobile, and browser extensions."
date: "Oct 08 2026"
draft: false
tags:
- Python
- FastAPI
- yt-dlp
- Systems Architecture
- Chrome Extension
---

Downloading web media in the modern streaming era is deceptively challenging. Web video delivery has evolved far beyond static MP4 links into adaptive bitrate protocols like HLS (`.m3u8`) and MPEG-DASH (`.mpd`), split audio/video streams, authenticated DRM-free portals, and thousands of platform-specific manifests.

Most existing download tools either lock users into single-purpose command-line utilities, package bloated electron wrappers, or compromise user privacy with ad-heavy browser add-ons. 

With **suravidl**, the goal was to build a unified, high-performance video downloader engine: an extensible core built on **Python**, **FastAPI**, and **yt-dlp**, paired with lightweight client shells across Chrome/Edge/Firefox extensions, desktop executables, and native Android.

Here is a technical walkthrough of how suravidl was architected and the engineering principles behind its features.

---

### 1. Probe-First Architecture: Precision Stream Selection

A common issue with automated media scrapers is guessing formats. A naive download request often grabs an unexpected resolution, merges the wrong audio track, or fails completely when encountering multi-audio tracks.

To solve this, **suravidl** implements a **Probe-First** pipeline. When a user inputs a URL, the backend invokes `yt-dlp` in non-download extraction mode to inspect the stream metadata:

```python
from fastapi import FastAPI, HTTPException
import yt_dlp

app = FastAPI()

@app.post("/api/probe")
async def probe_media_stream(url: str):
    ydl_opts = {
        "extract_flat": False,
        "skip_download": True,
        "noplaylist": True,
    }
    with yt_dlp.YoutubeDL(ydl_opts) as ydl:
        try:
            info = ydl.extract_info(url, download=False)
            formats = [
                {
                    "format_id": f.get("format_id"),
                    "ext": f.get("ext"),
                    "resolution": f.get("resolution") or f"{f.get('width')}x{f.get('height')}",
                    "vcodec": f.get("vcodec"),
                    "acodec": f.get("acodec"),
                    "filesize": f.get("filesize") or f.get("filesize_approx"),
                    "fps": f.get("fps"),
                }
                for f in info.get("formats", [])
                if f.get("vcodec") != "none" or f.get("acodec") != "none"
            ]
            return {
                "title": info.get("title"),
                "thumbnail": info.get("thumbnail"),
                "duration": info.get("duration"),
                "formats": formats,
            }
        except Exception as e:
            raise HTTPException(status_code=400, detail=str(e))
```

By presenting this structured format manifest to the frontend, users can selectively specify their target video codec (`av01`, `vp9`, or `h264`), choose exact audio bitrates, and verify file sizes prior to initiating bandwidth-intensive jobs.

---

### 2. Resilient Live Stream Recording & "Stop & Keep"

Recording live streams introduces distinct edge cases compared to static on-demand video. Network disconnects, transport stream discontinuities, and server resets frequently terminate recordings unexpectedly.

`suravidl` handles live broadcasts through an isolated worker thread that streams raw transport segments into temporary segment buffers while exposing an on-air recording clock (`● REC`). 

Crucially, we implemented a **"Stop & Keep" safety mechanism**:
- Instead of relying on a clean stream closure from the host, user interruptions trigger a coordinated SIGINT to the underlying muxer process.
- The pipeline flushes all written transport chunks, fixes missing PTS/DTS presentation timestamps, and immediately remuxes captured buffers into a pristine MP4 or MKV container.
- Even if a stream drops or an unexpected reboot occurs, all captured footage up to the final second is preserved without index corruption.

---

### 3. Channel & Playlist Automated Watchlists

Content archival often requires tracking periodic uploads across channels or curated playlists. 

To prevent redundant downloads, `suravidl` implements an SQLite-backed archive database:
1. **Periodic Pollers:** Watchlists query feeds with minimal payloads using `extract_flat="in_playlist"`.
2. **Hash-Based Delta Checks:** Known video IDs stored in the archive index are skipped instantly without downloading manifests.
3. **Queue Prioritization:** New items are fed into a concurrency-limited download pool to prevent IP throttling from video providers.

---

### 4. Audio Extraction & Subtitle Pipeline

For music sets, podcasts, and lectures, audio fidelity and metadata preservation are paramount:

- **Lossless Extraction:** If the source stream provides native AAC, Opus, or FLAC tracks, `suravidl` extracts the container bitstream directly without lossy re-encoding.
- **Transcoding Options:** When requested, `ffmpeg` transcodes streams to MP3 (192–320 kbps) or Opus, injecting cover art and ID3 metadata directly from the video thumbnail.
- **Universal Subtitles:** Subtitle tracks are extracted either as native WebVTT, embedded soft subtitles inside the MP4/MKV container, or converted on the fly to SubRip (`.srt`) format for compatibility with smart TVs and media centers.

---

### 5. Multi-Platform Delivery & Media Sniffing

Rather than forcing users to operate solely through a web browser tab, `suravidl` distributes lightweight client shells:

- **Browser Extensions (Chrome, Firefox, Edge):** Monitors network headers via background service workers, sniffing direct `.m3u8`, `.mpd`, and `.mp4` URLs playing on any active tab. A single click dispatches the link and active session cookies to the local `suravidl` server.
- **Authenticated Sessions:** Full compatibility with Netscape `cookies.txt` and native browser cookie jar imports enables seamless downloads of private playlists or subscriber-only lectures.
- **Android & Desktop Wrappers:** A WebView-based Android application with an integrated media sniffer, alongside portable Windows builds that bundle Python and ffmpeg out of the box.

---

### Conclusion

By decoupling extraction logic into a robust FastAPI service and pairing it with targeted client integrations, `suravidl` transforms the fragmented landscape of video downloading into a reliable, automated, and user-controlled media pipeline.
