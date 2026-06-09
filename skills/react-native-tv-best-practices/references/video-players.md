# Video Players for React Native TV

From simple playback with react-native-video to enterprise-grade streaming with Shaka Player — choose the right level of abstraction for your needs.

## Key Takeaways
- `react-native-video` covers most use cases (HLS/MP4, basic DRM, built-in controls)
- For enterprise-grade (live sports, complex DRM, custom ABR): consider Shaka Player or native players
- Custom controls: add a ref to Video component and build React Native UI on top
- Thumbnail previews: BIF format is most performant for seek bar thumbnails

## Available Players

| Player | Platform | Best For |
|--------|----------|----------|
| AVPlayer | iOS, tvOS | Native Apple playback, FairPlay DRM |
| ExoPlayer | Android TV, Fire TV | Wide format support, Widevine DRM |
| react-native-video | Cross-platform | Wraps AVPlayer + ExoPlayer; easiest setup |
| react-native-theoplayer | Cross-platform | THEOplayer SDK wrapper |
| Shaka Player | JS (all platforms) | DASH + HLS, advanced ABR, multiple DRMs |
| hls.js | Web-based TVs | HLS playback in browsers |
| dash.js | Web-based TVs | MPEG-DASH reference player |

## react-native-video — Basic Setup

```jsx
<Video
  source={{ uri: 'https://example.com/video.mp4' }}
  drm={{
    type: DRMType.WIDEVINE,
    licenseServer: 'https://license.example.com/widevine',
    getLicense: fetchLicense,
    headers: { Authorization: 'Bearer your-token' },
  }}
  style={{ width: '100%', aspectRatio: 16 / 9 }}
  controls
  onLoadStart={() => console.log('Requesting manifest & license')}
  onReadyForDisplay={() => console.log('Decryption complete')}
  onError={(e) => console.error('Error:', e)}
  onEnd={() => console.log('Session complete, cleanup')}
/>
```

## Custom Controls

```jsx
<View style={{ flex: 1 }}>
  <Video
    source={{ uri: 'https://example.com/video.mp4' }}
    ref={videoRef}
    onProgress={({ currentTime }) => setProgress(currentTime)}
  />
  <View style={styles.controls}>
    <TouchableOpacity onPress={() => videoRef.current?.pause()}>
      <Icon name="pause" />
    </TouchableOpacity>
    <Slider
      value={progress}
      onSlidingComplete={(value) => videoRef.current?.seek(value)}
    />
  </View>
</View>
```

## Seek Bar Implementation

```jsx
const [currentTime, setCurrentTime] = useState(0);
const [duration, setDuration] = useState(0);
const [seeking, setSeeking] = useState(false);

<Video
  ref={videoRef}
  onProgress={(data) => { if (!seeking) setCurrentTime(data.currentTime); }}
  onLoad={(data) => setDuration(data.duration)}
/>
<Slider
  value={currentTime}
  maximumValue={duration}
  onValueChange={(time) => { setSeeking(true); setCurrentTime(time); }}
  onSlidingComplete={(time) => { videoRef.current.seek(time); setSeeking(false); }}
/>
```

## Thumbnail Generation — BIF Format

The Broadcast Image Format bundles all thumbnails in one indexed binary file:
- Single network request (vs. individual image downloads)
- Indexed structure for instant lookup by timestamp
- `thumbIndex = Math.floor(videoTime / interval)`

For large videos (2+ hours), use a native module for BIF parsing. Cache BIF files locally.

## Focus During Scrubbing

- **Debounce thumbnail updates** (100-200ms) during rapid scrubbing
- **Preload adjacent thumbnails** during idle moments
- **Lazy load** distant timeline parts to optimize memory

## Shaka Player for Enterprise

For complex streaming (live sports, multi-DRM, custom ABR):
1. Build Shaka Player for React Native using provided build script
2. Create Turbo Native Modules bridging to AVPlayer/ExoPlayer
3. Build a ShakaPlayerComponent to bridge native to React Native

Shaka supports DASH + HLS, offline playback, and custom ABR logic. Runs in React Native's JS core and communicates with native players.

## When to Use What

| Scenario | Recommendation |
|----------|---------------|
| Basic HLS/MP4 playback | react-native-video |
| Simple DRM (single platform) | react-native-video with DRM config |
| Enterprise multi-DRM, live sports | Shaka Player or native players |
| Web-based TVs (Tizen, webOS) | Shaka Player, hls.js, or dash.js |

## Related
- `video-streaming.md` — Streaming architecture and protocols
- `video-debugging.md` — Debugging tools
- `focus-management.md` — Focus handling during playback
