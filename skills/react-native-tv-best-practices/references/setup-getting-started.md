# Getting Started with React Native for TV

React Native for TV is powered by `react-native-tvos`, an independent fork dedicated to TV support. It maintains close parity with React Native core while adding TV-specific APIs.

## Key Takeaways
- Use `react-native-tvos` as a drop-in replacement for `react-native`
- Expo SDK 50+ fully supports TV via `@react-native-tvos/config-tv` plugin
- The fork does NOT prevent building regular iOS/Android mobile apps
- TV support adds focus handling, remote input, and TV-optimized components

## Without Expo (React Native CLI)

### New Project
```bash
npx @react-native-community/cli@latest init TVTest \
  --template @react-native-tvos/template-tv
```

### Existing Project
Replace `react-native` in `package.json`:
```json
"react-native": "npm:react-native-tvos@latest"
```

#### Android TV Setup
Add to `AndroidManifest.xml`:
```xml
<intent-filter>
  <category android:name="android.intent.category.LEANBACK_LAUNCHER" />
</intent-filter>

<uses-feature android:name="android.hardware.touchscreen" android:required="false" />
<uses-feature android:name="android.hardware.faketouch" android:required="false" />
<uses-feature android:name="android.software.leanback" android:required="true" />
```

> Add these to TV-specific manifest only. Mobile builds still need touchscreen.

#### Apple TV Setup
- Update `project.pbxproj` to include tvOS platform
- In Podfile: `platform :tvos, min_ios_version_supported`

## With Expo

### New Project
```bash
npx create-expo-app MyTVProject -- -e with-tv
# Or with navigation:
npx create-expo-app MyTVProject -- -e with-router-tv
```

### Existing Expo Project
1. Replace react-native:
   ```json
   "react-native": "npm:react-native-tvos"
   ```
2. Exclude from version validation:
   ```json
   "expo": { "install": { "exclude": ["react-native"] } }
   ```
3. Install TV plugin:
   ```bash
   npx expo install @react-native-tvos/config-tv --dev
   ```
4. Add to `app.json`:
   ```json
   { "plugins": ["@react-native-tvos/config-tv"] }
   ```
5. Build:
   ```bash
   export EXPO_TV=1
   npx expo prebuild --clean
   ```

## Environment Setup

Same as React Native mobile, plus:
- **Android:** Download TV system image in SDK Manager, create Android TV emulator
- **Apple TV:** Install tvOS SDK via `xcodebuild --downloadAllPlatforms`

## Key API Differences from Core React Native

| Component / API | TV Changes |
|----------------|------------|
| `Platform` | Added `Platform.isTV` and `Platform.isTVOS` |
| `Pressable`, `Touchable*` | Native `onFocus` & `onBlur` events + remote mapping |
| `Pressable` | `.focus:` and `.active:` Tailwind pseudo classes |
| `TVEventHandler` / `useTVEventHandler` | Custom remote event handling |
| `TVFocusGuideView` | Focus management between disconnected areas |
| `View` | Added `nextFocus*` props for iOS (previously Android-only) |
| `VirtualizedList` | Extended for focus management |
| `BackHandler` | Extended for Apple & Android TV back button |
| `TVTextScrollView` (Apple TV) | Scrolling via swipe gestures from remote |
| `TVEventControl` (Apple TV) | Enable/disable Siri remote features |

## Community Resources
- **Ignite TV** — Boilerplate from Infinite Red for TV apps
- **Amazon Sample Apps** — Multi-platform TV best practices
- **Hoppix** — Demo showing spatial navigation on TV
- **@bamlab/react-tv-space-navigation** — Spatial navigation across platforms

## Related
- `setup-architecture.md` — Project structure and code sharing
- `setup-cross-platform.md` — Handling platform differences
