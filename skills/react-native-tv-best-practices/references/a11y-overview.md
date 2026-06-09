# Accessibility on TV — Overview

Accessibility means making digital content available and usable by everyone. On TV, this is essential — screens are shared spaces used by people of all ages, abilities, and contexts.

## Key Takeaways
- Accessibility is a legal requirement in most countries (ADA, Section 508, EN 301 549)
- TV accessibility differs from mobile/web: D-pad navigation, focus-driven UI, viewing distance
- Screen readers: TalkBack (Android TV), VoiceOver (Apple TV), VoiceView (Fire TV)
- Behavior is NOT consistent across platforms — test on each

## Why TV Accessibility Is Unique

| Feature | TV | Mobile | Web |
|---------|-----|--------|-----|
| Input | Remote / voice | Touch + assistive tech | Keyboard, mouse, screen readers |
| Focus Management | Essential (D-pad) | Implicit with touch | Tab focus |
| Screen Reader | Limited/inconsistent | VoiceOver, TalkBack | JAWS, NVDA, VoiceOver |
| Text Scaling | Limited; fixed layout | Dynamic type | CSS zoom |
| Contrast | Critical (viewing distance) | Important | Important |
| Gestures | Not applicable | Swipe, pinch | Keyboard, mouse |

## WCAG Standards (POUR Principles)

- **Perceivable** — Content via text, audio, or sensory alternatives
- **Operable** — UI usable via keyboard or remote
- **Understandable** — Content clear and predictable
- **Robust** — Works with assistive tools

## Platform Screen Readers

| Platform | Tool | Activation |
|----------|------|------------|
| Fire TV | VoiceView | Accessibility settings |
| Apple TV | VoiceOver | Menu + Siri Remote, or Settings |
| Android TV | TalkBack | Accessibility shortcut or dev settings |

### Platform Differences

- **Fire TV (VoiceView):** Similar to TalkBack but not all props honored on older models. Extra verbose by default.
- **Apple TV (VoiceOver):** Focus can jump non-linearly. `accessibilityHint` read immediately after label. Role mapping consistent with iOS.
- **Android TV (TalkBack):** Linear D-pad navigation. `accessibilityHint` sometimes skipped unless `accessible={true}` is explicit.

## React Native Accessibility Props

| Prop | Purpose |
|------|---------|
| `accessible={true}` | Makes element recognizable by screen readers |
| `accessibilityLabel` | Spoken name/title |
| `accessibilityHint` | What happens on interaction |
| `accessibilityRole` | What the element is (button, header, image, etc.) |
| `accessibilityState` | Current state (busy, selected, expanded, disabled, checked) |
| `accessibilityLiveRegion` | Announce dynamic content changes ("polite", "assertive") |
| `accessibilityViewIsModal` | Trap screen reader focus in modal |

## Related
- `a11y-implementation.md` — Detailed implementation guide
- `a11y-checklist.md` — Pre-launch checklist
- `design-color.md` — Contrast requirements
