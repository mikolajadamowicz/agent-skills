# Video Streaming Fundamentals

Most TV apps are built around video streaming. Understanding the architecture — from protocols to DRM — helps you debug the parts you control.

## Key Takeaways
- Adaptive Bitrate Streaming (ABR) adjusts quality based on bandwidth in real-time
- HLS (Apple) and DASH (industry standard) are the two dominant protocols
- DRM is non-negotiable: Widevine (Google), FairPlay (Apple), PlayReady (Microsoft)
- Your CDN, encoding, and DRM are usually already chosen — understand the stack to debug effectively

## Streaming Architecture

Every streaming pipeline has these layers:
1. **Content Source** — Raw content: live feeds, VOD assets, CDN files
2. **Protocol Layer** — HLS or DASH, breaking video into chunks with a manifest
3. **Player Abstraction** — AVPlayer, ExoPlayer, react-native-video, Shaka Player
4. **DRM Abstraction** — Widevine, FairPlay, PlayReady with key exchange
5. **UI Layer** — Controls: play/pause, seekbar, error screens

## Adaptive Bitrate Streaming (ABR)

How ABR works:
1. **Segmentation:** Video divided into small segments (few seconds), each at multiple bitrates
2. **Manifest file:** M3U8 (HLS) or MPD (DASH) lists available bitrates and segment URLs
3. **Initial playback:** Player requests average bitrate to gauge conditions
4. **Monitoring:** Player continuously monitors network speed and buffer levels
5. **Adjustment:** High bandwidth → higher bitrate; low bandwidth → lower bitrate

## Protocols

### HLS (HTTP Live Streaming)
Apple's protocol. Go-to for iOS, tvOS, Safari. Uses M3U8 manifest files.

### DASH (Dynamic Adaptive Streaming over HTTP)
Industry standard. Works across platforms. Uses MPD manifest files.

Both break video into segments delivered over HTTP.

## Digital Rights Management (DRM)

| DRM | Provider | Platforms | Protocol Support |
|-----|----------|-----------|-----------------|
| Widevine | Google | Android, Chrome | DASH, HLS |
| FairPlay | Apple | iOS, tvOS, Safari | HLS |
| PlayReady | Microsoft | Windows, Xbox | DASH, HLS, Smooth Streaming |

Each DRM requires:
- A valid license server
- App entitlements/permissions
- Sometimes native player configuration (security level, hardware decryption)

## Video Player Lifecycle

1. **Content request** — Player requests manifest file
2. **DRM license request** — Player asks for decryption license
3. **DRM license response** — Server returns decryption keys (stored for session)
4. **Playback** — Decrypted content plays
5. **Error handling** — Graceful handling of decryption failures, expired licenses
6. **License renewal** — Periodic renewal if time-limited
7. **Cleanup** — Session termination on playback end

## Related
- `video-players.md` — Player implementations and custom controls
- `video-debugging.md` — Debugging tools for video streams
