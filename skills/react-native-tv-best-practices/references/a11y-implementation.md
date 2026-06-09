# Accessibility Implementation in React Native TV

Practical guide to implementing accessibility across TV platforms with code examples for common patterns.

## Key Takeaways
- Set `accessible={true}` on all interactive elements
- Every focusable element needs `accessibilityLabel` + `accessibilityRole`
- Use `accessibilityLiveRegion="polite"` for dynamic content updates
- Use `TVFocusGuideView` for cross-platform focus management (not `nextFocus*`)
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
  <View accessible accessibilityLabel="Continue watching?">
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
- Use `AccessibilityInfo.isScreenReaderEnabled()` to conditionally mute
- Apple TV: respect system audio focus, integrate with `AVAudioSession`

## Focus on Screen Changes

```jsx
useEffect(() => {
  focusableElementRef.current?.requestTVFocus();
}, []);
```

## Cross-Platform Focus (Avoid nextFocus*)

`nextFocus*` props only work on Android TV. Use `TVFocusGuideView` for cross-platform:
```jsx
<TVFocusGuideView autoFocus destinations={['button1', 'button2']}>
  <TouchableOpacity nativeID="button1" ... />
  <TouchableOpacity nativeID="button2" ... />
</TVFocusGuideView>
```

## Common Pitfalls

| Issue | Solution |
|-------|----------|
| Icons without labels | Add `accessibilityLabel` to all icon buttons |
| Modals not announced | Use `accessibilityViewIsModal`, `accessibilityRole="dialog"` |
| Dynamic content invisible to screen reader | Use `accessibilityLiveRegion="polite"` |
| Focus on non-interactive elements | Only set `focusable={true}` on interactive items |
| Custom components inaccessible | Add `accessible`, role, state, label to all custom components |
| Visual-only feedback | Pair with `announceForAccessibility()` |

## Related
- `a11y-overview.md` — Accessibility fundamentals for TV
- `a11y-checklist.md` — Pre-launch checklist
- `focus-management.md` — Focus APIs
