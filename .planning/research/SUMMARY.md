# Research Summary: SweetSwap Domain Analysis

## Executive Summary

This document synthesizes findings from stack, features, architecture, and pitfalls research to provide actionable recommendations for SweetSwap v1 development.

---

## Key Findings

### Technology Stack ✅ Validated

| Component | Recommendation | Confidence |
|-----------|---------------|------------|
| Framework | Expo SDK 52+ (New Architecture) | High |
| UI | Tamagui with custom tokens | High |
| Animation | react-native-reanimated 3.16+ | High |
| Gestures | react-native-gesture-handler 2.20+ | High |
| Backend | Convex Cloud | High |
| Auth | Convex Auth (email/password) | High |
| Recipe AI | Gemini 2.5 Flash via OpenRouter | Medium |
| Image AI | Nano Banana Pro | Medium |

### Critical Architecture Insights

1. **Card Queue Pattern**
   - Maintain 5 cards minimum in queue
   - Refill when 2 cards remaining
   - Pre-generate in batches of 5 (initial) → 3 (refill)

2. **State Split**
   - **Local (Zustand)**: cardQueue, animations, UI state
   - **Server (Convex)**: savedRecipes, userProfile, swipeHistory

3. **Optimistic UI**
   - Update local state immediately on swipe
   - Background sync to Convex
   - Rollback only on failure

### Critical Pitfalls to Avoid

| Priority | Pitfall | Mitigation |
|----------|---------|------------|
| P0 | AI hallucination (diet violations) | Strict schema + post-validation |
| P0 | Slow AI response (>3s) | Pre-fetch + skeleton cards |
| P0 | Empty card state | Fallback recipe library |
| P1 | Gesture conflicts | Zone isolation, disable back swipe |
| P1 | No undo functionality | Single-undo on swipe-left |
| P1 | Repetitive recipes | Exclusion context + temperature variance |

---

## Feature Prioritization

### v1 MVP (Launch Requirements)

| Category | Features |
|----------|----------|
| **Onboarding** | Diet selection (single screen) |
| **Craving Input** | 7 categories + free text |
| **AI Generation** | Gemini 2.5 Flash, structured JSON output |
| **Swipe Interface** | Left=skip, Right=save, spring physics |
| **Recipe View** | Full recipe modal on card tap |
| **Saved Recipes** | List view, tap to view full recipe |
| **Auth** | Email/password via Convex Auth |
| **Profile** | Change diet preference |

### v2 Features

- AI-generated food images (Nano Banana Pro)
- Share recipe as text/image
- Recipe search
- Gamification (streaks, counters)
- Push notifications
- Full offline support

### Out of Scope

- Meal planning, shopping lists, social features
- Nutrition tracking, cooking timers
- Multiple diet profiles, recipe scaling

---

## Implementation Recommendations

### Folder Structure
```
src/
├── app/              # Expo Router pages
├── components/       # UI components
├── stores/           # Zustand stores
├── convex/           # Backend schema + functions
├── lib/              # Utilities, prompts
└── tamagui.config.ts
```

### Performance Targets

| Metric | Target |
|--------|--------|
| Time to first card | < 2 seconds |
| Swipe animation FPS | 60 fps |
| AI generation time | < 3 seconds |
| Cold start | < 1.5 seconds |

### Risk Mitigations

1. **Seed 20+ fallback recipes** per diet type before launch
2. **Validate ALL AI output** against Zod schema before display
3. **Test gestures on both iOS and Android** (different gesture systems)
4. **Profile memory after 100+ swipes** during development
5. **Rate limit AI calls** to prevent cost runaway

---

## Next Steps

1. **Phase 1**: Project setup, Convex integration, Auth flow
2. **Phase 2**: Craving input UI with 7 categories
3. **Phase 3**: AI recipe generation pipeline
4. **Phase 4**: Swipe card interface with animations
5. **Phase 5**: Recipe detail view
6. **Phase 6**: Saved recipes list
7. **Phase 7**: Image generation pipeline
8. **Phase 8**: Onboarding flow refinement
9. **Phase 9**: Polish, gamification touches

---

## Research Artifacts

| File | Contents |
|------|----------|
| [STACK.md](file:///c:/Users/nivko/AG/SweetSwap-Mobile-App/.planning/research/STACK.md) | Tech stack validation, configs |
| [FEATURES.md](file:///c:/Users/nivko/AG/SweetSwap-Mobile-App/.planning/research/FEATURES.md) | Feature prioritization matrix |
| [ARCHITECTURE.md](file:///c:/Users/nivko/AG/SweetSwap-Mobile-App/.planning/research/ARCHITECTURE.md) | System design, queue patterns |
| [PITFALLS.md](file:///c:/Users/nivko/AG/SweetSwap-Mobile-App/.planning/research/PITFALLS.md) | Failure modes + prevention |
