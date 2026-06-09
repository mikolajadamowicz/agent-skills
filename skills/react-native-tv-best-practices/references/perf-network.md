# Network Performance on TV

On TV, there's no "loading spinner safety net." People expect content to instantly fill the screen. If nothing happens after a remote press, they'll assume the app froze — and press again (duplicate requests).

## Key Takeaways
- Show something within 200ms of navigation — even a blurred poster or placeholder
- Prefetch the next likely screen while current one is stable
- Never block navigation/focus on network responses
- Debounce input during network stalls to prevent duplicate requests

## Why It's Worse on TV

- **Wi-Fi is often bad:** TVs in far corners, running on 2.4 GHz with packet loss/jitter
- **Platform timeouts:** Tizen aggressively kills "stuck" requests
- **Large payloads:** Home screen = multiple JSON payloads + dozens of poster images
- **No background fetch:** TVs don't run your app in background between sessions

## Problem: Blocking Navigation

**Bad:**
```jsx
const onRowFocus = async (rowId) => {
  const details = await fetchRowDetails(rowId); // Blocks focus
  setDetails(details);
};
```
User presses down, nothing highlights until network returns.

**Better (optimistic UI):**
```jsx
const onRowFocus = (rowId) => {
  highlightRow(rowId); // Instant visual feedback
  fetchRowDetails(rowId).then(setDetails);
};
```

## Prefetching

```jsx
useEffect(() => {
  prefetchRowData(currentRow + 1);
}, [currentRow]);
```

Hides network latency during fast remote presses.

## Best Practices

1. **Prefetch where it matters** — Preload next likely screen/row; keep payloads small.

2. **Use placeholders instead of blocking** — Show cached posters first, swap in updated metadata. Avoid "all-or-nothing" loading on home screen.

3. **Prioritize hero content** — Load top banner row first (first thing user sees). Defer secondary rows.

4. **Handle retries gracefully** — Remote presses during stalls should not spam duplicate requests. Debounce input or use request-in-flight check.

5. **Optimize payload size** — Strip unused JSON fields for TV clients. Use server-side image resizing.

6. **Smart caching** — `force-cache` for static assets (thumbnails, logos). Sensible `max-age` headers for repeat sessions.

## Platform Quirks

- **Tizen:** Requests >5 seconds can be killed without warning. Set shorter timeouts + retry logic.
- **webOS:** Some models cache aggressively in firmware — add version param to URLs.
- **Fire TV:** Prefetching too aggressively on slow Wi-Fi spikes memory (cached images + JSON) → GC stutter.
- **Apple TV:** Fast on wired Ethernet but test on Wi-Fi too.

> Optimize first paint time, not just throughput. Show something within 200ms of navigation.

## Related
- `perf-overview.md` — Overall performance strategy
- `perf-memory.md` — Memory impact of caching
- `perf-lists.md` — Virtualized list rendering
