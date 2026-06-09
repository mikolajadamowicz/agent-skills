# Performance Overview for TV

TV hardware is significantly weaker than modern phones. A 65" TV is often closer to a budget Android phone in CPU/GPU terms — while also decoding 4K video.

## Key Takeaways
- **Golden Rule:** Design for the weakest device, make it feel like the fastest one
- Optimize from day one, not as a pre-release checkbox
- Always keep a low-end streaming stick in your dev kit
- Treat your weakest device as the CI build target

## Why Performance Is Unforgiving on TV

- **CPU/GPU budgets are lower** — every unnecessary render competes with video decoding
- **Memory: 1-2 GB** — shared between OS, video buffer, and your app
- **Users notice everything** — 300ms input delay = "the app froze"; 45 FPS = visible stutter
- **TV chipsets are designed for video playback**, not high-performance UI rendering

## Device Tiers — Progressive Enhancement

### Low-End (Fire TV Stick Gen 1)
Keep it lean. Drop fancy gradients, heavy shadows, long transitions. Stick to snappy focus highlights, lightweight lists, instant feedback. Responsiveness beats visual flair.

### Mid-Range (Samsung Smart TV mid-tier)
Layer in some polish. Quick scale/fade here and there. Still performance-first.

### High-End (Apple TV 4K, Nvidia Shield)
Add visual polish: parallax banners, chained animations, cinematic transitions.

**Implementation:**
- Detect hardware class at runtime (device model, RAM, OS version)
- Maintain feature flags for performance tiers (basic, standard, enhanced)
- Shared baseline layout + conditional animations/effects per tier
- Test on real devices at each tier

## KPIs to Track

| Metric | Low-End Target | Mid-End | High-End |
|--------|---------------|---------|----------|
| Cold start time | <5s | <4s | <4s |
| Time to playback | <10s | <7s | <7s |
| Time to first meaningful paint | <3s | <2s | <1.5s |
| FPS during navigation | 60 | 60 | 60 |

## Key Performance Areas

1. **Startup time** — Only perform necessary operations at launch; defer everything else
   - JS bundle size: use Hermes, Re.Pack code splitting, Lazy Loading
   - Defer: analytics init, prefetching non-critical data

2. **Reactiveness** — Minimize latency between input and response
   - Delay non-essential operations while user navigates
   - Debounce rapid button presses
   - Show skeleton screens while loading

3. **Content loading** — Fetch only what's needed for display
   - Prioritize visible, above-the-fold content
   - Use pagination, not unbounded queries

4. **Network** — Every query costs time
   - Cache responses; pre-fetch next likely screen
   - Strip unused JSON fields for TV clients
   - Server-side image resizing

5. **Components** — Keep renders cheap
   - Memoize expensive components
   - Avoid unnecessary re-renders from state changes
   - Gradients, masks, animations are expensive — use sparingly

6. **Images** — Biggest performance impact after video
   - Download at display size, not full resolution
   - Never resize/scale/filter on client — do it on server

## Automating Performance Measurements

Manual testing doesn't scale across TV platforms:

```jsx
import { PerformanceObserver, performance } from 'react-native-performance';
performance.mark('app_start');
AppRegistry.registerComponent(appName, () => {
  performance.mark('app_registered');
  return App;
});
```

- Collect timestamps via automated tests on real devices
- Use AWS Device Farm for Fire TV/Android TV
- Push metrics to Grafana/Datadog for trend tracking
- Fail CI if metrics regress beyond thresholds

## Related
- `perf-animations.md` — Animation performance
- `perf-lists.md` — List virtualization
- `perf-network.md` — Network optimization
- `perf-memory.md` — Memory management
- `focus-performance.md` — Focus-related performance
