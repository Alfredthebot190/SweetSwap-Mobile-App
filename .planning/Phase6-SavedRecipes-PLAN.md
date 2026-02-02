# Phase 6: Saved Recipes - Implementation Plans

## Overview
Build saved recipes tab with list view, management, and empty state.

---

## Plan 1: Saved Recipes Query & Data Layer

<task type="auto">
  <name>Create saved recipes data layer with Convex</name>
  <files>
    convex/queries.ts
    convex/schema.ts (update)
  </files>
  <action>
    Create Convex queries for saved recipes:
    1. Update schema to add savedRecipes table:
       - id: v.id('savedRecipes')
       - userId: v.id('users')
       - recipeId: v.id('recipes')
       - savedAt: v.number()
       - Compound index: userId, savedAt

    2. Create queries:
       - getSavedRecipes(userId)
         - Fetch all saved recipes for user
         - Sort by savedAt (descending)
         - Join with recipes table for full data
         - Pagination support (optional)

       - isRecipeSaved(userId, recipeId)
         - Check if specific recipe is saved
         - Return boolean

       - getSavedRecipeCount(userId)
         - Return total count of saved recipes

    3. Create mutations:
       - saveRecipe(userId, recipeId)
         - Insert into savedRecipes table
         - Return new document
         - Prevent duplicates (check first)

       - unsaveRecipe(userId, recipeId)
         - Delete from savedRecipes table
         - Return success boolean

    4. Add indexes:
       - Index on userId for fast lookups
       - Index on recipeId for existence checks
  </action>
  <verify>
    - savedRecipes table exists in schema
    - getSavedRecipes returns sorted list
    - isRecipeSaved returns correct boolean
    - saveRecipe adds entry correctly
    - unsaveRecipe removes entry correctly
    - Duplicates prevented by saveRecipe
  </verify>
  <done>
    Convex data layer for saved recipes complete
  </done>
</task>

---

## Plan 2: SavedRecipesList UI Component

<task type="auto">
  <name>Create saved recipes list UI component</name>
  <files>
    src/components/saved/SavedRecipesList.tsx
    src/components/saved/SavedRecipeCard.tsx
  </files>
  <action>
    Create saved recipes list components:
    1. SavedRecipeCard component:
       - Recipe thumbnail (placeholder)
       - Recipe title (truncated)
       - Diet badge
       - Category label
       - Saved date (relative time)
       - Tap to view full recipe
       - Delete button (swipe or icon)
       - Heart icon to indicate saved status

    2. SavedRecipesList component:
       - FlatList for performance
       - Fetch saved recipes from Convex query
       - Show loading state while fetching
       - Show empty state if no recipes
       - Scrollable list with proper spacing
       - Pull-to-refresh (optional)

    3. Delete interaction:
       - Swipe-to-delete on each card
       - Confirmation dialog before delete
       - Optimistic delete with rollback
       - Undo toast notification

    4. Empty state:
       - "No saved recipes yet"
       - CTA: "Start swiping to save recipes"
       - Link to craving screen
       - Empty state illustration/icon

    Style decisions:
    - Card: 90% width, padding 16px, rounded corners 16px
    - Thumbnail: 80x80px, rounded 12px
    - Delete button: red, trash icon
    - Empty state: centered, large emoji + text
  </action>
  <verify>
    - SavedRecipesList renders recipe cards
    - Cards show title, thumbnail, diet badge, category
    - List is scrollable and performs well
    - Loading state shows spinner
    - Empty state displays when no recipes
    - Tapping card navigates to recipe modal
    - Swipe-to-delete works on cards
    - Delete shows confirmation dialog
    - Deleting updates list with animation
    - Undo toast appears after delete
  </verify>
  <done>
    Saved recipes list UI complete
  </done>
</task>

---

## Plan 3: Saved Tab Integration & Navigation

<task type="auto">
  <name>Integrate saved recipes into app navigation</name>
  <files>
    src/app/(tabs)/saved/index.tsx
    src/components/navigation/TabBar.tsx (create)
  </files>
  <action>
    Create saved tab and navigation integration:
    1. Saved tab screen:
       - Page header: "Saved Recipes"
       - Subtitle: "Your healthy favorites"
       - SavedRecipesList component
       - Sync with main app state
       - Refresh on mount

    2. Tab bar integration:
       - Add "Saved" tab to tab navigator
       - Icon: heart/bookmark
       - Badge showing count (optional)
       - Active state styling

    3. Real-time updates:
       - Listen to Convex real-time updates
       - Update list when recipes are saved/unsaved
       - Sync count with badge (if used)

    4. Navigation from other screens:
       - Swipe screen updates saved count
       - Recipe modal updates saved state
       - Save button reflects current state

    5. Error handling:
       - Show error message on fetch failure
       - Retry button on error
       - Graceful degradation if Convex is down

    Style decisions:
    - Header: 24-28px bold
    - Subtitle: 14-16px gray
    - Tab bar: bottom fixed, 4 tabs
    - Tab height: 56px
  </action>
  <verify>
    - Saved tab renders at /saved route
    - Header and subtitle display correctly
    - SavedRecipesList renders with data
    - Tab bar shows "Saved" tab with heart icon
    - Tapping "Saved" tab navigates correctly
    - Count updates when saving/un saving recipes
    - List updates in real-time from other screens
    - Error state shows on fetch failure
    - Retry button works to refetch
  </verify>
  <done>
    Phase 6 complete: Users can manage saved recipes
  </done>
</task>
