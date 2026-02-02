# Phase 3: AI Recipe Generation - Implementation Plans

## Overview
Integrate Gemini 2.5 Flash via OpenRouter to generate healthy recipe alternatives with structured output.

---

## Plan 1: OpenRouter Integration & Convex Action

<task type="auto">
  <name>Create OpenRouter integration with Convex action</name>
  <files>
    convex/generateRecipe.ts
    convex/schema.ts
  </files>
  <action>
    Create Convex action for recipe generation:
    1. Set up OpenRouter API client (add OPENROUTER_API_KEY to Convex env)
    2. Create generateRecipe function that:
       - Takes craving input (category or free text) + diet preference
       - Constructs diet-specific prompt for Gemini 2.5 Flash
       - Sends request to OpenRouter API
       - Returns parsed recipe data

    3. Define recipe schema in convex/schema.ts:
       - id: v.id('recipes')
       - title: v.string()
       - description: v.string()
       - ingredients: v.array(v.object({ amount: v.string(), unit: v.string(), item: v.string() }))
       - instructions: v.array(v.string())
       - prepTime: v.string()
       - healthBenefits: v.string()
       - category: v.string()
       - diet: v.union(v.literal('animal'), v.literal('carnivore'), v.literal('paleo'), v.literal('keto'))
       - createdAt: v.number()

    Prompt template should include:
    - Craving description
    - Diet constraints
    - Output format specification (JSON)
    - Quality guidelines (indulgent, not restrictive)
  </action>
  <verify>
    - generateRecipe function exists in Convex
    - Function accepts craving and diet parameters
    - Function returns recipe data matching schema
    - Convex schema includes recipes table with all required fields
  </verify>
  <done>
    OpenRouter + Convex integration ready for recipe generation
  </done>
</task>

---

## Plan 2: Zod Schema & Recipe Validation

<task type="auto">
  <name>Create Zod schema for recipe validation</name>
  <files>src/lib/schemas/recipeSchema.ts</files>
  <action>
    Create Zod schema for structured recipe output:
    1. Define Recipe Zod schema:
       - title: string (min 3 chars)
       - description: string (min 10 chars)
       - ingredients: array of objects (amount, unit, item)
       - instructions: array of strings (min 1 step)
       - prepTime: string
       - healthBenefits: string (min 10 chars)
       - imagePrompt: string (optional, for future image gen)

    2. Create validation function:
       - parseAndValidateRecipe(data: unknown): Recipe
       - Returns parsed recipe or throws ZodError
       - Includes detailed error messages

    3. Create recipe type from schema:
       export type Recipe = z.infer<typeof recipeSchema>;

    4. Create mock data validator:
       - validateMockRecipes(recipes: unknown[]): Recipe[]
       - Ensures fallback recipes meet schema

    Validation should catch:
    - Missing required fields
    - Invalid data types
    - Empty arrays (ingredients, instructions)
    - String length violations
  </action>
  <verify>
    - recipeSchema exports valid Zod schema
    - parseAndValidateRecipe function works
    - Recipe type is exported and used
    - Validation throws descriptive errors on invalid data
    - Mock data validator works
  </verify>
  <done>
    Recipe validation layer prevents hallucination issues
  </done>
</task>

---

## Plan 3: Batch Generation & Retry Logic

<task type="auto">
  <name>Implement batch generation with retry logic</name>
  <files>convex/batchGenerateRecipes.ts</files>
  <action>
    Create batch generation system:
    1. Create batchGenerateRecipes function that:
       - Takes craving, diet, count (default 5)
       - Generates recipes in parallel (Promise.all)
       - Includes exclusion context from previous generations
       - Implements retry logic with exponential backoff

    2. Retry logic:
       - Max 3 retries per recipe
       - Backoff: 1s, 2s, 4s delays
       - Zod validation after each attempt
       - Skip failed retries after max attempts

    3. Exclusion context:
       - Track recently shown recipes in user session
       - Pass IDs of previously shown recipes
       - Prompt AI to avoid similar recipes

    4. Fallback to seed recipes:
       - If batch fails, return from seed library
       - Seed library: 20+ recipes per diet
       - Stored in convex/seedRecipes.ts

    5. Error handling:
       - Log generation failures
       - Return partial results if some succeed
       - Never return empty array (use fallback)
  </action>
  <verify>
    - batchGenerateRecipes generates 5 recipes successfully
    - Retry logic works on API failures
    - Exclusion context prevents duplicate suggestions
    - Fallback recipes return when generation fails
    - Seed library has 20+ recipes per diet
  </verify>
  <done>
    Phase 3 complete: AI generates reliable, validated recipes
  </done>
</task>
