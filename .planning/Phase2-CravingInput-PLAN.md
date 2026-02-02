# Phase 2: Craving Input - Implementation Plans

## Overview
Create the craving input screen with 7 categories, icons, and free text input.

---

## Plan 1: Category Grid UI Component

<task type="auto">
  <name>Create craving category grid UI component</name>
  <files>src/components/craving/CravingGrid.tsx</files>
  <action>
    Create CravingGrid component with:
    - 7 category cards (Chocolate, Ice Cream, Cookies, Cake, Candy, Creamy, Fruity)
    - 2-column grid layout (responsive to screen size)
    - Category icons using lucide-react or custom SVG icons
    - Category-specific colors/gradients
    - Tap handling with ripple feedback
    - Smooth scale animation on press
    - Navigate to swipe screen on category tap

    Use Tamagui (YStack, XStack, Card, Text) for layout.
    Use expo-router's router.push for navigation.

    Style decisions:
    - Card aspect ratio: ~1:1 (square-ish)
    - Icon size: 40-48px
    - Font size: 16-18px category names
    - Colors: Pastel gradients matching category vibe
  </action>
  <verify>
    - CravingGrid renders 7 category cards
    - Grid layout shows 2 columns on mobile, 3-4 on tablet
    - Each card shows icon + category name
    - Tapping a card navigates to /swipe route
  </verify>
  <done>
    Category grid component ready for integration
  </done>
</task>

---

## Plan 2: Free Text Input Component

<task type="auto">
  <name>Create free text craving input component</name>
  <files>src/components/craving/FreeTextInput.tsx</files>
  <action>
    Create FreeTextInput component with:
    - Text input field with placeholder "What are you craving?"
    - Submit button (icon + text)
    - Input validation (min 3 characters)
    - Character count indicator
    - Clear button (X icon) to reset input
    - Focus state styling (border color change)
    - Submit on keyboard "Done" press

    Use Tamagui (Input, Button, Text) for UI.
    Store input value in local state.
    Pass onSubmit callback prop.

    Style decisions:
    - Input height: 48-56px
    - Rounded corners: 12-16px
    - Primary color: blue-500 (or brand color)
    - Placeholder color: gray-400
  </action>
  <verify>
    - FreeTextInput renders text field + submit button
    - Placeholder text displays correctly
    - Submit button is disabled when input < 3 characters
    - Submit button is enabled when input >= 3 characters
    - onSubmit callback fires with input value on submit
  </verify>
  <done>
    Free text input component ready for integration
  </done>
</task>

---

## Plan 3: Craving Screen Integration & Routing

<task type="auto">
  <name>Create craving screen and integrate components</name>
  <files>src/app/(tabs)/craving/index.tsx</files>
  <action>
    Create craving screen with:
    - Page header with title "What are you craving?"
    - Subtitle describing the experience
    - CravingGrid component (from Plan 1)
    - FreeTextInput component (from Plan 2)
    - Scrollable container for all content
    - Navigation to /swipe route with category or free text

    Use Expo Router for page structure.
    Pass craving info via router.push params:
    - { category: 'Chocolate' } for category taps
    - { freeText: 'something like Snickers' } for text input

    Style decisions:
    - Header: centered, bold, 24-28px
    - Subtitle: centered, gray, 14-16px
    - Padding: 20-24px horizontal
  </action>
  <verify>
    - Craving screen renders at /craving route
    - Header and subtitle display correctly
    - CravingGrid renders all 7 categories
    - FreeTextInput renders below categories
    - Tapping category navigates to /swipe with category param
    - Submitting text input navigates to /swipe with freeText param
  </verify>
  <done>
    Phase 2 complete: Users can select cravings to start swipe sessions
  </done>
</task>
