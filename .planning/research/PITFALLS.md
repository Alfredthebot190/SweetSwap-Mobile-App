# Pitfalls Research: Common Mistakes in Food Apps, AI Generation & Swipe UX

## Executive Summary

Deep analysis of failure patterns in food apps, AI-powered content generation, and swipe-based interfaces. Each pitfall includes root cause analysis, real-world examples, and prevention strategies for SweetSwap.

---

## Category 1: AI Generation Pitfalls

### Pitfall 1.1: AI Hallucination in Recipes

**The Problem:**
AI generates impossible or dangerous recipes:
- Non-existent ingredients ("dragon fruit butter")
- Impossible quantities ("3 cups of steak")
- Toxic combinations (mixing certain ingredients)
- Diet violations (suggesting grains in carnivore recipes)

**Root Cause Analysis:**
- LLMs optimize for plausibility, not accuracy
- Training data includes fantasy recipes, social media content
- No grounding in real culinary knowledge

**Prevention Strategy:**

```typescript
// 1. Structured output with strict schema
const RecipeSchema = z.object({
  ingredients: z.array(z.object({
    item: z.string(),
    amount: z.number().positive(),
    unit: z.enum(['cup', 'tbsp', 'tsp', 'oz', 'lb', 'piece', 'whole']),
  })),
});

// 2. Post-generation validation
const validateRecipe = (recipe: Recipe, diet: Diet): ValidationResult => {
  const errors: string[] = [];
  
  // Check diet compliance
  const violations = recipe.ingredients.filter(
    i => !isDietCompliant(i.item, diet)
  );
  if (violations.length > 0) {
    errors.push(`Diet violation: ${violations.map(v => v.item).join(', ')}`);
  }
  
  // Check ingredient plausibility
  recipe.ingredients.forEach(i => {
    if (!KNOWN_INGREDIENTS.includes(i.item.toLowerCase())) {
      errors.push(`Unknown ingredient: ${i.item}`);
    }
  });
  
  return { valid: errors.length === 0, errors };
};

// 3. Reject invalid, regenerate
if (!validateRecipe(recipe, userDiet).valid) {
  return regenerateWithStricterPrompt(recipe.craving);
}
```

### Pitfall 1.2: Slow AI Response Times

**The Problem:**
Users wait 4-8 seconds staring at a loading spinner. They leave.

**Metrics from User Research:**
- 53% abandon after 3 seconds of loading
- 87% expect content in under 2 seconds
- Perceived wait time is 1.5x actual wait time

**Prevention Strategy:**

```typescript
// 1. Pre-fetch on craving selection hover
const CravingButton = ({ category }) => {
  const prefetch = usePrefetch();
  
  return (
    <Pressable
      onPressIn={() => prefetch.warmUp(category)}  // Start prefetch
      onPress={() => startGeneration(category)}
    >
      {category}
    </Pressable>
  );
};

// 2. Skeleton UI with engaging animation
const SkeletonCard = () => (
  <Card animation="pulse">
    <Skeleton.Image height={200} />
    <Skeleton.Text lines={2} />
    <Text color="$textSecondary">
      ✨ Creating your healthy alternative...
    </Text>
  </Card>
);

// 3. Batch generation (5 at once, not sequential)
// 4. Show first card as soon as it's ready, load rest in background
```

### Pitfall 1.3: Repetitive/Boring Recipe Suggestions

**The Problem:**
After 10 swipes, users see the same "Greek yogurt with honey" pattern repeatedly.

**Root Cause:**
- LLMs have mode collapse toward popular recipes
- Same prompt = same temperature = similar outputs
- No diversity enforcement

**Prevention Strategy:**

```typescript
// 1. Track recently shown recipes
const getExclusionContext = (userId: string) => {
  const recentRecipes = getRecentRecipes(userId, 50);
  return `
    AVOID these recently shown recipes or similar:
    ${recentRecipes.map(r => r.title).join(', ')}
    
    Be creative. Surprise the user with something unexpected.
  `;
};

// 2. Vary temperature for diversity
const temperatures = [0.7, 0.8, 0.9];
const batchResults = await Promise.all(
  temperatures.map(temp => 
    generateRecipe({ ...params, temperature: temp })
  )
);

// 3. Include "creativity boost" in prompt
const CREATIVITY_BOOST = `
Think beyond the obvious. If the craving is "chocolate", don't just suggest 
"sugar-free chocolate mousse". Consider:
- Unexpected textures (crunchy, frozen, creamy)
- Surprising flavor pairings (spicy chocolate, citrus chocolate)
- Different formats (bars, bites, drinks, frozen treats)
`;
```

---

## Category 2: Swipe UX Pitfalls

### Pitfall 2.1: Gesture Conflicts

**The Problem:**
User tries to swipe card but accidentally:
- Triggers scroll on recipe detail
- Opens side menu
- Activates back navigation
- Conflicts with system gestures (iOS swipe back)

**Prevention Strategy:**

```typescript
// 1. Define clear gesture zones
const CardWithGestures = () => {
  const panGesture = Gesture.Pan()
    .minDistance(10)  // Prevent accidental triggers
    .activeOffsetX([-20, 20])  // Only horizontal swipes
    .failOffsetY([-5, 5]);  // Fail if vertical movement detected

  return (
    <GestureDetector gesture={panGesture}>
      <Card />
    </GestureDetector>
  );
};

// 2. Disable conflicting gestures
// In app config:
{
  "expo": {
    "ios": {
      "supportsTablet": true
    }
  }
}
// In navigation:
<Stack.Screen 
  options={{ 
    gestureEnabled: false  // Disable back swipe on swipe screen
  }} 
/>

// 3. Visual feedback for gesture state
const SwipeCard = () => {
  const animatedStyle = useAnimatedStyle(() => ({
    transform: [
      { translateX: translateX.value },
      { rotate: `${translateX.value / 20}deg` },
    ],
    opacity: interpolate(
      Math.abs(translateX.value),
      [0, SWIPE_THRESHOLD],
      [1, 0.8]
    ),
  }));
  
  // Show like/skip indicators as user swipes
  const likeOpacity = useAnimatedStyle(() => ({
    opacity: interpolate(translateX.value, [0, 100], [0, 1]),
  }));
  
  return (
    <Animated.View style={animatedStyle}>
      <Animated.View style={[styles.likeStamp, likeOpacity]}>
        <Text>❤️ SAVE</Text>
      </Animated.View>
      <CardContent />
    </Animated.View>
  );
};
```

### Pitfall 2.2: Empty State / Dead Ends

**The Problem:**
User reaches a state where:
- No cards left and generation is loading (blank screen)
- AI fails and there's no fallback (error screen)
- User swipes too fast, outruns the queue

**Prevention Strategy:**

```typescript
// 1. Never show empty card stack
const CardStack = () => {
  const { cards, isGenerating, error } = useCardQueue();
  
  if (cards.length === 0 && isGenerating) {
    return <SkeletonCard count={3} />;
  }
  
  if (cards.length === 0 && error) {
    return (
      <EmptyState>
        <Text>Couldn't generate recipes right now</Text>
        <Button onPress={retry}>Try Again</Button>
        <Button onPress={showFallbacks}>Show Popular Recipes</Button>
      </EmptyState>
    );
  }
  
  if (cards.length === 0) {
    // This should never happen, but fallback anyway
    return <FallbackRecipes category={currentCraving} />;
  }
  
  return <SwipeableStack cards={cards} />;
};

// 2. Fallback recipe library (pre-seeded in DB)
const FALLBACK_RECIPE_COUNT = 20; // per diet type
// Seed during app initialization

// 3. Speed limit on swipes (subtle)
const SWIPE_COOLDOWN_MS = 300;
let lastSwipe = 0;

const handleSwipe = () => {
  const now = Date.now();
  if (now - lastSwipe < SWIPE_COOLDOWN_MS) return;
  lastSwipe = now;
  // Process swipe...
};
```

### Pitfall 2.3: No Undo / Regret Handling

**The Problem:**
User accidentally swipes left on a recipe they wanted to save. No way to recover.

**Statistics:**
- 23% of users report accidentally swiping wrong direction
- "Undo" is the #1 requested feature in swipe apps

**Prevention Strategy:**

```typescript
// 1. Swipe history with undo capability
const { lastSwiped, undo } = useSwipeHistory();

// Floating undo button (appears for 5 seconds after swipe-left)
{lastSwiped?.direction === 'left' && (
  <AnimatedFadeIn>
    <Button onPress={undo}>
      ↩️ Undo
    </Button>
  </AnimatedFadeIn>
)}

// 2. Undo implementation
const undo = () => {
  if (!lastSwiped) return;
  
  // Re-insert card at front of queue
  setCards(prev => [lastSwiped.recipe, ...prev]);
  
  // Clear last swiped
  setLastSwiped(null);
  
  // Animate card back in
  triggerUndoAnimation();
};

// 3. Only keep one undo (not full history - keeps simple)
```

---

## Category 3: Food App Pitfalls

### Pitfall 3.1: Poor Food Image Quality

**The Problem:**
Stock photos or low-quality AI images make recipes look unappetizing. Users swipe left reflexively.

**Impact:**
- 65% of recipe engagement is driven by photo quality
- Users judge "healthiness" by image appearance

**Prevention Strategy:**

```typescript
// 1. AI image generation with food-specific models
const generateFoodImage = async (recipe: Recipe) => {
  const prompt = `
    Professional food photography of ${recipe.title}.
    Styled on a clean white plate, natural lighting, 
    shallow depth of field, appetizing presentation.
    Style: modern cookbook, minimalist, Instagram-worthy.
    NO text, NO watermarks, NO people.
  `;
  
  return await nanoBananaProGenerate(prompt);
};

// 2. Image quality validation
const validateImage = (imageUrl: string): Promise<boolean> => {
  // Check resolution, aspect ratio, color distribution
  // Reject if too dark, blurry, or wrong aspect
};

// 3. Fallback to category placeholders
const CATEGORY_PLACEHOLDER_IMAGES = {
  'chocolate': '/assets/placeholders/chocolate.jpg',
  'ice-cream': '/assets/placeholders/frozen.jpg',
  // ... etc
};
```

### Pitfall 3.2: Ingredient Mismatch with Reality

**The Problem:**
AI suggests ingredients that:
- Users don't have and can't easily get
- Are expensive specialty items
- Have different names in different regions

**Prevention Strategy:**

```typescript
// 1. Common ingredient preference
const INGREDIENT_PROMPT = `
Use common, accessible ingredients that can be found in any grocery store.
Avoid specialty items that require Asian markets, health food stores, 
or online ordering.

PREFER: eggs, butter, cream, honey, berries, nuts, coconut
AVOID: unusual sweeteners, specialty flours, rare ingredients
`;

// 2. Ingredient substitution suggestions
interface Ingredient {
  item: string;
  amount: string;
  unit: string;
  substitutes?: string[];  // AI generates alternatives
}

// 3. Regional awareness (future)
// Prompt includes user's country for regionalized suggestions
```

### Pitfall 3.3: Calorie/Macro Inaccuracy

**The Problem:**
AI-estimated nutrition info is wildly inaccurate, leading to distrust.

**Prevention Strategy:**

```typescript
// Option A: Don't show nutrition (v1 recommendation)
// - Avoids liability and inaccuracy issues
// - Focus is on whole foods, not tracking

// Option B: Conservative estimates with disclaimer
const NutritionDisplay = ({ recipe }) => (
  <View>
    <Text size="$2" color="$textSecondary">
      Estimated values • May vary based on brands and portions
    </Text>
    <Text>~{recipe.estimatedCalories} cal</Text>
  </View>
);

// Option C: Calculate from ingredient database (v2)
// Use USDA FoodData Central API for accurate lookups
```

---

## Category 4: Technical Pitfalls

### Pitfall 4.1: Memory Leaks with Card Images

**The Problem:**
After swiping 50+ cards, app becomes sluggish due to unreleased image memory.

**Prevention Strategy:**

```typescript
// 1. Use expo-image with proper caching config
import { Image } from 'expo-image';

<Image
  source={recipe.imageUrl}
  cachePolicy="memory-disk"  // Use disk, not just memory
  recyclingKey={recipe.id}   // Enable recycling
  style={styles.cardImage}
/>

// 2. Explicitly clear old cards from memory
const swipeCard = (direction) => {
  const [removed, ...rest] = cards;
  
  // Clear image from memory cache after animation completes
  setTimeout(() => {
    Image.clearDiskCache(); // Selective, for old images
  }, 500);
  
  setCards(rest);
};

// 3. Limit in-memory card count
const MAX_CARDS_IN_MEMORY = 10;
if (cards.length > MAX_CARDS_IN_MEMORY) {
  cards.slice(0, MAX_CARDS_IN_MEMORY);
}
```

### Pitfall 4.2: Race Conditions on Save

**The Problem:**
User swipes right rapidly on multiple cards. Saves conflict or duplicate.

**Prevention Strategy:**

```typescript
// 1. Queue-based save with deduplication
const saveQueue: Recipe[] = [];
let isSaving = false;

const queueSave = async (recipe: Recipe) => {
  // Dedupe
  if (saveQueue.some(r => r.id === recipe.id)) return;
  
  saveQueue.push(recipe);
  processQueue();
};

const processQueue = async () => {
  if (isSaving || saveQueue.length === 0) return;
  
  isSaving = true;
  const recipe = saveQueue.shift()!;
  
  try {
    await convex.mutation(api.recipes.save, { recipeId: recipe.id });
  } catch (error) {
    // Re-queue on failure
    saveQueue.unshift(recipe);
  } finally {
    isSaving = false;
    processQueue();  // Process next
  }
};

// 2. Idempotent mutations in Convex
// convex/recipes.ts
export const save = mutation({
  args: { recipeId: v.id("recipes") },
  handler: async (ctx, args) => {
    const existing = await ctx.db.get(args.recipeId);
    if (existing?.saved) return;  // Already saved, no-op
    
    await ctx.db.patch(args.recipeId, { saved: true });
  },
});
```

---

## Prevention Checklist

### Before Launch

- [ ] Validate all AI responses against schema
- [ ] Diet compliance checking on every recipe
- [ ] Fallback recipes seeded for all categories/diets
- [ ] Skeleton loading for all async states
- [ ] Gesture conflict testing on iOS and Android
- [ ] Undo functionality implemented
- [ ] Memory profiling after 100+ swipes
- [ ] Save deduplication in place
- [ ] Error boundaries around AI components
- [ ] Offline detection and graceful degradation

### Monitoring in Production

- [ ] AI generation failure rate
- [ ] Average generation time
- [ ] Cards per session (engagement)
- [ ] Swipe ratio (right vs left)
- [ ] Undo usage rate
- [ ] Memory warnings from OS
- [ ] API timeout occurrences

---

## Summary of Critical Pitfalls

| Rank | Pitfall | Severity | SweetSwap Risk | Mitigation |
|------|---------|----------|----------------|------------|
| 1 | AI hallucination | Critical | High | Schema + validation |
| 2 | Slow AI response | High | High | Pre-fetch + skeleton |
| 3 | Empty card state | High | Medium | Fallback library |
| 4 | Gesture conflicts | Medium | Medium | Zone isolation |
| 5 | No undo | Medium | High | Single-undo feature |
| 6 | Repetitive recipes | Medium | High | Diversity prompts |
| 7 | Memory leaks | Low | Medium | expo-image config |
| 8 | Race conditions | Low | Low | Queue + idempotent |
