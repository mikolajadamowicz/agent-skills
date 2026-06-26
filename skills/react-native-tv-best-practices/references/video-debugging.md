---
title: Debugging Video Streams
impact: HIGH
tags: video, debugging, ffmpeg, ffprobe, charles, proxyman, profiling
---

# Debugging Video Streams

## Quick Reference
- Use FFmpeg/ffprobe to inspect media properties and verify codec compatibility
- Charles Proxy or Proxyman for network traffic analysis
- Correlate client-side errors with server-side telemetry
- React Native Profiler and Hermes Profiler for JS thread bottlenecks

## FFmpeg for Media Analysis

Indispensable for diagnosing playback issues:
- **ffprobe** — Inspect codecs, bitrates, resolutions for TV device compatibility
- **Transcoding tests** — Convert streams to HLS/DASH formats
- **Bandwidth simulation** — Simulate throttling to identify playback bottlenecks
- **mediastreamvalidator** (Apple) — Simulates HLS session to verify spec compliance

## Network Traffic Analysis

Use a proxy (Charles or Proxyman) to inspect manifest requests, verify the DRM license exchange, and watch adaptive-bitrate switches. Both require installing their CA certificate on the simulator/emulator/device to decrypt HTTPS — see the summary table below.

### Rozenite
Chrome DevTools plugin with network inspection tab for React Native apps.

## Performance Monitoring

- **React Native Profiler** — Track UI jank
- **Hermes Profiler** — JS thread bottlenecks
- **Custom metrics** — Playback startup time, frame drops
- **Analytics platforms** — Firebase Performance, Datadog
- **Server-side correlation** — AWS CloudFront logs, Mux Data

### Debugging Tools Summary

| Tool | Purpose |
|------|---------|
| FFmpeg/ffprobe | Media file inspection, transcoding |
| mediastreamvalidator | HLS spec validation (Apple) |
| Charles Proxy | HTTPS traffic inspection |
| Proxyman | Modern network debugging for Mac |
| Wireshark | Low-level packet capture |
| React Native Profiler | UI performance |
| Hermes Profiler | JS thread analysis |

## Related Skills
- [video-streaming.md](./video-streaming.md) — Streaming architecture
- [video-players.md](./video-players.md) — Player implementations
- [perf-overview.md](./perf-overview.md) — Overall performance strategy
