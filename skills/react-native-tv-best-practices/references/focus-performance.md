# Focus Performance

On TV, every D-pad press sends focus change events. Careless handling triggers dozens of component updates per press, tanking your frame rate.

## Key Takeaways
- Keep focus effects local — don't update global state for visual changes
- Use `React.memo` to prevent full-row re-renders on focus change
- Prefer a single top-level focus frame over per-card overlays
- Use transforms (scale, translate) not layout properties (width, height) for focus animations
- Batch focus updates in one render frame

## Why It's Worse on TV
- **Focus cascades:** Changing focus in one row can cause style updates in multiple rows
- **Platform quirks:** Tizen/webOS trigger both onBlur and onFocus for multiple elements in quick succession
- **Remote latency:** Bluetooth/IR remotes already have inherent latency — any JS thread delay makes it worse
- Users hold directions or rapidly press buttons — your app must process multiple focus events per second

## Problem: Cascading Renders

**Bad** — Every card stores its own focus state in React state:
```jsx
const [isFocused, setIsFocused] = useState(false);
useEffect(() => {
  if (focusedId === id) setIsFocused(true);
  else setIsFocused(false);
}, [focusedId]);
```
Every change to `focusedId` re-renders every card in the row.

**Better** — Memoize the card:
```jsx
const Card = React.memo(({ isFocused, poster }) => (
  <Image
    style={isFocused ? styles.focused : styles.normal}
    source={poster}
  />
));
```
Only the focused card re-renders, not the whole row.

## Problem: Overlay Flicker

**Bad** — Unmounting/remounting overlays on focus:
```jsx
{isFocused && <FocusOverlay />}
```

**Better** — Toggle opacity:
```jsx
<View style={{ opacity: isFocused ? 1 : 0 }}>
  <FocusOverlay />
</View>
```
Avoids flicker but mounts dozens of hidden overlays consuming memory.

**Best** — Single top-level focus frame:
```jsx
const [frame, setFrame] = useState(null);
const onCardFocus = (ref) => {
  ref.current?.measure((x, y, w, h, pageX, pageY) => {
    setFrame({ x: pageX, y: pageY, w, h });
  });
};

return (
  <View style={{ flex: 1 }}>
    <FlatList data={movies} renderItem={({ item }) => {
      const ref = useRef(null);
      return (
        <Card ref={ref} item={item} onFocus={() => onCardFocus(ref)} />
      );
    }} />
    {frame && (
      <Animated.View style={[styles.absolute, {
        left: frame.x, top: frame.y,
        width: frame.w, height: frame.h
      }]} />
    )}
  </View>
);
```
One focus frame moves around — no duplication, minimal re-renders.

## Best Practices

1. **Keep focus effects local** — Don't update Redux/Zustand for visual focus changes. Pass focus as context value if multiple subcomponents need it.

2. **Preload focus styles** — Shadows, glows, gradients are GPU-expensive to generate on the fly. Pre-render them and toggle visibility.

3. **Batch focus updates** — Use `unstable_batchedUpdates` or a single `setState` to update multiple UI elements on focus.

4. **Avoid layout shifts** — Use transforms (scale, translate) for focus animations, not layout properties like width/height. This keeps GPU work cheaper.

5. **Use platform-specific focus helpers:**
   - On Android TV/Fire TV: `focusable` prop reduces extra focus jumps
   - On Apple TV/Android TV/Fire TV: `TVFocusGuideView` to control focus without excessive JS

## Related
- `focus-management.md` — Core focus APIs and debugging
- `perf-animations.md` — Animation performance on TV
- `perf-overview.md` — Overall TV performance strategy
