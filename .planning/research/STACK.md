# Stack Research: React Native/Expo for Swipe-Based Food Apps

## Executive Summary

SweetSwap requires a high-performance swipe interface with fluid 60fps animations, real-time AI integration, and cross-platform consistency. This research validates the chosen tech stack and identifies optimal library configurations.

---

## Core Framework Analysis

### Expo SDK 52+ (Recommended)

**Why Expo over Bare RN CLI:**
- **New Architecture Ready**: Expo SDK 52 ships with React Native 0.76 (Fabric renderer, TurboModules)
- **OTA Updates**: Critical for rapid iteration on AI-generated recipe quality
- **Managed Workflow**: Eliminates native build complexity, focus on product
- **Expo Router v4**: Type-safe file-based routing, deep linking, tabs

**Configuration for SweetSwap:**
```json
{
  "expo": {
    "newArchEnabled": true,
    "plugins": [
      "expo-router",
      ["expo-font", { "fonts": ["./assets/fonts/Inter.ttf"] }]
    ]
  }
}
```

---

## Animation & Gesture Stack

### Critical Dependencies

| Library | Version | Purpose |
|---------|---------|---------|
| `react-native-reanimated` | 3.16+ | Worklet-based 60fps animations |
| `react-native-gesture-handler` | 2.20+ | Native-driven gesture recognition |
| `react-native-redash` | 18+ | Animation utilities for swipe physics |

### Swipe Card Implementation Pattern

**Tinder-Style Card Stack Architecture:**

```typescript
// Gesture-driven card with spring physics
const panGesture = Gesture.Pan()
  .onUpdate((e) => {
    translateX.value = e.translationX;
    translateY.value = e.translationY;
  })
  .onEnd((e) => {
    const shouldSwipe = Math.abs(e.translationX) > SWIPE_THRESHOLD;
    
    if (shouldSwipe) {
      // Exit animation with velocity
      translateX.value = withSpring(
        Math.sign(e.translationX) * SCREEN_WIDTH * 1.5,
        { velocity: e.velocityX, damping: 15 }
      );
      runOnJS(onSwipe)(e.translationX > 0 ? 'right' : 'left');
    } else {
      // Snap back with spring
      translateX.value = withSpring(0, { damping: 20 });
      translateY.value = withSpring(0);
    }
  });
```

### Animation Performance Considerations

1. **Worklet Isolation**: All animation logic runs on UI thread via Reanimated worklets
2. **Derive Rotations**: Card rotation derived from translateX (no extra shared values)
3. **Pre-render Next Card**: Always have 3+ cards in memory for seamless transitions
4. **Image Preloading**: Use `expo-image` with prefetch for food images

---

## Backend Integration: Convex Cloud

### Why Convex for SweetSwap

| Feature | Benefit for SweetSwap |
|---------|----------------------|
| Real-time Subscriptions | Live updates when recipes save successfully |
| Serverless Functions | AI generation triggered without cold starts |
| Built-in Auth | Convex Auth for email/password, extensible for OAuth |
| TypeScript Schema | End-to-end type safety from DB to UI |
| Transactional Writes | Atomic recipe saves with all ingredients |

### Convex Schema Design

```typescript
// convex/schema.ts
import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";

export default defineSchema({
  users: defineTable({
    email: v.string(),
    dietPreference: v.union(
      v.literal("animal-based"),
      v.literal("carnivore"),
      v.literal("paleo"),
      v.literal("keto")
    ),
    onboardingComplete: v.boolean(),
  }).index("by_email", ["email"]),

  recipes: defineTable({
    userId: v.id("users"),
    craving: v.string(),
    cravingCategory: v.string(),
    title: v.string(),
    description: v.string(),
    ingredients: v.array(v.object({
      item: v.string(),
      amount: v.string(),
      unit: v.string(),
    })),
    instructions: v.array(v.string()),
    prepTime: v.number(),
    healthBenefits: v.string(),
    imageUrl: v.optional(v.string()),
    saved: v.boolean(),
    generatedAt: v.number(),
  }).index("by_user", ["userId"])
    .index("by_user_saved", ["userId", "saved"]),

  swipeHistory: defineTable({
    userId: v.id("users"),
    recipeId: v.id("recipes"),
    direction: v.union(v.literal("left"), v.literal("right")),
    swipedAt: v.number(),
  }).index("by_user", ["userId"]),
});
```

---

## UI Framework: Tamagui

### Why Tamagui over NativeWind/ShadCN

| Aspect | Tamagui | NativeWind | 
|--------|---------|------------|
| Native Performance | Compiles to native styles | Runtime className parsing |
| Design System | Built-in tokens, themes | Manual setup required |
| Animation Integration | `@tamagui/animations` | Separate library needed |
| Component Library | 50+ optimized components | Community libraries |

### Tamagui Configuration

```typescript
// tamagui.config.ts
import { createTamagui, createTokens } from 'tamagui';
import { createInterFont } from '@tamagui/font-inter';
import { shorthands } from '@tamagui/shorthands';
import { themes, tokens } from '@tamagui/themes';
import { createAnimations } from '@tamagui/animations-react-native';

const animations = createAnimations({
  fast: {
    type: 'spring',
    damping: 20,
    stiffness: 250,
  },
  medium: {
    type: 'spring',
    damping: 15,
    stiffness: 150,
  },
  bouncy: {
    type: 'spring',
    damping: 10,
    stiffness: 100,
  },
});

const sweetSwapTokens = createTokens({
  ...tokens,
  color: {
    ...tokens.color,
    // Brand colors
    primary: '#FF6B6B',      // Appetizing coral
    primaryDark: '#E85555',
    secondary: '#4ECDC4',    // Fresh mint
    background: '#FFF9F5',   // Warm cream
    cardBg: '#FFFFFF',
    textPrimary: '#2D3436',
    textSecondary: '#636E72',
    success: '#00B894',      // Save success
    skip: '#B2BEC3',         // Skip gray
  },
});

export const config = createTamagui({
  tokens: sweetSwapTokens,
  themes,
  shorthands,
  animations,
  fonts: {
    heading: createInterFont({ weight: { 6: '600', 7: '700' } }),
    body: createInterFont(),
  },
});
```

---

## AI Integration Architecture

### OpenRouter + Gemini 2.5 Flash

**Request Pattern for Recipe Generation:**

```typescript
// convex/actions/generateRecipe.ts
import { action } from "./_generated/server";
import { v } from "convex/values";

export const generateRecipe = action({
  args: {
    craving: v.string(),
    cravingCategory: v.string(),
    dietPreference: v.string(),
  },
  handler: async (ctx, args) => {
    const response = await fetch("https://openrouter.ai/api/v1/chat/completions", {
      method: "POST",
      headers: {
        "Authorization": `Bearer ${process.env.OPENROUTER_API_KEY}`,
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        model: "google/gemini-2.5-flash",
        messages: [{
          role: "user",
          content: buildRecipePrompt(args),
        }],
        response_format: { type: "json_object" },
      }),
    });
    
    const data = await response.json();
    return parseRecipeResponse(data);
  },
});
```

### Prompt Engineering for Recipe Quality

```typescript
const buildRecipePrompt = ({ craving, cravingCategory, dietPreference }) => `
You are a healthy dessert chef who creates ${dietPreference} alternatives to processed sweets.

User craving: "${craving}" (Category: ${cravingCategory})

Create a healthy alternative recipe that:
1. Satisfies the same craving profile (texture, sweetness, flavor notes)
2. Uses only whole-food, unprocessed ingredients
3. Fits strictly within ${dietPreference} diet guidelines
4. Is genuinely delicious, not a sad substitute

Respond in this exact JSON format:
{
  "title": "Recipe Name",
  "description": "One compelling sentence about why this satisfies the craving",
  "ingredients": [{"item": "ingredient", "amount": "1", "unit": "cup"}],
  "instructions": ["Step 1...", "Step 2..."],
  "prepTime": 15,
  "healthBenefits": "Why this is better than the processed version"
}
`;
```

---

## Performance Benchmarks

### Target Metrics

| Metric | Target | Critical For |
|--------|--------|--------------|
| Card Swipe FPS | 60 fps | Addictive UX feel |
| Time to First Card | < 2s | Immediate gratification |
| AI Response Time | < 3s | Keep engagement high |
| Image Load Time | < 500ms | Visual appeal |
| App Cold Start | < 1.5s | First impression |

### Optimization Strategies

1. **Batch AI Generation**: Generate 5 recipes per craving selection, queue cards
2. **Image Caching**: `expo-image` with aggressive memory + disk cache
3. **Skeleton Loading**: Show card outlines during AI generation
4. **Optimistic Saves**: Update UI immediately, sync to Convex async
5. **Prefetch Next Batch**: When 2 cards remain, trigger next generation

---

## Recommendations

1. **Use Expo SDK 52+** with New Architecture enabled for best performance
2. **Combine react-native-reanimated + gesture-handler** for swipe physics
3. **Convex Cloud** handles backend perfectly with real-time + auth + serverless
4. **Tamagui** for design system with native performance
5. **Pre-generate card batches** to mask AI latency
6. **expo-image** for aggressive image caching strategy
