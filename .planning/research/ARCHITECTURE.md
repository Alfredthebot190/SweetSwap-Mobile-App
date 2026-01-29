# Architecture Research: Swipe-Based AI Generation Apps

## Executive Summary

Analysis of how modern swipe-based apps with AI generation (Tinder, Bumble, AI photo apps) structure their architecture. Focus on card queue management, AI pipelining, and optimistic UI patterns critical for SweetSwap's UX.

---

## System Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────────┐
│                              SWEETSWAP                                   │
├─────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐                  │
│  │   SWIPE     │    │   RECIPE    │    │   SAVED     │     Expo Router  │
│  │   SCREEN    │───▶│   MODAL     │    │   TAB       │     Navigation   │
│  └──────┬──────┘    └─────────────┘    └─────────────┘                  │
│         │                                                                │
│  ┌──────▼──────────────────────────────────────────────────────────┐    │
│  │                     CARD QUEUE MANAGER                           │    │
│  │  ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐         │    │
│  │  │ Card 1 │ │ Card 2 │ │ Card 3 │ │ Card 4 │ │ Card 5 │  Queue  │    │
│  │  │VISIBLE │ │PRELOAD │ │PRELOAD │ │LOADING │ │LOADING │         │    │
│  │  └────────┘ └────────┘ └────────┘ └────────┘ └────────┘         │    │
│  └──────┬──────────────────────────────────────────────────────────┘    │
│         │                                                                │
├─────────┼────────────────────────────────────────────────────────────────┤
│         │             STATE LAYER (Zustand / Jotai)                      │
│  ┌──────▼──────┐    ┌─────────────┐    ┌─────────────┐                  │
│  │  cardQueue  │    │ swipeHistory│    │savedRecipes │    Global State  │
│  └─────────────┘    └─────────────┘    └─────────────┘                  │
├──────────────────────────────────────────────────────────────────────────┤
│                           CONVEX LAYER                                   │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐                  │
│  │  Mutations  │    │   Queries   │    │   Actions   │    Real-time     │
│  │  saveRecipe │    │ getSaved    │    │ generateAI  │    Sync          │
│  └──────┬──────┘    └─────────────┘    └──────┬──────┘                  │
│         │                                      │                         │
├─────────┼──────────────────────────────────────┼─────────────────────────┤
│         ▼                                      ▼                         │
│  ┌─────────────┐                     ┌───────────────────┐              │
│  │   Convex    │                     │    OpenRouter     │  External    │
│  │   Database  │                     │  (Gemini 2.5 AI)  │  Services    │
│  └─────────────┘                     └───────────────────┘              │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## Card Queue Management

### The Queue Pattern

**Problem**: AI generation is slow (2-4 seconds). Users should never wait.

**Solution**: Maintain a queue of 5+ cards, continuously refilled as cards are swiped.

```typescript
// stores/cardQueueStore.ts
interface CardQueueState {
  cards: Recipe[];
  isGenerating: boolean;
  currentCraving: string | null;
  
  // Actions
  initQueue: (craving: string, diet: string) => Promise<void>;
  swipeCard: (direction: 'left' | 'right') => void;
  refillQueue: () => Promise<void>;
}

export const useCardQueue = create<CardQueueState>((set, get) => ({
  cards: [],
  isGenerating: false,
  currentCraving: null,

  initQueue: async (craving, diet) => {
    set({ isGenerating: true, currentCraving: craving, cards: [] });
    
    // Generate initial batch of 5 recipes
    const recipes = await convex.action(api.ai.generateBatch, {
      craving,
      diet,
      count: 5,
    });
    
    set({ cards: recipes, isGenerating: false });
  },

  swipeCard: (direction) => {
    const { cards, refillQueue } = get();
    const [current, ...rest] = cards;
    
    // Record swipe
    if (direction === 'right') {
      convex.mutation(api.recipes.save, { recipeId: current.id });
    }
    convex.mutation(api.swipes.record, { 
      recipeId: current.id, 
      direction 
    });
    
    // Update queue
    set({ cards: rest });
    
    // Trigger refill when 2 cards remain
    if (rest.length <= 2) {
      refillQueue();
    }
  },

  refillQueue: async () => {
    const { isGenerating, currentCraving, cards } = get();
    if (isGenerating || !currentCraving) return;
    
    set({ isGenerating: true });
    
    const newRecipes = await convex.action(api.ai.generateBatch, {
      craving: currentCraving,
      diet: getUserDiet(),
      count: 3,
      excludeIds: cards.map(c => c.id),
    });
    
    set(state => ({ 
      cards: [...state.cards, ...newRecipes],
      isGenerating: false 
    }));
  },
}));
```

### Queue State Diagram

```
User selects craving
        │
        ▼
┌───────────────────┐
│ Show loading      │ ◀──┐
│ (skeleton cards)  │    │
└────────┬──────────┘    │
         │               │
         ▼               │
┌───────────────────┐    │
│ Batch generated   │    │
│ (5 recipes ready) │    │
└────────┬──────────┘    │
         │               │
         ▼               │
┌───────────────────┐    │
│ User swipes       │    │
│ cards             │    │
└────────┬──────────┘    │
         │               │
         ▼               │
    cards.length <= 2?   │
     │              │    │
     │yes           │no  │
     ▼              └────┘
┌───────────────────┐
│ Background refill │
│ (3 more recipes)  │
└───────────────────┘
```

---

## AI Generation Pipeline

### Optimizing for Latency

**Multi-step generation flow:**

```
1. User selects craving ─────────────────────────────┐
                                                      │
2. Immediately show skeleton cards ◀──────────────────┤
                                                      │
3. Convex action: generateBatch() ────────────────────┤
   │                                                  │
   ├─► Call OpenRouter (Gemini 2.5 Flash)             │
   │   - System prompt: Diet + formatting             │
   │   - User prompt: Craving + category              │
   │   - Response format: JSON schema                 │
   │                                                  │
   ├─► Parse structured JSON response                 │
   │                                                  │
   ├─► Store recipes in Convex DB                     │
   │                                                  │
   └─► Return recipe IDs to client                    │
                                                      │
4. Client fetches full recipes (real-time query) ─────┘
                                                      
5. Cards populated, skeleton → real content
```

### Structured Output Schema

```typescript
// AI response schema for type safety
const RecipeSchema = z.object({
  title: z.string().max(50),
  description: z.string().max(150),
  ingredients: z.array(z.object({
    item: z.string(),
    amount: z.string(),
    unit: z.string(),
  })).min(3).max(15),
  instructions: z.array(z.string()).min(3).max(10),
  prepTime: z.number().min(5).max(120),
  healthBenefits: z.string().max(200),
});

// Batch response
const BatchResponseSchema = z.object({
  recipes: z.array(RecipeSchema).length(5),
});
```

### Error Handling Strategy

```typescript
const generateWithRetry = async (params, retries = 3) => {
  for (let i = 0; i < retries; i++) {
    try {
      const response = await callOpenRouter(params);
      const parsed = BatchResponseSchema.parse(response);
      return parsed.recipes;
    } catch (error) {
      if (i === retries - 1) {
        // Final failure: return fallback recipes
        return getFallbackRecipes(params.craving, params.diet);
      }
      // Exponential backoff
      await sleep(Math.pow(2, i) * 1000);
    }
  }
};
```

---

## Optimistic UI Patterns

### Why Optimistic Updates Matter

**Without optimistic UI:**
```
Swipe right → Wait 500ms → Save confirmed → Heart animation
(User notices lag, feels unresponsive)
```

**With optimistic UI:**
```
Swipe right → Immediate heart animation → Background save
(Instant feedback, smooth UX)
```

### Implementation

```typescript
// Optimistic save with rollback
const handleSwipeRight = async (recipe: Recipe) => {
  // 1. Optimistic: Update local state immediately
  addToSavedRecipes(recipe);
  
  // 2. Trigger satisfying animation
  triggerSaveAnimation();
  
  // 3. Background sync to Convex
  try {
    await convex.mutation(api.recipes.save, { recipeId: recipe.id });
  } catch (error) {
    // 4. Rollback on failure
    removeFromSavedRecipes(recipe.id);
    showErrorToast("Couldn't save recipe. Try again?");
  }
};
```

---

## State Management Architecture

### Recommended: Zustand + Convex React

```
┌────────────────────────────────────────────────────────┐
│                    STATE LAYERS                         │
├────────────────────────────────────────────────────────┤
│                                                         │
│  LOCAL (Zustand)           │  SERVER (Convex)          │
│  ─────────────────         │  ────────────────         │
│  • cardQueue               │  • savedRecipes           │
│  • currentlyViewing        │  • userProfile            │
│  • animationState          │  • swipeHistory           │
│  • uiState                 │  • generatedRecipes       │
│                            │                           │
│  Fast, ephemeral           │  Persistent, synced       │
│                                                         │
└────────────────────────────────────────────────────────┘
```

### Why This Split?

| State Type | Storage | Reason |
|------------|---------|--------|
| Card queue | Zustand | Ephemeral, loses on craving change |
| Animation | Zustand | UI-only, no persistence needed |
| Saved recipes | Convex | Must sync across devices |
| User profile | Convex | Core user data |
| Swipe history | Convex | Analytics, prevents duplicates |

---

## Folder Structure

```
src/
├── app/                          # Expo Router pages
│   ├── (tabs)/
│   │   ├── _layout.tsx           # Tab navigator
│   │   ├── index.tsx             # Swipe screen
│   │   ├── saved.tsx             # Saved recipes
│   │   └── profile.tsx           # User profile
│   ├── recipe/
│   │   └── [id].tsx              # Recipe detail modal
│   ├── onboarding/
│   │   └── index.tsx             # Diet selection
│   └── _layout.tsx               # Root layout
│
├── components/
│   ├── cards/
│   │   ├── SwipeCard.tsx         # Individual swipeable card
│   │   ├── CardStack.tsx         # Stack manager
│   │   └── SkeletonCard.tsx      # Loading state
│   ├── recipe/
│   │   ├── RecipeView.tsx        # Full recipe display
│   │   └── IngredientList.tsx    # Ingredient component
│   └── ui/                       # Tamagui-based primitives
│
├── stores/
│   ├── cardQueueStore.ts         # Zustand card queue
│   └── uiStore.ts                # UI state
│
├── convex/
│   ├── schema.ts                 # Database schema
│   ├── recipes.ts                # Recipe mutations/queries
│   ├── users.ts                  # User operations
│   └── actions/
│       └── generateRecipe.ts     # AI generation action
│
├── lib/
│   ├── ai/
│   │   └── prompts.ts            # AI prompt templates
│   └── animations/
│       └── swipeConfig.ts        # Reanimated configs
│
└── tamagui.config.ts             # Tamagui setup
```

---

## Key Architectural Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| State split | Zustand + Convex | Fast local + synced server |
| Queue size | 5 cards | Balance memory vs latency |
| Refill trigger | 2 cards remaining | Time to generate before empty |
| Batch size | 5 initial, 3 refill | Optimal for API calls |
| Animation driver | Reanimated worklets | 60fps guaranteed |
| Optimistic saves | Yes | Critical for UX feel |
| Fallback recipes | Cached library | Never show "no content" |

---

## Recommendations

1. **Always maintain 3+ cards in queue** - User should never see loading after initial batch
2. **Skeleton cards during generation** - Show app is working, set expectations
3. **Convex for all persistent state** - Single source of truth, real-time sync
4. **Zustand for UI/animation state** - Fast, doesn't need persistence
5. **Batch operations** - Generate 5 recipes per API call, not 1
6. **Fallback recipe library** - Pre-populate with 20-30 recipes per diet for emergencies
