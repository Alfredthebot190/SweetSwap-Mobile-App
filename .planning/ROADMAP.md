# SweetSwap Roadmap

## Overview

9-phase implementation plan for SweetSwap v1 MVP. Each phase builds incrementally toward a fully functional swipe-based healthy recipe discovery app.

---

## Phase 1: Foundation

**Goal:** Project setup, Convex integration, authentication flow

### Deliverables

| Task | REQ-IDs |
|------|---------|
| Initialize Expo SDK 52+ with TypeScript | TEC-008 |
| Configure Tamagui with custom tokens | TEC-001 |
| Setup Expo Router navigation (tabs) | - |
| Initialize Convex Cloud project | TEC-008 |
| Define database schema (users, recipes, swipes) | - |
| Implement Convex Auth (email/password) | AUTH-001, AUTH-002 |
| Create login/signup screens | AUTH-001, AUTH-002 |
| Password reset flow | AUTH-003 |
| Logout functionality | AUTH-004 |
| Tab navigator (Swipe, Saved, Profile) | SAV-001, PRF-001 |

### Success Criteria
- [ ] App builds on iOS and Android
- [ ] User can sign up, login, logout
- [ ] Tab navigation works

---

## Phase 2: Craving Input

**Goal:** Category selection screen with beautiful UI

### Deliverables

| Task | REQ-IDs |
|------|---------|
| Create craving categories data | CRV-001 |
| Design category grid/list UI | CRV-001, CRV-005 |
| Category icons/illustrations | CRV-005 |
| Tap category → navigate to swipe screen | CRV-002 |
| Free text input field | CRV-003 |
| Submit free text → navigate to swipe | CRV-003 |

### Success Criteria
- [ ] All 7 categories displayed with icons
- [ ] Free text input works
- [ ] Navigation triggers correctly

---

## Phase 3: AI Recipe Generation

**Goal:** Gemini 2.5 Flash integration for recipe generation

### Deliverables

| Task | REQ-IDs |
|------|---------|
| OpenRouter API integration | GEN-001 |
| Convex action for generateRecipe | GEN-001 |
| Diet-specific prompt engineering | GEN-004 |
| Structured JSON output schema (Zod) | GEN-003 |
| Batch generation (5 recipes) | GEN-002 |
| Recipe data model validation | GEN-005 |
| Retry with exponential backoff | GEN-008 |
| Exclusion context for diversity | GEN-006 |
| Seed fallback recipes (20 per diet) | GEN-007 |
| Skeleton loading state | GEN-009 |

### Success Criteria
- [ ] AI generates valid recipes
- [ ] Diet compliance is strict
- [ ] Fallback works when AI fails

---

## Phase 4: Swipe Interface

**Goal:** Tinder-style card stack with gestures and animations

### Deliverables

| Task | REQ-IDs |
|------|---------|
| SwipeCard component | SWP-001 |
| CardStack manager | SWP-001 |
| Pan gesture handler | SWP-002, SWP-003 |
| Spring physics animation | SWP-004 |
| Rotation on drag | SWP-005 |
| Like/skip overlay indicators | SWP-006 |
| Save animation (heart burst) | SWP-007 |
| Swipe velocity detection | SWP-011 |
| Haptic feedback | SWP-009 |
| Zustand card queue store | TEC-002 |
| Queue refill logic (at 2 remaining) | SWP-012 |
| Undo last swipe | SWP-008 |

### Success Criteria
- [ ] 60fps swipe animations
- [ ] Cards refill seamlessly
- [ ] Undo works for left-swipes

---

## Phase 5: Recipe View

**Goal:** Full recipe display modal

### Deliverables

| Task | REQ-IDs |
|------|---------|
| Recipe modal route/screen | RCV-001 |
| Hero section (title, description) | RCV-002 |
| Ingredient list component | RCV-003 |
| Instructions component | RCV-004 |
| Prep time display | RCV-005 |
| Health benefits section | RCV-006 |
| Save button (from modal) | RCV-007 |
| Card tap → modal navigation | SWP-010 |

### Success Criteria
- [ ] Full recipe displays correctly
- [ ] Save works from modal
- [ ] Back navigation returns to stack

---

## Phase 6: Saved Recipes

**Goal:** List view of saved recipes with management

### Deliverables

| Task | REQ-IDs |
|------|---------|
| Saved recipes Convex query | SAV-001 |
| Recipe card list component | SAV-002 |
| Tap to view full recipe | SAV-003 |
| Delete recipe (swipe-to-delete or button) | SAV-004 |
| Empty state with CTA | SAV-005 |
| Sort by date saved | SAV-009 |
| Optimistic delete with rollback | TEC-006 |

### Success Criteria
- [ ] Saved recipes display in order
- [ ] Delete works with undo
- [ ] Empty state guides to swipe

---

## Phase 7: Image Pipeline

**Goal:** Category placeholder images for v1, AI image prep

### Deliverables

| Task | REQ-IDs |
|------|---------|
| Create/source category placeholder images | IMG-004 |
| expo-image setup with caching | IMG-003 |
| Blur-up lazy loading | IMG-006 |
| Image component for cards | IMG-003 |
| (v2 prep) Nano Banana Pro integration stub | IMG-001 |

### Success Criteria
- [ ] All cards show appropriate images
- [ ] Images cached properly
- [ ] No memory issues after 100 swipes

---

## Phase 8: Onboarding & Profile

**Goal:** Diet selection onboarding, profile settings

### Deliverables

| Task | REQ-IDs |
|------|---------|
| Welcome screen (value prop) | ONB-003 |
| Diet selection screen | ONB-001 |
| Skip onboarding option | ONB-002 |
| Store diet preference in Convex | ONB-001 |
| Profile screen layout | PRF-001 |
| Change diet preference | PRF-002 |
| View account email | PRF-003 |
| Change password | PRF-004 |
| Delete account | AUTH-007 |

### Success Criteria
- [ ] New users see onboarding
- [ ] Diet selection persists
- [ ] Profile settings work

---

## Phase 9: Polish & QA

**Goal:** Performance optimization, error handling, final polish

### Deliverables

| Task | REQ-IDs |
|------|---------|
| Error boundaries around AI components | TEC-006 |
| Offline detection + graceful degradation | TEC-004 |
| Memory profiling (100+ swipes) | TEC-005 |
| TypeScript strict mode enforcement | TEC-007 |
| Performance profiling (time to first card) | TEC-002 |
| AI response time optimization | TEC-003 |
| UI polish pass (spacing, colors) | - |
| Accessibility review | - |
| Cross-platform testing (iOS + Android) | - |

### Success Criteria
- [ ] < 2s to first card
- [ ] 60fps throughout
- [ ] No crashes or hangs
- [ ] Works offline gracefully

---

## Timeline Estimate

| Phase | Estimated Effort |
|-------|-----------------|
| Phase 1: Foundation | 2-3 days |
| Phase 2: Craving Input | 1 day |
| Phase 3: AI Generation | 2 days |
| Phase 4: Swipe Interface | 3-4 days |
| Phase 5: Recipe View | 1 day |
| Phase 6: Saved Recipes | 1 day |
| Phase 7: Image Pipeline | 1 day |
| Phase 8: Onboarding & Profile | 2 days |
| Phase 9: Polish & QA | 2-3 days |
| **Total** | **15-18 days** |

---

## Dependency Graph

```
Phase 1 (Foundation)
    │
    ├──► Phase 2 (Craving Input)
    │         │
    │         └──► Phase 3 (AI Generation)
    │                   │
    │                   └──► Phase 4 (Swipe Interface)
    │                             │
    │                             ├──► Phase 5 (Recipe View)
    │                             │
    │                             └──► Phase 6 (Saved Recipes)
    │
    └──► Phase 7 (Image Pipeline) ─── can run parallel to 4-6
    │
    └──► Phase 8 (Onboarding) ─── after auth works
              │
              └──► Phase 9 (Polish) ─── final pass
```

---

## Next Action

Start with Phase 1:
```
/gsd:discuss-phase 1
```
