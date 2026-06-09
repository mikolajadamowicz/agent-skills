# Memory Management on TV

On TVs, you're sharing RAM with the OS, video decoder, DRM, audio buffers, and even the live TV tuner. Your UI runs in the leftovers.

## Key Takeaways
- Many devices have 1-1.5 GB total — your app might only get 300-500 MB
- 4K video streams eat 100-200 MB just for decoded frames
- Image optimization is the #1 memory lever
- Smart TVs aggressively reclaim memory from your app

## Symptoms of Memory Pressure

- Sudden GC spikes (frame drops during scrolling)
- Images unloading from cache and re-downloading mid-session
- Crashes or forced restarts (Tizen and webOS are notorious)

## Image Memory Optimization

**Bad:**
```jsx
<Image source={{ uri: posterUrl }} />
```
Without cache control, changing `posterUrl` holds multiple decoded bitmaps until GC runs.

**Better:**
```jsx
<Image
  source={{ uri: posterUrl, cache: 'force-cache' }}
  resizeMode="cover"
/>
```
Or use `react-native-fast-image` for cache control.

### Image Best Practices
- **Pre-scale on server** to match actual on-screen size
- **Use WebP/AVIF** for posters, JPEG for large backdrops
- Avoid decoding a 4K asset for a 200px thumbnail — wasted RAM
- Use memory-aware caching libraries that evict under pressure

## List Item Memory

Even with virtualization, if row components keep large objects in state (full metadata blobs), you're holding memory hostage.

**Better:** Store only IDs in list item state, fetch full details on demand.

## Best Practices

1. **Optimize images** — Pre-scale server-side, use efficient formats, match display dimensions.

2. **Release what you're not using** — Null out refs for large objects on unmount. Stop/clean up animations when leaving screen.

3. **Mind your caches** — Control image caching strategy (`force-cache` vs `reload`). Use memory-aware libraries.

4. **Watch JS object lifetimes** — Avoid keeping entire Redux/Zustand states in memory. Use selectors to keep component subscriptions lean.

5. **Use native profiling tools:**
   - Android TV/Fire TV: Android Studio Profiler → Memory tab
   - Apple TV: Xcode Instruments → Allocations + Leaks
   - Tizen/webOS: Emulator memory usage overlays

## Platform Quirks

- **Low-end Fire TV:** ~0.5-1 GB RAM total. Every extra library adds startup time.
- **Tizen/webOS:** Aggressive OS memory reclaim — your app can be killed without warning.
- **Apple TV 4K:** More generous RAM (4 GB) but don't assume you can skip optimization.

## Related
- `perf-overview.md` — Overall performance strategy
- `perf-lists.md` — Virtualized lists reduce memory
- `perf-network.md` — Caching and payload optimization
