# Phase 8: Onboarding & Profile - Implementation Plans

## Overview
Build diet selection onboarding flow and profile/settings screen.

---

## Plan 1: Onboarding Flow with Diet Selection

<task type="auto">
  <name>Create onboarding flow with diet selection</name>
  <files>
    src/app/onboarding/index.tsx
    src/app/onboarding/welcome.tsx
    src/app/onboarding/diet.tsx
    src/components/onboarding/DietCard.tsx
    src/stores/onboardingStore.ts
  </files>
  <action>
    Create onboarding flow:
    1. Onboarding store (Zustand):
       - State: currentStep, selectedDiet, hasSeenOnboarding
       - Actions: setDiet, nextStep, skipOnboarding, completeOnboarding
       - Persist to AsyncStorage

    2. Welcome screen (Step 1):
       - App logo and name (SweetSwap)
       - Value prop: "Craving sweets? We'll find healthy alternatives"
       - Subtitle explaining the experience
       - "Get Started" button
       - "Skip" link (optional)

    3. Diet selection screen (Step 2):
       - Header: "Choose your diet"
       - Description: "We'll tailor recipes to your preferences"
       - 4 diet cards:
         - Animal-based
         - Carnivore
         - Paleo
         - Keto
       - Each card: name, brief description, tap to select
       - Selected state: highlighted border, checkmark
       - "Continue" button (enabled when diet selected)
       - "Skip" link (defaults to Paleo)

    4. DietCard component:
       - Diet name (large)
       - Diet description
       - Example foods list
       - Tap to select
       - Animated selection state

    5. Navigation:
       - Welcome → Diet selection → App (complete)
       - Skip from any step → App (default Paleo)
       - Store hasSeenOnboarding = true after complete

    Style decisions:
    - Screen background: gradient or solid color
    - Diet card: rounded 16px, padding 20px
    - Selected state: blue border, background tint
    - Spacing: 24-32px between cards
  </action>
  <verify>
    - Onboarding starts on first app launch
    - Welcome screen shows app name and value prop
    - "Get Started" navigates to diet selection
    - Diet selection shows 4 diet cards
    - Tapping a diet card selects it (visual feedback)
    - "Continue" button enables when diet selected
    - Skipping defaults diet to Paleo
    - Completing onboarding sets hasSeenOnboarding = true
    - Returning user skips onboarding automatically
  </verify>
  <done>
    Onboarding flow complete
  </done>
</task>

---

## Plan 2: Profile Screen UI

<task type="auto">
  <name>Create profile/settings screen UI</name>
  <files>
    src/app/(tabs)/profile/index.tsx
    src/components/profile/ProfileHeader.tsx
    src/components/profile/SettingsSection.tsx
    src/components/profile/SettingItem.tsx
  </files>
  <action>
    Create profile screen components:
    1. Profile screen:
       - Page header: "Profile"
       - User info display (email)
       - Settings sections
       - Logout button
       - Delete account button

    2. ProfileHeader component:
       - User avatar (placeholder or initials)
       - User email
       - Diet preference display
       - "Edit" button (for diet change)

    3. SettingsSection component:
       - Section header (grouped settings)
       - List of SettingItem components
       - Divider between sections
       - Proper padding and spacing

    4. SettingItem component:
       - Setting label
       - Setting value (if applicable)
       - Chevron icon for navigation
       - Tap handler
       - Subtle tap feedback

    5. Sections:
       - Diet Preference: Change diet
       - Account: Change password, Delete account
       - App: Logout

    6. Logout flow:
       - Alert dialog confirmation
       - Clear Convex auth session
       - Navigate to login screen
       - Clear local state

    Style decisions:
    - Avatar: 80-100px, circular
    - Header: centered, large font
    - Settings sections: grouped with subtle background
    - Logout button: red, prominent
    - Delete account: red, less prominent
  </action>
  <verify>
    - Profile screen renders at /profile route
    - ProfileHeader shows avatar and email
    - Diet preference displays correctly
    - Settings sections render with correct items
    - "Change Diet" navigates to onboarding (diet selection)
    - "Change Password" navigates to password change screen
    - "Logout" button shows confirmation dialog
    - Confirming logout clears session and navigates to login
    - "Delete Account" button shows confirmation
  </verify>
  <done>
    Profile screen UI complete
  </done>
</task>

---

## Plan 3: Diet Change & Password Management

<task type="auto">
  <name>Implement diet change and password management</name>
  <files>
    src/app/profile/change-diet.tsx
    src/app/profile/change-password.tsx
    convex/mutations.ts (update)
  </files>
  <action>
    Implement diet change and password management:
    1. Change diet flow:
       - Reuse diet selection from onboarding
       - Update user record in Convex
       - Clear saved recipes (optional - user choice)
       - Show confirmation dialog
       - Navigate back to profile

    2. Change password screen:
       - Current password input
       - New password input
       - Confirm password input
       - Validation: new === confirm, min length
       - Submit button
       - Show/hide password toggles

    3. Convex mutations:
       - updateUserDiet(userId, diet)
         - Update user.diet field
         - Return updated user

       - changePassword(userId, currentPassword, newPassword)
         - Verify current password
         - Hash new password
         - Update user.password
         - Return success/failure

    4. Error handling:
       - Invalid current password message
       - Password too short message
       - Passwords don't match message
       - Network error message

    5. Success feedback:
       - Toast notification on success
       - Navigate back to profile
       - Update local state

    Style decisions:
    - Password screen: 16px minimum length
    - Success toast: green, dismissible
    - Error messages: red, below inputs
  </action>
  <verify>
    - "Change Diet" navigates to diet selection
       - Selecting new diet shows confirmation
       - Confirming updates user's diet in Convex
       - Optional: clearing saved recipes works
       - Success toast appears
       - Navigates back to profile
    - "Change Password" navigates to password screen
       - Current password input works
       - New password input works
       - Confirm password input works
       - Show/hide toggles work
       - Validation prevents submission if:
         - Current password is incorrect
         - New password < 16 characters
         - New passwords don't match
       - Submitting with valid data updates password
       - Success toast appears
       - Navigates back to profile
  </verify>
  <done>
    Phase 8 complete: Users can manage diet and password
  </done>
</task>
