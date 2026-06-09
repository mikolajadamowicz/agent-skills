# Handling Cross-Platform Inconsistencies

When building for both mobile and TV, small platform differences add up. Centralize platform-specific logic and leverage libraries with built-in platform support.

## Key Takeaways
- Use `Platform.isTV` for conditional TV logic
- Use platform-specific file extensions for drastically different UI
- Abstract platform-specific styles with `Platform.select()`
- Many libraries (react-navigation, react-native-gesture-handler) handle platform quirks internally

## Platform Detection

```jsx
import { Platform } from 'react-native';
if (Platform.isTV) {
  // TV-specific logic
}
```

Centralize platform checks in utility functions rather than scattering them throughout components.

## Platform-Specific Files

```
MyComponent.tvos.js
MyComponent.ios.js
MyComponent.android.js
```

React Native automatically selects the correct file for the running platform.

## Platform-Specific Styles

```jsx
const styles = StyleSheet.create({
  container: {
    ...Platform.select({
      fireTV: { shadowColor: '#000', shadowOpacity: 0.2 },
      tvOS: { elevation: 4 },
    }),
  },
});
```

## Third-Party Libraries

Check if a library already addresses your cross-platform needs before building custom solutions:
- **react-navigation** — Handles navigation patterns across platforms
- **react-native-gesture-handler** — Platform-aware gesture handling
- **@bamlab/react-tv-space-navigation** — Spatial navigation across TV platforms
- **@noriginmedia/norigin-spatial-navigation** — For web-based TV platforms

## Avoid nextFocus* for Cross-Platform

`nextFocusUp`, `nextFocusDown`, etc. only work on Android TV — ignored on tvOS. For shared codebases, use `TVFocusGuideView` and inferred focus behavior instead.

## react-native-tvos Compatibility

The `react-native-tvos` fork does not prevent mobile builds. It extends core with TV-specific features while maintaining API compatibility. Mobile app logic stays intact.

## Related
- `setup-getting-started.md` — Project setup
- `setup-architecture.md` — Code sharing strategies
- `focus-management.md` — Cross-platform focus handling
