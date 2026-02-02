# Phase 9: Polish & QA - Implementation Plans

## Overview
Performance optimization, error handling, final UI polish, and cross-platform testing.

---

## Plan 1: Error Boundaries & Offline Detection

<task type="auto">
  <name>Implement error boundaries and offline detection</name>
  <files>
    src/components/common/ErrorBoundary.tsx
    src/components/common/OfflineBanner.tsx
    src/lib/networkStatus.ts
    src/app/_layout.tsx (update)
  </files>
  <action>
    Implement error handling and offline detection:
    1. Error Boundary component:
       - Catch React errors in child components
       - Show friendly error message
       - "Try Again" button to reload
       - "Report Issue" button (send error logs)
       - Fallback UI for critical errors
       - Log errors to error tracking service

    2. Network status utility:
       - Use NetInfo for network detection
       - Listen for online/offline events
       - Expose isOnline state via React context
       - Debounce rapid connection changes
       - Cache offline state for restart persistence

    3. Offline banner:
       - Show when network is offline
       - Fixed at top or bottom of screen
       - "You're offline" message
       - Auto-dismiss when back online
       - Animated slide in/out
       - Subtle styling (yellow/amber)

    4. Graceful degradation:
       - Show cached content when offline
       - Disable actions that require network
       - Queue mutations to sync when online
       - Show "Retry" for failed requests
       - Persist queue to AsyncStorage

    5. Error boundary placement:
       - Wrap app root (app/_layout.tsx)
       - Wrap AI components (recipe generation)
       - Wrap Convex components (data fetching)
       - Wrap critical screens

    Style decisions:
    - Error screen: centered, large icon, clear text
    - Offline banner: amber background, white text
    - Retry button: prominent, brand color
  </action>
  <verify>
    - Error boundary catches React errors and shows fallback
    - Error boundary loggs errors to tracking service
    - "Try Again" button reloads failed component
    - Network status detects online/offline correctly
    - Context provides isOnline to app
    - Offline banner appears when disconnected
    - Offline banner dismisses when reconnected
    - Cached content shows when offline
    - Queued mutations sync when back online
    - Network changes trigger debounced updates
  </verify>
  <done>
    Error boundaries and offline detection complete
  </done>
</task>

---

## Plan 2: Performance Optimization & Profiling

<task type="auto">
  <name>Optimize performance and add profiling</name>
  <files>
    src/lib/performance.ts
    src/app/_layout.tsx (update)
    src/components/swipe/SwipeCard.tsx (update)
    src/components/swipe/CardStack.tsx (update)
  </files>
  <action>
    Optimize app performance:
    1. Performance monitoring:
       - Add Reactotron or Flipper for profiling
       - Track frame rate (should be 60fps)
       - Track time to first render
       - Track time to first card
       - Track memory usage over time
       - Log slow renders (> 16ms)

    2. Swipe optimization:
       - Use React Native's useAnimatedStyle
       - Optimize gesture handler callbacks
       - Reduce layout thrashing in CardStack
       - Off-screen card rendering optimization
       - Use react-native-fast-image for cards

    3. List optimization:
       - Virtualization with FlatList
       - Optimized getItemLayout
       - Key extraction for proper diffing
       - Window size for off-screen item rendering
       - Lazy loading for images (Phase 7)

    4. Memory management:
       - Monitor memory usage over 100+ swipes
       - Clear image cache when memory threshold reached
       - Dispose unused gesture handlers
       - Cancel pending Convex queries on unmount
       - Clean up listeners and timers

    5. AI generation optimization:
       - Stream responses if possible
       - Show skeleton cards immediately
       - Batch recipe insertion into Convex
       - Debounce rapid craving changes

    6. Target metrics:
       - Time to first render: < 1s
       - Time to first card: < 2s
       - Swipe animation: 60fps
       - Recipe generation: < 3s
       - Memory leak: < 10MB over 100 swipes

    Style decisions:
    - Performance overlay: dev mode only
    - Slow render warnings: console logs
  </action>
  <verify>
    - Performance monitoring tracks frame rate (60fps target)
    - Time to first render is < 1s
    - Time to first card is < 2s
    - Swipe animations run at 60fps
    - Recipe generation completes < 3s
    - Memory usage stable over 100+ swipes
    - Image cache clears when threshold reached
    - Unused resources disposed on unmount
    - Pending queries cancelled properly
    - Performance overlay shows in dev mode
  </verify>
  <done>
    Performance optimized and monitored
  </done>
</task>

---

## Plan 3: Final Polish & Accessibility

<task type="auto">
  <name>Add final UI polish and accessibility improvements</name>
  <files>
    src/constants/theme.ts (update)
    src/app/_layout.tsx (update)
    src/components/common/Button.tsx (create)
    src/components/common/Text.tsx (create)
  </files>
  <action>
    Final polish pass and accessibility:
    1. Typography system:
       - Create reusable Text component
       - Define font scales (h1-h6, body, caption)
       - Implement readable() for large text preference
       - Use system font stack
       - Ensure text contrast ratios (WCAG AA)

    2. Spacing system:
       - Define spacing scale (4, 8, 12, 16, 20, 24, 32, 40)
       - Consistent padding across components
       - Proper whitespace between elements
       - Responsive spacing (larger on tablets)

    3. Color system:
       - Define primary, secondary, semantic colors
       - Light and dark mode variants
       - Ensure 4.5:1 contrast for normal text
       - Ensure 3:1 contrast for large text
       - Add focus states for all interactive elements

    4. Accessibility:
       - All images have alt text or decorative
       - All buttons have accessibilityLabel
       - Screen reader announcements for actions
       - Support for screen magnification
       - Support for reduced motion preference
       - Minimum touch target size: 44x44px
       - Proper heading hierarchy (h1-h6)
       - Focus management for modals

    5. Animations:
       - Respect prefers-reduced-motion
       - Disable animations when requested
       - Smooth transitions (ease-out, not bounce)
       - Consistent duration (200-400ms)
       - No jarring or dizzying effects

    6. Platform-specific polish:
       - iOS: proper status bar handling
       - Android: proper back button handling
       - Safe area insets handling
       - Keyboard avoidance for text inputs
       - Proper scroll indicators

    7. Review checklist:
       - Check all screens for spacing consistency
       - Verify color contrast throughout
       - Test with screen reader (VoiceOver/TalkBack)
       - Test with large text accessibility setting
       - Test with reduced motion accessibility setting
       - Test on both iOS and Android devices
       - Test on different screen sizes

    Style decisions:
    - Font scale: 12-48px range
    - Spacing scale: 4-40px range
    - Focus ring: 2-4px, brand color
  </action>
  <verify>
    - Typography scale is defined and used consistently
    - Spacing scale is defined and used consistently
    - All text meets WCAG AA contrast requirements
    - All interactive elements have accessibilityLabel
    - Screen reader announces actions correctly
    - Touch targets are >= 44x44px
    - Heading hierarchy is proper (h1 → h6)
    - Animations respect reduced motion preference
    - Focus states are visible
    - Safe area insets work correctly
    - Keyboard avoids text inputs
    - App works on iOS device
    - App works on Android device
    - App works on tablet (responsive)
    - No console errors or warnings
  </verify>
  <done>
    Phase 9 complete: App is polished, accessible, and performant
  </done>
</task>
