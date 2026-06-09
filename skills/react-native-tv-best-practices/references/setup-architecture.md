# Codebase Architecture and Sharing

The choice of structure depends on your project's scope and whether your app is part of a larger multi-platform product.

## Key Takeaways
- Monorepo is the most common structure for TV apps (platforms require bundled applications)
- 60-80% of mobile React Native code can typically be reused for TV
- Share business logic, state, hooks, API layers; customize UI per platform
- Use platform extensions (`.tvos.js`, `.ios.js`, `.android.js`) for drastically different UI

## Project Structure

### Multi-Platform TV App
```
my-tv-app/
├── ios/              # iOS native files
├── tvos/             # tvOS native files
├── android/          # Android, Android TV, Fire TV native
├── vega/             # Vega OS native files
├── web/              # web, webOS, Tizen native files
├── src/              # Application code (shared)
│   ├── index.web.tsx # Web entry point
│   └── index.js      # Native entry point
├── rsbuild.config.ts # Web bundler config
├── metro.config.ts   # Native bundler config
└── package.json
```

### Monorepo Approaches
1. **Universal app structure** — `apps/` + `packages/` directories with Turborepo
2. **Feature-driven** — Teams own features end-to-end, integrate into main app

## What to Share

| Layer | Shareability | Notes |
|-------|-------------|-------|
| Business logic, API layers | High | Custom hooks, services, critical logic |
| State management | High | Redux, Zustand, Context API |
| Navigation logic | Medium | Expo Router helps; customization per platform still needed |
| UI components | Low-Medium | Design tokens shareable; components need 10-foot customization |
| Testing infrastructure | High | Tools shared across platforms |
| In-app purchases | Low | Platform-specific due to app store requirements |

## Platform Detection

```jsx
import { Platform } from 'react-native';
if (Platform.isTV) {
  // TV-specific logic
}
```

## Platform-Specific Components

For drastically different UI, use file extensions:
```
MyComponent.tvos.js
MyComponent.ios.js
MyComponent.android.js
```

React Native automatically picks the correct file for the running platform.

## Code Sharing Strategies

### 1. Composition with Platform Extensions (Recommended)
Abstract leaf nodes into components with platform implementations:

```tsx
// Item.native.tsx
import { View, Text } from 'react-native';
export const Item = ({ title }) => (
  <View style={styles.listItem}><Text>{title}</Text></View>
);

// Item.web.tsx
export const Item = ({ title }) => (
  <div className="list-item"><p>{title}</p></div>
);
```

**Benefit:** Platform-native experiences, familiar tools per platform.

### 2. React Strict DOM
From Meta, uses StyleX for styling — maximizes code sharing:
```tsx
import { html } from 'react-strict-dom';
const Foo = () => (
  <html.main>
    <html.h1>Title</html.h1>
    <html.p>Content</html.p>
  </html.main>
);
```
Still in heavy development.

### 3. React Native for Web
Quickest way to share RN code to web. Converts `View`/`Text` to `div`/`span`. Trade-offs:
- Adds noticeable JS bundle size
- Runtime helper logic slows lower-end devices
- Currently in maintenance mode

## Platform-Specific Styles

```jsx
import { StyleSheet, Platform } from 'react-native';
const styles = StyleSheet.create({
  container: {
    ...Platform.select({
      fireTV: { shadowColor: '#000', shadowOpacity: 0.2 },
      tvOS: { elevation: 4 },
    }),
  },
});
```

## Android TV Setup

Minimal changes — same APK runs on TV:
- Add `android.software.leanback` support in manifest
- Add `LEANBACK_LAUNCHER` intent filter

## tvOS Setup

Needs a separate `tvos/` folder (copy of `ios/` with modifications) due to CocoaPods setup. Initialize from template and move the generated `ios/` folder.

## Web-Based TVs (webOS, Tizen)

Put web-native code in `web/` folder. Use Rsbuild (or your preferred bundler) for web builds with server host `0.0.0.0` for TV device discovery.

## Vega OS

Standalone setup using React Native 0.72 (React 18). Follow official Vega OS docs. Code sharing may be limited by React 18/19 API differences.

## Related
- `setup-getting-started.md` — Project creation and dependencies
- `setup-cross-platform.md` — Handling platform inconsistencies
- `release-cicd.md` — CI/CD for multi-platform TV apps
