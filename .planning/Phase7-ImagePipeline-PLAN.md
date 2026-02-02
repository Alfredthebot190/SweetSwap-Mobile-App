# Phase 7: Image Pipeline - Implementation Plans

## Overview
Implement category placeholder images and image caching system with lazy loading.

---

## Plan 1: Category Placeholder Images

<task type="auto">
  <name>Create category placeholder images</name>
  <files>
    assets/images/categories/
    src/constants/categoryImages.ts
  </files>
  <action>
    Create category placeholder images:
    1. Create or source placeholder images for each category:
       - chocolate.png (brownie-like)
       - icecream.png (scoop of ice cream)
       - cookies.png (chocolate chip cookies)
       - cake.png (slice of cake)
       - candy.png (colorful candies)
       - creamy.png (pudding/mousse)
       - fruity.png (fruit tart)

    2. Image specifications:
       - Size: 400x400px (or 2:1 ratio)
       - Format: PNG with transparency
       - Style: Minimalist, appetizing
       - Consistent art direction
       - Optimized file size (< 50KB each)

    3. Create categoryImages constant:
       - Map category names to image paths
       - Export for use across app
       - Include fallback image

    4. Create Image utility:
       - getCategoryImage(category: string)
       - Returns image path
       - Returns fallback if category not found
       - Type-safe category mapping

    Style decisions:
    - Use pastel colors matching category
    - Simple, clean illustrations
    - No text in images (use overlay for text)
  </action>
  <verify>
    - 7 category placeholder images exist in assets/images/categories/
    - All images meet size specifications
    - categoryImages constant maps all categories
    - getCategoryImage utility works
    - Fallback image returned for invalid category
  </verify>
  <done>
    Category placeholder images complete
  </done>
</task>

---

## Plan 2: Image Caching with expo-image

<task type="auto">
  <name>Implement image caching with expo-image</name>
  <files>
    src/lib/imageCache.ts
    src/components/common/CachedImage.tsx
    package.json (update)
  </files>
  <action>
    Set up image caching with expo-image:
    1. Install expo-image:
       - npm install expo-image
       - Check compatibility with Expo SDK 52+

    2. Create image cache utility:
       - Configure cache strategy (memory + disk)
       - Set cache size limits (memory: 50MB, disk: 200MB)
       - Set cache expiration (7 days)
       - Clear cache function

    3. Create CachedImage component:
       - Wrapper around Expo Image component
       - Auto-caching enabled
       - Loading state (spinner or skeleton)
       - Error state (fallback image)
       - Accepts same props as Expo Image

    4. Cache configuration:
       - Preload critical images on app start
       - Background prefetch for upcoming recipes
       - Clear stale cache on app update

    5. Cache monitoring:
       - Track cache hit rate
       - Log cache size periodically
       - Debug mode to see cache status

    Style decisions:
    - Loading skeleton: gray background with shimmer
    - Error fallback: generic food icon
  </action>
  <verify>
    - expo-image is installed
    - CachedImage component works
    - Images load and cache correctly
    - Cache persists across app restarts
    - Clear cache function works
    - Loading state shows correctly
    - Error state shows fallback image
    - Preloading happens on app start
  </verify>
  <done>
    Image caching with expo-image complete
  </done>
</task>

---

## Plan 3: Lazy Loading with Blur-Up Effect

<task type="auto">
  <name>Add lazy loading with blur-up effect</name>
  <files>
    src/components/common/BlurImage.tsx
    src/lib/imageUtils.ts
  </files>
  <action>
    Implement lazy loading with blur-up:
    1. Create blur-up utility:
       - generateBlurPlaceholder(imageUrl)
         - Fetch image at low resolution
         - Convert to base64
         - Create small placeholder (10-20px)
         - Apply blur effect (CSS filter)

    2. Create BlurImage component:
       - Uses React Native's ImageBackground
       - Renders blur placeholder initially
       - Crossfades to full image on load
       - Animated transition (opacity 0 → 1)
       - Fade duration: 300-500ms

    3. Lazy loading strategy:
       - Only load images when in viewport
       - Use FlatList's onViewableItemsChanged
       - Preload images 1-2 items ahead
       - Unload off-screen images

    4. Performance optimizations:
       - Lazy load for recipe cards
       - Eager load for current/next card in stack
       - Thumbnail strategy for list views
       - Full quality for modal view

    5. Fallback chain:
       - Try blur-up first
       - Fall back to CachedImage
       - Finally fall back to static placeholder

    Style decisions:
    - Blur radius: 10-20px
    - Transition timing: ease-out
    - Placeholder color: gray-100
  </action>
  <verify>
    - BlurImage component renders blur placeholder initially
    - Full image loads and crossfades smoothly
    - Transition animation is smooth (300-500ms)
    - Only visible images load
    - Off-screen images don't load
    - Preloading happens for upcoming items
    - Blur placeholder matches full image colors
    - Fallback chain works correctly
    - Performance is smooth with many images
  </verify>
  <done>
    Phase 7 complete: Image pipeline with caching and lazy loading
  </done>
</task>
