---
title: Accessibility Implementation in React Native TV
impact: HIGH
tags: accessibility, screen-reader, focus, voiceover, talkback, tv
---

# Accessibility Implementation in React Native TV

Practical guide to implementing accessibility across TV platforms with code examples for common patterns.

## Quick Reference
- Set `accessible={true}` on all interactive elements
- Every focusable element needs `accessibilityLabel` + `accessibilityRole`
- Use `accessibilityLiveRegion="polite"` for dynamic content updates
- Prefer `TVFocusGuideView` for cross-platform focus management; use `nextFocus*` only as a targeted override
- Test on real devices with screen readers enabled

## Making Elements Accessible

```jsx
<View
  accessible={true}
  accessibilityRole="button"
  accessibilityLabel="Watch Trailer"
  accessibilityHint="Press center button to play the trailer"
>
  <Image source={trailerImage} style={styles.thumbnail} />
  <Text style={styles.caption}>Watch Trailer</Text>
</View>
```

> `accessible={true}` at parent groups all children into one selectable component. Be careful — too far up in hierarchy makes components inaccessible.

## Roles

Common roles for TV apps:
- `button` — Triggers an action
- `header` — Section header or title
- `image` — Visual content
- `text` — Static/dynamic text
- `imagebutton` — Image acting as button
- `adjustable` — Sliders, knobs
- `link` — Navigation (inconsistent on Android TV)
- `dialog` — Modal dialogs
- `switch` — Toggle controls

## Labels and Hints

```jsx
<TouchableOpacity
  accessibilityLabel="Subscribe to Stranger Things"
  accessibilityHint="Press OK to Subscribe"
  accessibilityRole="button"
>
  <Text>Subscribe</Text>
</TouchableOpacity>
```

| Label | Hint |
|-------|------|
| "Play" | "Plays the selected movie" |
| "Delete" | "Removes this item from your list" |
| "Stranger Things" | "Press OK for more details" |

Use hints only when outcome is not obvious from label/context.

## Dynamic Content

Announce updates to screen readers:
```jsx
<View accessibilityLiveRegion="polite">
  {results.map(result => (
    <Text key={result.id}>{result.title}</Text>
  ))}
</View>
```

For major changes:
```jsx
AccessibilityInfo.announceForAccessibility('Added to Watchlist');
```

## Accessible Modals

```jsx
<Modal
  visible={isVisible}
  accessibilityViewIsModal={true}
  onRequestClose={handleClose}
>
  <View>
    {/* Don't wrap the body in `accessible` — it collapses children into one
        element and hides the buttons from focus. Label a header node instead. */}
    <Text accessibilityRole="header">Continue watching?</Text>
    <Text>Are you still watching?</Text>
    <Button title="Yes" onPress={handleContinue} />
    <Button title="No" onPress={handleExit} />
  </View>
</Modal>
```

- Set initial focus inside modal
- Trap focus within modal
- Announce dialog opening

## Scrollable Content

```jsx
<FlatList
  horizontal
  data={movies}
  renderItem={({ item }) => (
    <TouchableOpacity
      focusable={true}
      accessibilityLabel={`Movie: ${item.title}`}
    >
      <Image source={{ uri: item.thumbnail }} />
    </TouchableOpacity>
  )}
/>
```

Add section headings:
```jsx
<View accessible accessibilityRole="header" accessibilityLabel="Top Stories">
  <Text style={{ fontSize: 20, fontWeight: 'bold' }}>Top Stories</Text>
</View>
```

## Icons Without Text

```jsx
<TouchableOpacity accessibilityLabel="Delete item">
  <Icon name="trash" />
</TouchableOpacity>
```

Hide purely decorative icons with `accessible={false}`.

## Audio Description Toggle

```jsx
<TouchableOpacity
  onPress={toggleAudioDescription}
  accessibilityRole="switch"
  accessibilityLabel="Audio Description"
  accessibilityState={{ checked: audioDescriptionEnabled }}
>
  <Text>Audio Description: {audioDescriptionEnabled ? 'On' : 'Off'}</Text>
</TouchableOpacity>
```

## Autoplay Content

- Mute by default unless user opts in
- Include remote-focusable playback controls
- `AccessibilityInfo.isScreenReaderEnabled()` returns a **Promise** — `await` it (or subscribe to the `screenReaderChanged` event) before deciding whether to mute; a bare synchronous `if` is always truthy:
  ```jsx
  const srOn = await AccessibilityInfo.isScreenReaderEnabled();
  if (srOn) muteAutoplay();
  ```
- Apple TV: respect system audio focus, integrate with `AVAudioSession`

## Focus on Screen Changes

```jsx
useEffect(() => {
  focusableElementRef.current?.requestTVFocus();
}, []);
```

## Cross-Platform Focus

Prefer `TVFocusGuideView` over `nextFocus*` for shared layouts. `destinations` takes an array of resolved components (`ref.current`), not the ref objects and not string IDs. Don't build it inline — `ref.current` is `null` on the first render and mutating a ref triggers no re-render, so the guide would register nothing. Hoist the resolved components into state once the children have mounted:
```jsx
const playRef = useRef(null);
const infoRef = useRef(null);
const [destinations, setDestinations] = useState([]);

// Children assign their refs after this component first renders; push the
// resolved nodes into state so a NEW array is passed and the guide updates.
useEffect(() => {
  setDestinations([playRef.current, infoRef.current].filter(Boolean));
}, []);

<TVFocusGuideView destinations={destinations}>
  <TouchableOpacity ref={playRef} accessibilityRole="button" accessibilityLabel="Play" />
  <TouchableOpacity ref={infoRef} accessibilityRole="button" accessibilityLabel="Info" />
</TVFocusGuideView>
```
See [focus-management.md](./focus-management.md) for when `nextFocus*` overrides are acceptable.

## Common Pitfalls

| Issue | Solution |
|-------|----------|
| Icons without labels | Add `accessibilityLabel` to all icon buttons |
| Modals not announced | Use `accessibilityViewIsModal`, `accessibilityRole="dialog"` |
| Dynamic content invisible to screen reader | Use `accessibilityLiveRegion="polite"` |
| Focus on non-interactive elements | Only set `focusable={true}` on interactive items |
| Custom components inaccessible | Add `accessible`, role, state, label to all custom components |
| Visual-only feedback | Pair with `announceForAccessibility()` |

## Related Skills
- [a11y-overview.md](./a11y-overview.md) — Accessibility fundamentals for TV
- [a11y-checklist.md](./a11y-checklist.md) — Pre-launch checklist
- [focus-management.md](./focus-management.md) — Focus APIs
