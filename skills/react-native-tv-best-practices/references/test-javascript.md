---
title: JavaScript Tests for TV Apps
impact: MEDIUM
tags: testing, rntl, tvremote, focus, hardware-key-events, tv
---

# JavaScript Tests for TV Apps

TV tests use the same React Native Testing Library but need custom helpers for remote-controlled navigation — you can't emulate D-pad with click events.

## Quick Reference
- Create a `tvRemote` helper to emit platform-specific hardware key events
- Focus movement must be explicitly specified (focus engine is native, not testable in JS)
- Platform differences: tvOS emits different event sequences than Android TV
- No reusable library exists yet — build helpers for your use case

## Example Test

`tvRemote` is a project-specific helper (built in the next section), not a library export.

```jsx
import { render, screen, fireEvent } from '@testing-library/react-native';
import { tvRemote } from './testUtils/tvRemote';

it('navigates and selects the play button', () => {
  const onPressMock = jest.fn();
  render(<MyComponent onPress={onPressMock} />);

  const infoButton = screen.getByRole('button', { name: 'Info' });
  const playButton = screen.getByRole('button', { name: 'Play' });

  fireEvent(infoButton, 'onFocus');
  tvRemote.right({ elementToFocus: playButton, elementToBlur: infoButton });
  tvRemote.select({ elementToSelect: playButton });

  expect(onPressMock).toHaveBeenCalled();
});
```

## Building the tvRemote Helper

The snippets below are an illustrative scaffold for your own test utility. The `prepare*Event`/`emit*Event` functions are project-specific — define them to match the event shape your native focus layer dispatches. `PlatformSwitch` is a small local helper (shown below), not a React Native export.

```jsx
import { act } from '@testing-library/react-native';
import { DeviceEventEmitter, Platform } from 'react-native';

// Local helper: react-native-tvos reports Platform.OS === 'ios' on tvOS,
// so branch on Platform.isTVOS to pick a tvOS vs. Android TV event sequence.
const PlatformSwitch = {
  select: (spec) => (Platform.isTVOS ? spec.tvos : spec.default),
};
```

### Hardware Key Event Emitter
```jsx
function emitHWKeyEvent(hwEventPayload) {
  act(() => {
    DeviceEventEmitter.emit('onHWKeyEvent', hwEventPayload);
  });
}
```

### Platform-Specific Event Creation
```jsx
const createEvent = ({ tag, action, type }) => {
  return PlatformSwitch.select({
    tvos: { eventType: type, body: {} },
    default: { eventType: type, eventKeyAction: action, tag, target: tag },
  });
};
```

### Directional Press Helpers
```jsx
function emitDirectionalSinglePressEvent(keyCode, params) {
  const emitPressDown = preparePressDownEvent({ keyCode });
  const emitPressUp = preparePressUpEvent({ keyCode });

  PlatformSwitch.select({
    tvos: [emitFocusEvent, emitBlurEvent, emitPressUp],
    default: [emitPressDown, emitBlurEvent, emitFocusEvent, emitPressUp],
  }).forEach((emitEvent) => act(() => emitEvent(params)));
}
```

> Note: tvOS emits a different sequence of events compared to Android TV for the same input.

### The tvRemote API
```jsx
const tvRemote = {
  right: ({ elementToFocus, elementToBlur, tag } = {}) => {
    emitDirectionalSinglePressEvent('right', {
      elementToFocus, elementToBlur, tag,
    });
  },
  select: ({ elementToSelect } = {}) => {
    const emitPressDown = preparePressDownEvent({ keyCode: 'select' });
    const emitPressUp = preparePressUpEvent({ keyCode: 'select' });
    if (elementToSelect && Platform.isTVOS) {
      fireEvent(elementToSelect, 'pressIn');
      fireEvent.press(elementToSelect);
    }
    PlatformSwitch.select({
      tvos: [emitPressUp],
      default: [emitPressDown, emitPressUp],
    }).forEach((emitEvent) => act(() => emitEvent()));
    if (elementToSelect && !Platform.isTVOS) {
      fireEvent(elementToSelect, 'pressIn');
      fireEvent.press(elementToSelect);
    }
  },
};
```

## Why Focus Must Be Explicit

The native focus engine (tvOS/Android TV) handles actual focus movement. In JavaScript tests, there's no way to trigger real focus navigation — you must specify `elementToFocus` and `elementToBlur` manually.

## Performance Testing with Reassure

Reuse integration test scenarios to measure render characteristics:
```jsx
// Same RNTL tests, but Reassure measures render times
// Compare results against a stable baseline
```

## Related Skills
- [test-strategy.md](./test-strategy.md) — Overall testing approach
- [test-e2e.md](./test-e2e.md) — End-to-end testing with Appium
- [focus-management.md](./focus-management.md) — Focus APIs being tested
