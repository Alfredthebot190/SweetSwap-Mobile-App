# SweetSwap Requirements

## Overview

This document defines all requirements for SweetSwap, categorized by feature area and scoped for v1 (MVP), v2, or out of scope.

**Legend:**
- 🟢 **v1** — Launch MVP requirement
- 🟡 **v2** — Post-launch enhancement
- ⚫ **OOS** — Out of scope

---

## 1. Onboarding

| REQ-ID | Requirement | Scope | Notes |
|--------|-------------|-------|-------|
| ONB-001 | Diet selection screen (Animal-based, Carnivore, Paleo, Keto) | 🟢 v1 | Single tap selection |
| ONB-002 | Skip onboarding option | 🟢 v1 | Default to Paleo |
| ONB-003 | Welcome/value prop screen | 🟢 v1 | Single screen, < 5s read |
| ONB-004 | Taste preference quiz | 🟡 v2 | Improve AI accuracy |
| ONB-005 | Allergy/restriction input | 🟡 v2 | Additional filtering |
| ONB-006 | Tutorial/walkthrough overlay | 🟡 v2 | First-time swipe hints |

---

## 2. Craving Input

| REQ-ID | Requirement | Scope | Notes |
|--------|-------------|-------|-------|
| CRV-001 | Display 7 craving categories | 🟢 v1 | Chocolate, Ice Cream, Cookies, Cake, Candy, Creamy, Fruity |
| CRV-002 | Tap category to start swipe session | 🟢 v1 | Immediate navigation |
| CRV-003 | Free text craving input | 🟢 v1 | "I want something like Snickers" |
| CRV-004 | Recent/popular cravings display | 🟡 v2 | Quick access |
| CRV-005 | Category icons/illustrations | 🟢 v1 | Visual appeal |
| CRV-006 | Category-specific color themes | 🟡 v2 | Enhanced UX |

---

## 3. Recipe Generation

| REQ-ID | Requirement | Scope | Notes |
|--------|-------------|-------|-------|
| GEN-001 | Generate recipes via Gemini 2.5 Flash | 🟢 v1 | OpenRouter integration |
| GEN-002 | Batch generation (5 recipes per request) | 🟢 v1 | Performance optimization |
| GEN-003 | Structured JSON output (Zod validation) | 🟢 v1 | Prevents hallucination |
| GEN-004 | Diet-specific prompt engineering | 🟢 v1 | Strict compliance |
| GEN-005 | Recipe includes: title, description, ingredients, instructions, prep time, health benefits | 🟢 v1 | Core data model |
| GEN-006 | Exclusion context for diversity | 🟢 v1 | Avoid repetition |
| GEN-007 | Fallback recipe library (20+ per diet) | 🟢 v1 | Emergency fallback |
| GEN-008 | Generation retry with backoff | 🟢 v1 | Error handling |
| GEN-009 | Real-time generation status indicator | 🟢 v1 | Skeleton cards |

---

## 4. Image Generation

| REQ-ID | Requirement | Scope | Notes |
|--------|-------------|-------|-------|
| IMG-001 | AI-generated food images per recipe | 🟡 v2 | Nano Banana Pro |
| IMG-002 | Food photography style prompt | 🟡 v2 | Premium aesthetic |
| IMG-003 | Image caching (expo-image) | 🟢 v1 | Memory + disk |
| IMG-004 | Category placeholder images | 🟢 v1 | Fallback for v1 |
| IMG-005 | Image quality validation | 🟡 v2 | Reject bad generations |
| IMG-006 | Lazy image loading with blur-up | 🟢 v1 | Perceived performance |

---

## 5. Swipe Interface

| REQ-ID | Requirement | Scope | Notes |
|--------|-------------|-------|-------|
| SWP-001 | Swipeable card stack (3+ visible) | 🟢 v1 | Tinder-style |
| SWP-002 | Left swipe = skip | 🟢 v1 | Core mechanic |
| SWP-003 | Right swipe = save | 🟢 v1 | Core mechanic |
| SWP-004 | Spring physics animation | 🟢 v1 | react-native-reanimated |
| SWP-005 | Rotation on drag | 🟢 v1 | Natural feel |
| SWP-006 | Like/skip overlay indicators | 🟢 v1 | Visual feedback |
| SWP-007 | Save animation (heart burst) | 🟢 v1 | Satisfying feedback |
| SWP-008 | Undo last swipe (swipe-left only) | 🟢 v1 | Error recovery |
| SWP-009 | Haptic feedback on swipe | 🟢 v1 | Tactile response |
| SWP-010 | Card tap = view full recipe | 🟢 v1 | Navigation |
| SWP-011 | Swipe velocity detection | 🟢 v1 | Quick vs slow swipe |
| SWP-012 | Queue refill at 2 cards remaining | 🟢 v1 | Seamless experience |

---

## 6. Recipe View

| REQ-ID | Requirement | Scope | Notes |
|--------|-------------|-------|-------|
| RCV-001 | Full-screen recipe modal | 🟢 v1 | On card tap |
| RCV-002 | Recipe title + description | 🟢 v1 | Hero section |
| RCV-003 | Ingredient list (formatted) | 🟢 v1 | Amount, unit, item |
| RCV-004 | Step-by-step instructions | 🟢 v1 | Numbered list |
| RCV-005 | Prep time display | 🟢 v1 | Quick info |
| RCV-006 | Health benefits section | 🟢 v1 | Educational content |
| RCV-007 | Save button from modal | 🟢 v1 | Secondary save path |
| RCV-008 | Share button | 🟡 v2 | Export feature |
| RCV-009 | Nutrition info (estimated) | 🟡 v2 | Disclaimer required |
| RCV-010 | Ingredient substitutions | 🟡 v2 | AI-generated |
| RCV-011 | Recipe scaling | ⚫ OOS | Complexity |

---

## 7. Saved Recipes

| REQ-ID | Requirement | Scope | Notes |
|--------|-------------|-------|-------|
| SAV-001 | Saved recipes tab/screen | 🟢 v1 | Tab navigation |
| SAV-002 | Recipe card list view | 🟢 v1 | Scrollable |
| SAV-003 | Tap to view full recipe | 🟢 v1 | Same modal as swipe |
| SAV-004 | Delete saved recipe (swipe or button) | 🟢 v1 | Management |
| SAV-005 | Empty state with CTA | 🟢 v1 | Guide to swipe screen |
| SAV-006 | Search saved recipes | 🟡 v2 | Find specific recipe |
| SAV-007 | Filter by category | 🟡 v2 | Organization |
| SAV-008 | Offline access to saved | 🟡 v2 | AsyncStorage cache |
| SAV-009 | Sort by date saved | 🟢 v1 | Default order |

---

## 8. Export & Sharing

| REQ-ID | Requirement | Scope | Notes |
|--------|-------------|-------|-------|
| EXP-001 | Copy recipe as plain text | 🟡 v2 | Share anywhere |
| EXP-002 | Share recipe card as image | 🟡 v2 | Instagram/Pinterest |
| EXP-003 | Deep link to recipe | 🟡 v2 | App user sharing |
| EXP-004 | PDF export | ⚫ OOS | Complexity |

---

## 9. Authentication

| REQ-ID | Requirement | Scope | Notes |
|--------|-------------|-------|-------|
| AUTH-001 | Email/password signup | 🟢 v1 | Convex Auth |
| AUTH-002 | Email/password login | 🟢 v1 | Convex Auth |
| AUTH-003 | Password reset flow | 🟢 v1 | Email-based |
| AUTH-004 | Logout functionality | 🟢 v1 | Clear session |
| AUTH-005 | Google Sign-In | 🟡 v2 | OAuth |
| AUTH-006 | Apple Sign-In | 🟡 v2 | Required for iOS if OAuth offered |
| AUTH-007 | Delete account | 🟢 v1 | Legal requirement |

---

## 10. User Profile

| REQ-ID | Requirement | Scope | Notes |
|--------|-------------|-------|-------|
| PRF-001 | Profile/settings tab | 🟢 v1 | Tab navigation |
| PRF-002 | Change diet preference | 🟢 v1 | Re-onboarding |
| PRF-003 | View account email | 🟢 v1 | Basic info |
| PRF-004 | Change password | 🟢 v1 | Security |
| PRF-005 | Dark/light theme toggle | 🟡 v2 | Preference |
| PRF-006 | Notification preferences | 🟡 v2 | When implemented |
| PRF-007 | Clear all saved recipes | 🟡 v2 | Reset option |

---

## 11. Gamification & Engagement

| REQ-ID | Requirement | Scope | Notes |
|--------|-------------|-------|-------|
| GAM-001 | Save animation (satisfying feedback) | 🟢 v1 | Already in SWP-007 |
| GAM-002 | "Healthy swaps" counter | 🟡 v2 | Cumulative total |
| GAM-003 | Daily streak indicator | 🟡 v2 | Engagement |
| GAM-004 | Milestone celebrations (10, 50, 100) | 🟡 v2 | Achievement moments |
| GAM-005 | Push notifications | 🟡 v2 | Re-engagement |

---

## 12. Technical Requirements

| REQ-ID | Requirement | Scope | Notes |
|--------|-------------|-------|-------|
| TEC-001 | 60fps swipe animations | 🟢 v1 | Reanimated worklets |
| TEC-002 | < 2s time to first card | 🟢 v1 | Performance target |
| TEC-003 | < 3s AI generation time | 🟢 v1 | UX threshold |
| TEC-004 | Offline detection + graceful degradation | 🟢 v1 | Error handling |
| TEC-005 | Memory management (100+ swipes) | 🟢 v1 | No leaks |
| TEC-006 | Error boundaries around AI components | 🟢 v1 | Fault isolation |
| TEC-007 | TypeScript strict mode | 🟢 v1 | Code quality |
| TEC-008 | Expo SDK 52+ with New Architecture | 🟢 v1 | Performance |

---

## Summary

| Scope | Count |
|-------|-------|
| 🟢 v1 | 56 requirements |
| 🟡 v2 | 28 requirements |
| ⚫ OOS | 2 requirements |

---

## Traceability

All v1 requirements map to phases in ROADMAP.md.
