# Layout Patterns and Common Components

TV interfaces share a common structure: hero section at top, rows of content below, menu on the side. Familiarity reduces cognitive effort and lets users focus on content.

## Key Takeaways
- A well-designed layout provides: Orientation (where am I?), Context (what can I do?), Momentum (where can I go?)
- Use safe zones (5-10% margin) to prevent overscan clipping
- Use relative units, not fixed pixels, for responsive TV design
- Test on multiple display modes, brands, and sizes

## Cards and Content Tiles

The foundation of content-focused TV interfaces:
- Each card represents one piece of content (movie, show, playlist)
- **Focus feedback:** 3-5% scale increase, glow/drop shadow for depth
- Include: title, thumbnail, one secondary detail (runtime, badge)
- Leave spacing so focus indicators never overlap adjacent cards
- Avoid cramming too much info — cards are entry points, not summaries

```jsx
<Card
  image={poster}
  title="The Great Adventure"
  onFocus={() => setHighlight(id)}
  onPress={() => openDetails(id)}
/>
```

## Rows / Swimlanes

Organize cards horizontally — the signature look of streaming apps:
- **Virtualize:** Render only visible items with `FlatList` or `FlashList`
- Left/right within a row, up/down between rows
- Auto-scroll when focus reaches row edge
- Label each row clearly ("Recommended", "New Releases")
- Fewer, more distinct categories > overwhelming options

```jsx
<Row title="Recommended for You">
  {data.map((item) => <Card key={item.id} {...item} />)}
</Row>
```

## Hero Headers

Large visual element at top of screen:
- Anchors attention and sets tone
- Feature a single piece of content
- Strong composition — fills space naturally, not cropped
- Readable text with subtle gradients or blurs
- Single primary action ("Play", "Resume", "More Info")
- Smooth transitions to first content row

## Buttons

Appear less frequently than mobile but must be obvious:
- Focused buttons: contrast, outlines, or subtle scaling
- Group related actions ("Play" + "More Info") with consistent spacing
- Short verb labels: "Play", "Retry", "Cancel"

## Overlays

Video controls, pause menus:
- Fade in quickly, fade out after inactivity
- Predictable focus order (left to right)
- Dim content beneath but don't hide completely

```jsx
<Overlay visible={paused}>
  <Button label="Play" onPress={resume} />
  <Button label="Restart" onPress={restart} />
  <Button label="Exit" onPress={exitToMenu} />
</Overlay>
```

## Safe Zones

Many TVs apply overscan — outer 5-10% may get cropped:
- Keep all essential elements (text, logos, buttons) inside 5-10% margin
- Backgrounds and hero images can extend to the edge
- Use gridlines or bounding boxes during development to visualize safe boundaries

## Responsive TV Design

TVs range from 32" to 85" and don't all render pixels identically:
- **Use relative units** (viewport height/width, percentages) not fixed pixels
- **Anchor to screen edges** (top, left, right) not absolute coordinates
- **Center critical content** — peripheral areas less reliable
- **Test multiple display modes:** Standard, Cinema, Game, HDR
- Design around 16:9 base grid, ensure it adapts to 21:9 without breaking

## Spacing

- Align content to grid system (12-column or 8-column)
- Consistent vertical rhythm between rows (1.5x card height for padding)
- Invisible baselines for text/components keep focus transitions smooth
- TV design prioritizes clarity over space efficiency

## Related
- `design-10foot.md` — 10-foot experience principles
- `design-typography.md` — Text sizing and readability
- `perf-lists.md` — List virtualization for performance
