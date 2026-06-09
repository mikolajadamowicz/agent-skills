# Animation Performance on TV

Animations make a TV app feel polished — if they're smooth. On TV hardware, JS-driven animations tank performance fast because the JS thread competes with focus handling, list rendering, and playback controls.

## Key Takeaways
- **Always use `useNativeDriver: true`** for transforms and opacity
- Keep focus animations short: 100-150ms
- Avoid animating layout properties (width, height, margin) — use transforms instead
- Use Reanimated 3 for complex sequences (runs off JS thread via worklets)
- Test with the actual remote — keyboard/dev tools hide input lag

## Why It's Worse on TV

- **Tight CPU budgets:** Fire TV Stick Lite JS thread runs ~200-300 MHz while decoding 4K
- **Remote input expectations:** Unlike touch, TV navigation feels broken if animations delay focus
- **Extra compositor hops:** Tizen/webOS have more rendering pipeline layers

## Focus Scale Animation

**Bad (JS thread):**
```jsx
const [scale, setScale] = useState(1);
useEffect(() => {
  if (isFocused) setScale(1.1);
  else setScale(1);
}, [isFocused]);
<View style={{ transform: [{ scale }] }} />
```

**Better (native-driven):**
```jsx
const scale = useRef(new Animated.Value(1)).current;
useEffect(() => {
  Animated.spring(scale, {
    toValue: isFocused ? 1.1 : 1,
    useNativeDriver: true,
  }).start();
}, [isFocused]);
<Animated.View style={{ transform: [{ scale }] }} />
```

Runs entirely on UI thread — JS is free for input and logic.

## Chained Animations

JS-driven chains (fade → scale → shadow) cause multiple layout passes.

**Better:**
- Combine into one `Animated.parallel` call, all using native driver
- Or use **Reanimated 3** to orchestrate in a single worklet off JS thread

## Best Practices

1. **`useNativeDriver` everywhere possible** — especially for transforms (scale, translate, rotate) and opacity. These are GPU-friendly.

2. **Keep animations short:**
   - 100-150ms for focus changes = feels instant
   - 300-500ms "hero" animations only for big transitions

3. **Avoid layout-changing animations** — `width`/`height`/`margin` force layout recalculation (slow on low-power CPUs). Use transforms or opacity instead.

4. **Prefer Reanimated for complex sequences** — worklets run natively without blocking JS. Good for home-screen heroes, auto-scrolling carousels, parallax.

5. **Test with actual remote** — keyboard and dev tools hide input lag. Even 50ms extra delay is noticeable on a remote.

## Platform Quirks

- **Apple TV:** Native focus engine provides subtle scaling — avoid doubling unless you disable defaults
- **Tizen:** Focus/blur events can be delayed by OS — match animation durations to platform responsiveness
- **Fire TV:** Aggressive frame skipping if animations aren't native-driven; can drop to 30 FPS instantly

> When in doubt, animate less. On TV, a fast and crisp focus change beats a slow, fancy effect every time.

## Related
- `focus-performance.md` — Focus-specific render optimization
- `perf-overview.md` — Overall performance strategy
- `perf-lists.md` — List scrolling performance
