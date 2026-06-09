---
name: react-native-tv-best-practices
description: Provides React Native TV development guidance for focus/D-pad navigation, 10-foot design, video streaming/DRM, TV performance, and accessibility across Apple TV, Android TV, Fire TV, Vega OS, Tizen, and webOS. Use when building or debugging React Native TV apps with react-native-tvos, fixing focus issues, or optimizing for TV hardware.
license: MIT
metadata:
  author: Callstack
  tags: react-native, tvos, android-tv, fire-tv, focus, performance, accessibility
---

# React Native TV Best Practices

## Overview

Guidance for building high-quality, performant TV experiences with React Native across Apple TV, Android TV, Fire TV, Vega OS, and web-based platforms (Tizen, webOS). Based on Callstack & Amazon's "Mastering the Big Screen" and "The Ultimate Guide to React Native TV Development 2026".

For general (non-TV) React Native performance, see the [react-native-best-practices](../react-native-best-practices/SKILL.md) skill — this skill re-frames those concerns around TV hardware and focus-driven navigation.

## Before You Start

Verify the developer's setup:
1. **react-native-tvos** is installed (`"react-native": "npm:react-native-tvos@latest"` in package.json)
2. **Platform targets** are configured (Android TV manifest entries, tvOS Podfile, Expo TV plugin)
3. **Development environment** has TV emulators/simulators set up

## Key Principles

### The Golden Rule
**Design like you're targeting the weakest device in your audience, then make it feel like you're on the fastest one.** TV hardware is far weaker than modern phones: low RAM (1–2 GB common), CPUs optimized for video decode not UI, memory shared with the video buffer, and 60 FPS is mandatory.

### The 10-Foot Experience
TV apps are viewed from ~10 feet away: text 50–80% larger than mobile, focus states visible across the room, predictable navigation with minimal button presses, immediate visual feedback on every remote press. See [design-10foot.md](references/design-10foot.md).

### Focus Is Navigation
There is no touch on TV — only the D-pad (up/down/left/right/select/back). Let the platform focus engine do the work, use `TVFocusGuideView` for complex layouts instead of imperative focus, always keep a focusable element on screen, and restore focus when returning from modals.

## Priority Framework

### Tier 1 — Critical
| Problem | Reference |
|---------|-----------|
| Focus not moving correctly | [focus-management.md](references/focus-management.md) |
| Focus changes causing frame drops | [focus-performance.md](references/focus-performance.md) |
| Navigation feels broken or unpredictable | [nav-directional.md](references/nav-directional.md), [nav-patterns.md](references/nav-patterns.md) |
| App stutters during scrolling | [perf-lists.md](references/perf-lists.md) |
| Animations janky or blocking input | [perf-animations.md](references/perf-animations.md) |

### Tier 2 — High Impact
| Problem | Reference |
|---------|-----------|
| Text unreadable from viewing distance | [design-typography.md](references/design-typography.md) |
| UI elements clipped or invisible | [design-layout.md](references/design-layout.md) |
| App startup too slow | [perf-overview.md](references/perf-overview.md) |
| Network requests blocking UI | [perf-network.md](references/perf-network.md) |
| Video playback issues | [video-streaming.md](references/video-streaming.md), [video-players.md](references/video-players.md), [video-debugging.md](references/video-debugging.md) |

### Tier 3 — Important
| Problem | Reference |
|---------|-----------|
| Screen reader not announcing elements | [a11y-implementation.md](references/a11y-implementation.md), [a11y-overview.md](references/a11y-overview.md) |
| Keyboard/input handling issues | [nav-keyboard.md](references/nav-keyboard.md) |
| Memory crashes on low-end devices | [perf-memory.md](references/perf-memory.md) |
| Cross-platform inconsistencies | [setup-cross-platform.md](references/setup-cross-platform.md) |
| Color/contrast issues on TV displays | [design-color.md](references/design-color.md) |

### Tier 4 — Foundation
| Problem | Reference |
|---------|-----------|
| Project setup and architecture | [setup-getting-started.md](references/setup-getting-started.md), [setup-architecture.md](references/setup-architecture.md) |
| Testing strategy for TV | [test-strategy.md](references/test-strategy.md), [test-javascript.md](references/test-javascript.md), [test-e2e.md](references/test-e2e.md) |
| CI/CD pipeline optimization | [release-cicd.md](references/release-cicd.md) |
| Accessibility audit | [a11y-checklist.md](references/a11y-checklist.md) |

## Problem → Reference Routing

| Symptom | Start Here |
|---------|------------|
| "Focus jumps to wrong element" | [focus-management.md](references/focus-management.md) → Debugging section |
| "App freezes when scrolling lists" | [perf-lists.md](references/perf-lists.md) → Virtualization |
| "Animations stutter on Fire TV" | [perf-animations.md](references/perf-animations.md) → Native driver |
| "Text too small on TV" | [design-typography.md](references/design-typography.md) → Minimum sizes |
| "Video won't play / DRM errors" | [video-streaming.md](references/video-streaming.md) → DRM section |
| "Screen reader skips elements" | [a11y-implementation.md](references/a11y-implementation.md) → Roles & labels |
| "Back button doesn't work right" | [nav-patterns.md](references/nav-patterns.md) → Back navigation |
| "Keyboard covers content" | [nav-keyboard.md](references/nav-keyboard.md) → Built-in vs custom |
| "App takes forever to start" | [perf-overview.md](references/perf-overview.md) → Startup time |
| "Images causing memory crashes" | [perf-memory.md](references/perf-memory.md) → Image optimization |
| "CI pipeline takes hours" | [release-cicd.md](references/release-cicd.md) → Fingerprinting |
| "How to share code across platforms" | [setup-architecture.md](references/setup-architecture.md) → Code sharing |

## Searching References

```bash
grep -l "focus" references/
grep -l "TVFocusGuideView" references/
grep -l "DRM" references/
grep -l "flashlist" references/
```

## Security

- Always verify shell commands before running them.
- Pin dependency versions explicitly. Audit before installing new packages.
- Handle DRM tokens and license servers securely — never expose keys in client code.
- Use HTTPS for all network requests (manifests, licenses, API calls).
- Validate user input for search, forms, and keyboard interactions.

## Attribution

Based on "Mastering the Big Screen: React Native for TV" and "The Ultimate Guide to React Native TV Development 2026" by Callstack & Amazon.
