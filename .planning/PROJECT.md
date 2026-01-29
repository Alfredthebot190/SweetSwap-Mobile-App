# SweetSwap

## What This Is

SweetSwap is a React Native (Expo) mobile app that helps people with sweet tooth cravings find healthy dessert alternatives through a dating-app-style swipe interface powered by AI.

Users enter a dessert craving, get AI-generated healthy alternatives displayed as swipeable cards (Tinder-style), and can save or view full recipes for the ones they like.

## Core Value

Transform sugar cravings into healthy eating moments through an addictive swipe-based UX. Instead of reaching for processed junk, users get instant, personalized healthy alternatives that actually satisfy their craving.

## Who It's For

Health-conscious people who struggle with sugar cravings. Inspired by whole-foods/ancestral health philosophy (MAHA, Paul Saladino MD approach) — this philosophy informs the alternatives but is not mentioned explicitly in-app.

## Diet Options

- Animal-based
- Carnivore
- Paleo
- Keto

**No vegan/vegetarian options.** The app is built around whole-foods, animal-inclusive dietary frameworks.

## Craving Categories

7 preset categories plus free text input:

1. **Chocolate** — Brownies, truffles, chocolate cake, etc.
2. **Ice Cream & Frozen** — Ice cream, popsicles, frozen yogurt, etc.
3. **Cookies & Crunchy** — Cookies, granola bars, biscotti, etc.
4. **Cake & Pastry** — Cake, muffins, pastries, donuts, etc.
5. **Candy & Gummy** — Gummy bears, hard candy, caramels, etc.
6. **Creamy & Rich** — Pudding, mousse, cheesecake, custard, etc.
7. **Fruity & Sweet** — Fruit tarts, sorbets, fruit snacks, etc.

Plus: **Free text input** for specific cravings ("I want something like a Snickers bar")

## UX Flow

1. **Pick craving** — Select a category or type a specific craving
2. **AI generates** — Healthy alternatives are generated based on craving + diet preference
3. **Swipe left** — Skip this alternative
4. **Swipe right** — Save with animation (heart/checkmark)
5. **Tap card** — View full recipe (ingredients, instructions, nutrition info)
6. **Next card** — After action, next alternative appears

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | React Native (Expo) |
| Language | TypeScript |
| Backend | Convex Cloud (database, serverless functions, real-time) |
| Auth | Convex Auth (email/password) |
| Primary Styling | Tamagui |
| Fallback Styling | NativeWind + react-native-reusables |
| Recipe AI | Gemini 2.5 Flash via OpenRouter (free tier) |
| Image AI | Nano Banana Pro / Gemini 3 Pro Image |
| Navigation | Expo Router |

## APIs

- **Gemini 2.5 Flash** (via OpenRouter) — Recipe generation with structured output. Free tier available.
- **Nano Banana Pro / Gemini 3 Pro Image** — Food image generation for recipe cards.

## Auth Strategy

- **v1**: Email/password via Convex Auth
- **Deferred**: Google Sign-In, Apple Sign-In

## Monetization

Freemium model — details TBD. Build the full experience first, then determine paywall placement.

## Philosophy & Tone

- **Anti-processed food** messaging throughout
- **Educational content** about why alternatives are healthier (what's wrong with the original, what's better about this version)
- **Honest and encouraging** language — no guilt, no shaming, just better options
- Recipes should feel indulgent and satisfying, not restrictive

## Key Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| UI Framework | Tamagui over ShadCN | Better React Native compatibility, native feel |
| App Framework | Expo over bare RN CLI | Faster development, managed workflow, OTA updates |
| Backend | Convex Cloud | Real-time sync, built-in auth, serverless functions, single platform |
| Recipe AI | Gemini 2.5 Flash | Free via OpenRouter, good structured output, fast |
| Image AI | Nano Banana Pro | High-quality food images, reasonable pricing |
| Styling Fallback | NativeWind + reusables | Backup if Tamagui components don't cover all needs |

## Project Status

**Phase**: Initialization
**Current Milestone**: v1 MVP
