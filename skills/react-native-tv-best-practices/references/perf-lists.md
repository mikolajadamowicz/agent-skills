# Lists and Grids: Virtualization Is Mandatory

TV UIs are grids of lists inside lists. Home screens have 10-15 rows with 10-20 items each. Without virtualization, your app will be unusable on TV hardware.

## Key Takeaways
- **Always virtualize** — Use FlashList or RecyclerListView, not ScrollView
- FlashList is faster and more memory-efficient than FlatList for large datasets
- Keep list items lightweight — avoid deep nesting, heavy shadows/gradients
- Memoize list items; preload images for next row in background
- Render hero row outside the virtualized list

## Why It's Worse on TV

- **Lower RAM:** Fewer off-screen items can stay in memory
- **No GPU tile caching:** On older Tizen/webOS, scrolling back = re-render from scratch
- **Focus-driven navigation:** Users whip through rows faster than mobile swipes
- **Large assets:** Movie posters, 4K stills are heavier than mobile thumbnails

## Bad: Non-Virtualized Grid
```jsx
<ScrollView>
  {rows.map((row) => (
    <Row key={row.id} data={row.items} />
  ))}
</ScrollView>
```
Every item in every row exists in memory all the time.

## Better: Virtualized with FlashList
```jsx
<FlashList
  data={movies}
  renderItem={renderPoster}
/>
```
Only a "window" of items exists in memory at any time.

## FlatList Optimization Props

If you must use FlatList, use all optimization props:

| Prop | Purpose |
|------|---------|
| `windowSize` | Smaller = less memory, more re-renders; larger = smoother scroll, more memory |
| `initialNumToRender` | Fill the screen but not much bigger |
| `getItemLayout` | Skip runtime measurement if items are same size |
| `maxToRenderPerBatch` | Control how many items render per batch |
| `removeClippedSubviews` | Remove off-screen items from native view hierarchy |

## Best Practices

1. **Virtualize your lists** — FlashList or RecyclerListView for horizontal and vertical lists. Avoid FlatList with huge data on older devices.

2. **Preload smartly** — Prefetch images for next screen/row in background. Keep preloads small — balance speed with memory pressure.

3. **Keep items lightweight** — Avoid deeply nested views with heavy shadows/gradients. These add GPU overhead.

4. **Memoize list items** — So they don't re-render unless data changes:
   ```jsx
   const Card = React.memo(({ poster, title }) => (
     <View><Image source={poster} /><Text>{title}</Text></View>
   ));
   ```

5. **Defer non-critical work** — If fetching additional metadata (ratings, trailers), load after row is visible or focused.

6. **Hero row outside list** — Render the hero row outside the virtualized list. This avoids recalculating its layout during scroll (saves several ms per frame).

## Platform Quirks

- **Tizen:** Prefetching too aggressively triggers out-of-memory reloads. Keep cache conservative.
- **webOS:** Scrolling performance drops if images aren't decoded yet — preload 1-2 screens ahead.
- **Apple TV:** Generally smoothest rendering, but older models still choke on giant grids.

## Related
- `perf-overview.md` — Overall performance strategy
- `perf-memory.md` — Image and memory optimization
- `design-layout.md` — Row/card layout patterns
