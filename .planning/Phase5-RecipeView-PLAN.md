# Phase 5: Recipe View - Implementation Plans

## Overview
Build full-screen recipe modal with ingredients, instructions, prep time, and health benefits.

---

## Plan 1: Recipe Modal Structure & Navigation

<task type="auto">
  <name>Create recipe modal with navigation</name>
  <files>src/app/recipe/[id].tsx</files>
  <action>
    Create full-screen recipe modal:
    1. Expo Router modal route:
       - Dynamic route: /recipe/[id]
       - Modal presentation (sheet-like)
       - Back button (X icon) to dismiss
       - Swipe down to dismiss gesture

    2. Modal structure:
       - Scrollable content area
       - Fixed header with back button
       - Hero section at top
       - Recipe details below
       - Save button fixed at bottom

    3. Recipe fetching:
       - Use Convex query to fetch recipe by ID
       - Loading state: skeleton UI
       - Error state: error message + retry button

    4. Navigation integration:
       - Connect to CardStack (card tap opens modal)
       - Pass recipe ID via router.push
       - Handle case when recipe not found

    Style decisions:
    - Modal max-width: 600px (on larger screens)
    - Header height: 56px
    - Bottom button: full-width, 56px height
    - Back button: top-left, 44px tap area
  </action>
  <verify>
    - Recipe modal opens when navigating to /recipe/[id]
    - Recipe loads by ID from Convex
    - Back button closes modal
    - Swipe down gesture dismisses modal
    - Loading state shows while fetching
    - Error state shows when fetch fails
  </verify>
  <done>
    Recipe modal structure with navigation complete
  </done>
</task>

---

## Plan 2: Recipe Content Components

<task type="auto">
  <name>Create recipe content components</name>
  <files>
    src/components/recipe/RecipeHero.tsx
    src/components/recipe/IngredientsList.tsx
    src/components/recipe/InstructionsList.tsx
    src/components/recipe/HealthBenefits.tsx
  </files>
  <action>
    Create recipe content components:
    1. RecipeHero:
       - Recipe title (large, bold)
       - Recipe description
       - Prep time badge
       - Diet badge
       - Placeholder image

    2. IngredientsList:
       - Section header "Ingredients"
       - List of ingredient items
       - Each item: amount, unit, item name
       - Numbered or bullet list
       - Tappable for checking off

    3. InstructionsList:
       - Section header "Instructions"
       - Numbered step list
       - Each step in separate card
       - Tap to expand/collapse long steps
       - Auto-scroll to next step

    4. HealthBenefits:
       - Section header "Why This Is Healthier"
       - Benefit text with highlights
       - Educational tone
       - Expand/collapse for long content

    Style decisions:
    - Hero: gradient background or image
    - Prep time: small badge with icon
    - Ingredients: compact list, 12-14px font
    - Instructions: step cards, 14-16px font
    - Health benefits: highlighted text, soft background
  </action>
  <verify>
    - RecipeHero renders title, description, prep time, diet badge
    - IngredientsList renders all ingredients with amounts
    - InstructionsList renders numbered steps
    - HealthBenefits renders educational content
    - Long steps can be expanded/collapsed
    - All components use appropriate Tamagui primitives
  </verify>
  <done>
    Recipe content components complete
  </done>
</task>

---

## Plan 3: Save Button & Swipe Integration

<task type="auto">
  <name>Add save button and integrate with swipe</name>
  <files>
    src/app/recipe/[id].tsx (update)
    src/components/swipe/SwipeCard.tsx (update)
    src/hooks/useRecipeSave.ts
  </files>
  <action>
    Add save functionality:
    1. useRecipeSave hook:
       - saveRecipe(recipeId) function
       - Optimistic UI update
       - Convex mutation to save recipe
       - Update saved recipes list
       - Check if already saved

    2. Save button in modal:
       - Fixed at bottom of modal
       - "Save Recipe" text
       - Heart icon
       - Shows "Saved!" state after save
       - Disabled if already saved

    3. SwipeCard tap integration:
       - Open recipe modal on card tap
       - Pass recipe ID via router
       - Prevent swipe gesture during tap

    4. Save state management:
       - Track saved recipes in Convex
       - Query user's saved recipes
       - Update save button state across app
       - Sync with swipe saves

    5. Animation:
       - Button pulse animation on save
       - Heart fills with animation
       - Success feedback (haptic + visual)

    Style decisions:
    - Button: gradient background, white text
    - Saved state: filled heart, green background
    - Button shadow: elevation 4-6
  </action>
  <verify>
    - Save button renders at bottom of modal
    - Tapping save button saves recipe to Convex
    - Button changes to "Saved!" after save
    - Button is disabled for already saved recipes
    - Tap on card opens recipe modal
    - Save from modal syncs with swipe saves
    - Haptic feedback triggers on save
  </verify>
  <done>
    Phase 5 complete: Users can view and save full recipes
  </done>
</task>
