# Testing Strategy for React Native TV Apps

Testing TV apps requires a thoughtful approach that considers focus management, device fragmentation, remote control interaction, and performance constraints.

## Key Takeaways
- Use the **trophy approach** — emphasize integration tests over unit tests
- Integration tests provide best effect-per-effort ratio for TV apps
- Shift E2E tests into faster integration tests using RNTL where possible
- ~80% of test logic can run at integration layer before reaching a device
- Always test on real hardware — emulators can't replicate overscan, input latency, or color profiles

## Trophy Testing Approach (Kent C. Dodds)

1. **Static analysis** — TypeScript, ESLint covering type errors across project
2. **Unit tests** — Important but not primary focus
3. **Integration tests** — **Primary focus** — best balance of speed and coverage
4. **E2E tests** — Sparingly, on real devices/emulators

## Why Integration Tests for TV

1. **Closer to real environment** — Test behavior in context resembling actual TV
2. **Comprehensive coverage** — Multiple components and their interactions
3. **Balance of speed and impact** — Faster than E2E, more meaningful than unit
4. **Resilience** — Less affected by implementation detail changes

## Essential Testing Tools

| Tool | Purpose |
|------|---------|
| Jest / Vitest | Test runner |
| React Native Testing Library | UI/interaction testing |
| Reassure | Performance regression testing |
| Appium | E2E testing on TV platforms |
| WebdriverIO | Test runner for Appium |

## Integration Test Best Practices

### Use Real State
Don't mock state management. Capture real state from your running app and apply it:
```jsx
const snapshot = require('my-state.json');
const { Wrapper } = loadStateFromSnapshot(snapshot);
render(<Wrapper><VideoPlayer /></Wrapper>);
```

### ARRANGE / ACT / ASSERT Pattern
```jsx
describe('Video Player Controls', () => {
  it('shows controls when OK pressed', () => {
    // ARRANGE
    mockTVDeviceEnvironment();
    render(<Wrapper><VideoPlayer /></Wrapper>);
    // ACT
    fireEvent.tvRemote.press('PLAY');
    // ASSERT
    expect(screen.getByTestId('player-controls-modal')).toBeVisible();
  });
});
```

### Minimize Mocking
- Mock native modules when unavoidable
- Mock timers for time-dependent behavior (`jest.useFakeTimers()`)
- Never mock state management in integration tests

## TV-Specific Testing Considerations

| Test Type | TV Considerations |
|-----------|------------------|
| Unit | Test focus management helpers, mock TV APIs |
| Integration | Test focus movement, remote control inputs, player controls |
| E2E | Test on real devices/emulators, simulate remote interactions |
| Accessibility | Test with platform screen readers, verify labels/roles |
| Performance | Measure rendering times, compare against baseline |

## CI Integration

- **Pre-push hooks** — Run fast integration tests and static checks
- **CI pipeline** — Full suite on PR: unit + integration + performance + E2E
- **Selective execution** — Only run tests affected by changes
- **Parallelization** — Run tests concurrently across devices/environments

## Production Monitoring

Testing doesn't end at deploy:
- **Real User Monitoring (RUM)** — Sentry, New Relic, DataDog
- Monitor errors, crashes, performance across device diversity
- React to issues as they happen with detailed crash reports

## Related
- `test-javascript.md` — JS test setup and tvRemote helpers
- `test-e2e.md` — E2E testing with Appium and device farms
- `perf-overview.md` — Performance KPIs to test
