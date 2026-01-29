# Features Research: Food/Recipe/Health App Standards

## Executive Summary

Analysis of successful food and health apps (MyFitnessPal, Yummly, Lifesum, Noom) to extract standard features and UX patterns for SweetSwap. Focus on what drives engagement and retention in the healthy eating space.

---

## Core Feature Categories

### 1. Onboarding & Personalization

**Industry Standard Features:**

| Feature | Adoption Rate | SweetSwap Relevance |
|---------|--------------|---------------------|
| Diet preference selection | 95% | **Critical** - Core differentiator |
| Goal setting | 85% | Low - Not tracking calories |
| Allergy/restriction input | 80% | Medium - Could add later |
| Taste preference quiz | 40% | High - Improve AI accuracy |
| Sample content preview | 70% | High - Show value immediately |

**Best Practices:**
- **Progressive disclosure**: Only ask essential questions upfront (diet type)
- **Show value fast**: Display first recipe card within 30 seconds of install
- **Skip option**: Allow skipping onboarding, infer preferences from behavior

**SweetSwap Onboarding Flow:**
```
Screen 1: Welcome + Value Prop (5s read)
Screen 2: Diet Selection (single tap: Animal-based/Carnivore/Paleo/Keto)
Screen 3: First Craving Selection → Immediately show cards
```

---

### 2. Recipe Management

**Standard Features Matrix:**

| Feature | Priority | Implementation Notes |
|---------|----------|---------------------|
| Recipe display (ingredients, steps) | P0 | Full-screen modal on card tap |
| Save/favorite recipes | P0 | Right swipe = save |
| Saved recipes list | P0 | Dedicated tab |
| Recipe search | P1 | Search saved recipes |
| Recipe categories/tags | P2 | Auto-tag by craving category |
| Shopping list generation | P2 | v2 feature |
| Meal planning/scheduling | P3 | Out of scope for v1 |
| Nutrition info | P2 | AI can generate estimates |
| Cooking timers | P3 | Out of scope |
| Recipe scaling | P2 | Simple multiplier |
| Recipe sharing | P1 | Share as text/image |

**UX Patterns from Top Apps:**

1. **Card-Based Recipe Preview** (Yummly, Tasty)
   - Large hero image (70% of card)
   - Title + prep time overlay
   - Quick save action

2. **Step-by-Step Instructions** (Tasty)
   - One step per screen option
   - Checkboxes for completion
   - Voice readout capability

3. **Ingredient Formatting** (AllRecipes)
   - Clear amount/unit/item separation
   - Substitution suggestions
   - "Add all to list" button

---

### 3. Dietary Preference Systems

**How Top Apps Handle Diet Filters:**

| App | Diet Options | Filter Behavior |
|-----|--------------|-----------------|
| Yummly | 15+ diets | Exclusive filtering |
| MyFitnessPal | 10+ diets | Suggestion-based |
| Lifesum | 8 plans | Complete meal plans |
| Noom | None | Psychology-based |

**SweetSwap Approach:**
- **4 diet options only**: Animal-based, Carnivore, Paleo, Keto
- **Strict filtering**: AI prompt enforces diet, no "close enough"
- **Educational content**: Explain why each alternative fits the diet

**Diet Enforcement in AI Prompts:**
```
Animal-based: Prioritize raw dairy, honey, fruit, eggs, organ meats
Carnivore: Meat, fish, eggs, limited dairy only - NO plant ingredients
Paleo: Whole foods, no grains/legumes/dairy/refined sugar
Keto: High fat, <20g net carbs, moderate protein
```

---

### 4. Caching & Offline Access

**Industry Standards:**

| Feature | Implementation |
|---------|----------------|
| Recent recipes | Cache last 50 viewed |
| Saved recipes | Full offline access |
| Images | Aggressive disk cache |
| Pending actions | Queue syncs for when online |

**Technical Implementation:**

```typescript
// Offline-first save pattern
const saveRecipe = async (recipe: Recipe) => {
  // 1. Optimistic UI update
  setSavedRecipes(prev => [...prev, recipe]);
  
  // 2. Persist to local storage
  await AsyncStorage.setItem(
    `recipe_${recipe.id}`,
    JSON.stringify(recipe)
  );
  
  // 3. Sync to Convex (will retry if offline)
  await convex.mutation(api.recipes.save, { recipeId: recipe.id });
};
```

---

### 5. Engagement & Retention Mechanics

**What Drives Daily Opens:**

| Mechanic | Effectiveness | SweetSwap Feasibility |
|----------|--------------|----------------------|
| Push notifications | High | "New craving ideas based on your saves" |
| Streak counters | Medium | "5-day healthy swap streak!" |
| Daily challenges | Medium | "Today's craving: Beat the Brownie" |
| Social features | Low | Skip for v1 |
| Achievement badges | Medium | "Saved 10 chocolate alternatives" |
| Progress visualization | High | "Cravings conquered this week" |

**Recommended v1 Gamification:**
1. **Satisfying save animation**: Heart burst, confetti for milestones
2. **Simple counter**: "X healthy swaps made"
3. **Streak indicator**: Days in a row using app
4. **No guilt mechanics**: Never punish for not using

---

### 6. Export & Sharing

**Standard Patterns:**

| Format | Use Case | Priority |
|--------|----------|----------|
| Plain text | Quick share via any app | P1 |
| Recipe card image | Instagram/Pinterest | P2 |
| PDF | Print for cooking | P3 |
| Deep link | Share to other SweetSwap users | P2 |

**Share Flow:**
```
Tap "Share" on recipe → 
Bottom sheet: [Copy Text] [Share Image] [Share Link]
```

---

### 7. User Profile & Settings

**Essential Settings:**

| Setting | Priority |
|---------|----------|
| Diet preference (change) | P0 |
| Account email/password | P0 |
| Notification preferences | P1 |
| Clear saved recipes | P2 |
| App theme (dark/light) | P2 |
| Delete account | P1 (legal requirement) |

---

## Feature Prioritization Matrix

### v1 Features (MVP)

| Category | Features |
|----------|----------|
| Onboarding | Diet selection, skip-to-swipe |
| Input | 7 craving categories + free text |
| Generation | AI recipes via Gemini |
| Swipe | Left skip, right save |
| Recipe View | Full recipe on card tap |
| Saved | List of saved recipes |
| Auth | Email/password |
| Profile | Diet preference change |

### v2 Features

| Category | Features |
|----------|----------|
| Images | AI-generated food images |
| Sharing | Text + image export |
| Gamification | Streaks, counters |
| Search | Search saved recipes |
| Offline | Full offline saved recipes |
| Notifications | Craving suggestions |

### Out of Scope

- Meal planning
- Shopping lists
- Social features
- Nutrition tracking
- Cooking timers
- Recipe scaling
- Multiple diet profiles

---

## Competitive Insights

### What Makes Users Love Recipe Apps

1. **Speed to value**: First useful content < 30 seconds
2. **Visual appeal**: Beautiful food photography
3. **Simplicity**: One tap to save, one tap to view
4. **Reliability**: Recipes that actually work
5. **Personalization**: Remembers my preferences

### What Makes Users Abandon Recipe Apps

1. **Too many questions** before showing content
2. **Aggressive upselling** before value proven
3. **Slow loading** or clunky animations
4. **Generic content** that ignores preferences
5. **Poor recipe quality** (AI hallucinations a risk)

---

## Recommendations for SweetSwap

1. **Time-to-first-card under 30 seconds** from install
2. **Diet selection = only required onboarding question**
3. **Right swipe must feel delightful** (spring physics, haptics, visual feedback)
4. **Recipe quality > recipe quantity** - Better to generate 3 great options than 10 mediocre
5. **v1 gamification: Counter + save animation only** - Don't over-engineer
6. **Sharing as text is sufficient for v1** - Image export is v2
