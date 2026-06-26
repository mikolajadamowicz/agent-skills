---
title: CI/CD for TV Apps
impact: MEDIUM
tags: cicd, build-fingerprinting, diff-triggers, app-store, multi-platform, tv
---

# CI/CD for TV Apps

In TV development, a simple "build and test" pipeline explodes in complexity. Every step multiplies by the number of platforms and targets you support.

## Quick Reference
- TV CI/CD = mobile pipeline × 6+ platforms × multiple device SKUs
- **Fail fast, fail cheap** — shift E2E tests to faster integration tests
- **Build fingerprinting** — skip native builds when only JS changed (saves 50%+ pipeline time)
- **Diff-based triggers** — only build platforms affected by changes
- Standard CI caching (node_modules, Gradle, CocoaPods) applies as usual — it just pays off more here because every cache hit is multiplied across N platform targets

## The Multiplication Problem

Mobile: install → validate → build (iOS, Android) → bundle → E2E = ~45 min

TV: Same steps × tvOS, Android TV, Fire TV, webOS, Tizen, Vega OS = multi-hour monster. Each native build takes 30+ min, E2E tests 20 min per platform.

## Fail Fast, Fail Cheap

Shift E2E tests into faster integration tests using RNTL:
- Abstract platform-specific quirks (D-pad keycodes)
- ~80% of test logic runs at integration layer
- Single set of integration tests runs identically across platforms
- Dramatically faster feedback than device-based tests

## Build Fingerprinting

Most PRs don't modify native code — only JS. Fingerprinting generates a hash of everything that influences the native build:

```bash
npx expo fingerprint:generate --platform ios,android,tvos

# Compare in CI:
if [ "$(cat .last_fingerprint)" = "$(cat .current_fingerprint)" ]; then
  echo "No native changes — skipping rebuild."
else
  echo "Native changes detected — rebuilding..."
fi
```

**Sources included:** `ios/Podfile`, `android/build.gradle`, `app.json`, `package.json`

Typically reduces pipeline duration by **50% or more**.

## Diff-Based Triggers

Only build what actually changed:

```yaml
# GitHub Actions example
name: TV CI
on:
  pull_request:
    paths:
      - 'packages/common/**'
      - 'apps/tvos/**'
      - 'apps/androidtv/**'
```

Map directories to platforms → trigger only affected builds.

## Performance in CI

Embed performance markers in your app:
```jsx
import { performance } from 'react-native-performance';
performance.mark('app_start');
AppRegistry.registerComponent(appName, () => {
  performance.mark('app_registered');
  return App;
});
```

Collect via automated tests on:
- AWS Device Farm for Fire TV / Android TV
- Local device rack for tvOS and Tizen
- Push metrics to Grafana/Datadog
- Fail CI if cold start exceeds KPI targets by even 10%

## App Store Requirements

Different platforms have different review processes:
- **Amazon Fire TV** — Amazon Appstore submission
- **Android TV** — Google Play Store with TV-specific requirements
- **Apple TV** — App Store review (tvOS-specific guidelines)
- **webOS / Tizen** — Platform-specific submission portals

## Related Skills
- [test-strategy.md](./test-strategy.md) — Testing approach and tools
- [test-e2e.md](./test-e2e.md) — E2E testing and device farms
- [setup-architecture.md](./setup-architecture.md) — Multi-platform project structure
