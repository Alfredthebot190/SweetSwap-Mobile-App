# Phase 4: Swipe Interface - Implementation Plans

## Overview
Build Tinder-style swipeable card stack with gestures, animations, and haptic feedback.

---

## Plan 1: SwipeCard Component with Gesture Handling

<task type="auto">
  <name>Create SwipeCard component with pan gesture</name>
  <files>
    src/components/swipe/SwipeCard.tsx
    src/components/swipe/types.ts
  </files>
  <action>
    Create SwipeCard component with:
    1. Gesture handling using react-native-gesture-handler:
       - Pan gesture for horizontal swipes
       - Gesture state tracking (active, end, fail)
       - Translation on drag (follow finger)

    2. Animation with react-native-reanimated:
       - Shared value for translation X
       - Rotate card based on translation
       - Scale effect on drag start
       - Spring physics on release

    3. Visual feedback:
       - Like overlay (checkmark, green) on right drag
       - Skip overlay (X, red) on left drag
       - Overlay opacity scales with drag distance

    4. Props:
       - recipe: Recipe object
       - onSwipeLeft: () => void
       - onSwipeRight: () => void
       - onCardPress: () => void (tap to view recipe)

    5. Card content:
       - Recipe title
       - Recipe description (truncated)
       - Placeholder image (category-based)
       - Diet badge

    Style decisions:
    - Card dimensions: 85-90% screen width, 70% screen height
    - Border radius: 20-24px
    - Shadow: elevation 8-12
    - Rotation: ±15deg max on drag
  </action>
  <verify>
    - SwipeCard renders recipe card with title, description, image placeholder
    - Pan gesture works: card follows finger on drag
    - Card rotates slightly on drag
    - Like overlay (green check) appears on right drag
    - Skip overlay (red X) appears on left drag
    - Releasing card springs back to center if not swiped far enough
    - onSwipeRight fires when swiping right past threshold
    - onSwipeLeft fires when swiping left past threshold
  </verify>
  <done>
    SwipeCard component with gesture handling complete
  </done>
</task>

---

## Plan 2: CardStack with Queue Management

<task type="auto">
  <name>Create CardStack component with queue management</name>
  <files>
    src/components/swipe/CardStack.tsx
    src/stores/cardQueueStore.ts
  </files>
  <action>
    Create CardStack with queue management:
    1. Zustand store (cardQueueStore):
       - State: recipes queue, current index, history
       - Actions: addRecipes, swipeRight, swipeLeft, undoLast
       - Queue refill logic: trigger at 2 cards remaining

    2. CardStack component:
       - Renders stack of cards (top 3 visible)
       - Only top card receives gestures
       - Background cards stacked with offset
       - Smooth layout when card leaves stack

    3. Queue management:
       - Pre-fetch next batch before empty
       - Call batchGenerateRecipes on low queue
       - Show skeleton cards while loading
       - Handle loading errors gracefully

    4. Visual stack effect:
       - Card 2: 95% size, offset Y +10px
       - Card 3: 90% size, offset Y +20px
       - Opacity fade for cards 2 and 3

    5. Empty state:
       - "All caught up! Start a new craving"
       - CTA button to go back to craving screen

    Style decisions:
    - Stack spacing: 10px between cards
    - Empty state: centered, large emoji + text
  </action>
  <verify>
    - CardStack renders stack of 3 cards at start
    - Top card swipes away, next card becomes top
    - Stack maintains 3 cards visually
    - Queue refills automatically when 2 cards left
    - Skeleton cards show while loading new batch
    - Empty state displays when queue is empty
    - CTA button navigates back to craving screen
  </verify>
  <done>
    CardStack with queue management complete
  </done>
</task>

---

## Plan 3: Swipe Animations, Haptics & Undo

<task type="auto">
  <name>Add swipe animations, haptic feedback and undo</name>
  <files>
    src/components/swipe/SwipeCard.tsx (update)
    src/components/swipe/UndoButton.tsx
    src/lib/animations.ts
  </files>
  <action>
    Enhance swipe experience with:
    1. Swipe velocity detection:
       - Fast swipes (> 1000 px/s) fly off screen
       - Slow swipes spring back if not past threshold
       - Threshold: 100px from center

    2. Haptic feedback:
       - Light haptic on card pickup (gesture active)
       - Medium haptic on swipe completion
       - Success haptic on save (right swipe)

    3. Save animation (heart burst):
       - On right swipe, show heart animation
       - Heart scales up and fades out
       - Confetti-like particle burst effect
       - Animation lasts 500-800ms

    4. Undo button:
       - Floating button appears after left swipe
       - Shows last skipped recipe
       - One-time undo (button disappears after use)
       - Undo only for left swipes (not saved recipes)

    5. Animation utilities:
       - springConfigs for different animation types
       - useSharedValue hooks for smooth animations
       - runOnJS for gesture callbacks

    Style decisions:
    - Undo button: floating bottom-right, circular, icon-only
    - Heart animation: centered on screen, pink/red gradient
    - Confetti: small particles, random directions
  </action>
  <verify>
    - Fast swipes fly off screen smoothly
    - Slow swipes spring back if threshold not met
    - Haptic feedback triggers on appropriate events
    - Heart animation plays on save (right swipe)
    - Undo button appears after left swipe
    - Undo button restores last skipped recipe to stack
    - Undo button disappears after use
    - Undo button does NOT appear after right swipes (saved recipes)
  </verify>
  <done>
    Phase 4 complete: Polished swipe interface with animations
  </done>
</task>
