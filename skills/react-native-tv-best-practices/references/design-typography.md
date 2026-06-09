# Typography for TV Displays

Typography that works on phones fails on TV. Distance, panel tech, ambient light, and OS rendering push designs toward bigger type, clearer spacing, and higher contrast.

## Key Takeaways
- TV text needs to be **50-80% larger** than mobile equivalents
- Minimum body text: 24px (not 16px like mobile)
- Avoid ultra-light/ultra-thin weights — they shimmer on LCDs, bloom on OLEDs
- Use platform system fonts for best rendering quality

## Minimum Font Sizes

| Text Style | Mobile | TV | Increase | Use Case |
|-----------|--------|-----|----------|----------|
| Body | 16px | 24px | +50% | Descriptions, paragraphs |
| Caption | 12px | 20px | +67% | Metadata, labels, tags |
| Button | 14px | 22px | +57% | Interactive elements, CTAs |
| Heading | 20px | 32px | +60% | Section titles, categories |
| Title | 28px | 48px | +71% | Page titles, hero text |
| Display | 36px | 64px | +78% | Large promotional text |

```jsx
const tvTypography = StyleSheet.create({
  body:    { fontSize: 24, lineHeight: 32, fontWeight: '400' },
  button:  { fontSize: 22, lineHeight: 28, fontWeight: '600' },
  caption: { fontSize: 20, lineHeight: 26, fontWeight: '400' },
  heading: { fontSize: 32, lineHeight: 40, fontWeight: '600' },
  title:   { fontSize: 48, lineHeight: 56, fontWeight: '700' },
});
```

## Platform-Specific Typefaces

- **tvOS:** San Francisco (SF Pro Display / SF Pro Text) — crisp at larger sizes
- **Android TV / Fire TV:** Roboto — wide glyph support, predictable metrics
- **Fallbacks / multi-script:** Inter, Noto Sans families — broad language coverage

## Line Spacing and Letter Spacing

| Context | Line Height | Letter Spacing | Best For |
|---------|-------------|----------------|----------|
| Content (paragraphs) | 1.4x | 0.4px | Long-form reading |
| Navigation (menus) | 1.2x | 0.5px | Menu items, scanning |
| Display (large text) | 1.15x | -0.4px to -0.8px | Hero text, titles |

Negative tracking for large titles: huge sizes amplify default spacing; tightening avoids airy gaps.

## Text Rendering on TV

TVs expose edge cases with subpixel rendering:
- **Text over images:** Add subtle text shadow to separate from backgrounds
  ```jsx
  const readableOnImage = {
    color: '#FFF',
    textShadowColor: 'rgba(0,0,0,0.35)',
    textShadowOffset: { width: 0, height: 1 },
    textShadowRadius: 2,
  };
  ```
- **Subtitles/over-video:** Combine shadow + stroke at low opacity (not heavy blur)
- **Safe zone:** Keep text inside safe zone — overscan clips labels at edges
- **Tile titles:** `numberOfLines={2}` + `ellipsizeMode="tail"` — avoid wrapping issues
- **Long localized titles:** Gentle marquee only on focus, never by default

## Practical Tips

- Use **sentence case** over ALL CAPS for body/captions — caps reduce word shape recognition
- **Avoid decorative fonts** — prioritize legibility for UI text (buttons, cards)
- Choose fonts with **large counters** and adequate width for instant recognition
- Optimize for **"at-a-glance" readability**
- Recommended open-source fonts: Roboto, Noto Sans, Open Sans, DM Sans, Inter

## Related
- `design-10foot.md` — 10-foot experience design principles
- `design-color.md` — Color and contrast guidelines
- `design-layout.md` — Layout and spacing patterns
