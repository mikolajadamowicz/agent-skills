# Debugging Video Streams

Video debugging requires specialized tools for media analysis, network inspection, and platform-specific behavior.

## Key Takeaways
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

### Charles Proxy
Application-layer view of HTTP/HTTPS communication. Valuable for:
- Inspecting manifest requests and responses
- Verifying DRM license exchange
- Monitoring adaptive bitrate switches

### Proxyman
Native macOS proxy tool with modern interface:
- Install CA certificate on simulator/emulator/device
- Intercept API calls, inspect headers and bodies
- Supports filtering, breakpoints, request rewriting

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

## Related
- `video-streaming.md` — Streaming architecture
- `video-players.md` — Player implementations
- `perf-overview.md` — Overall performance strategy
