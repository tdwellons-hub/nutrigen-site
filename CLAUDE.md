# NutriGen AI — Frontend Project Context

## What This Is
NutriGen AI is an AI-powered weekly meal planning web app for families.
Currently in prototype/investor preview stage.

## Live URLs
- Site: https://www.nutrigenfoods.com
- Password: NutriGen2026
- API: https://nutrigen-api.azurewebsites.net/api
- GitHub: https://github.com/tdwellons-hub/nutrigen-site

## Tech Stack
- Pure HTML/CSS/JavaScript (single index.html file)
- No framework, no build step
- Azure Static Web Apps hosting
- GitHub Actions auto-deploy on push to main

## Deployment
ALWAYS use git push — NEVER use swa deploy:
  cd ~/nutrigen-site
  git add .
  git commit -m "describe change"
  git push
GitHub Actions deploys automatically in ~1 minute.

## File Structure
  index.html          — entire frontend application
  password.html       — investor access gate
  staticwebapp.config.json — Azure routing config
  .github/workflows/  — GitHub Actions CI/CD
  CLAUDE.md           — this file

## Authentication
Simple sessionStorage password gate:
  Password: NutriGen2026
  Stored in: sessionStorage key 'nutrigen_auth'
  Gate file: password.html
  Check in: index.html <head> script tag

## API Connection
  const API_BASE_URL = 'https://nutrigen-api.azurewebsites.net/api';
  Function: apiFetch(table, params)
  Endpoints: /recipes, /recipe_ingredients, /recipe_steps

## Application Flow
  Page 1 (welcome)     → intro screen with stats
  Page 2 (preferences) → user selects cuisines, proteins,
                         allergies, dislikes, household size,
                         health goals, custom macros,
                         difficulty, diet
  Page 3 (plan)        → 7-day meal plan grid
  Page 4 (shopping)    → consolidated shopping list

## State Object
  state = {
    householdSize:  2,
    cuisines:       new Set(),
    proteins:       new Set(),
    allergies:      new Set(),
    dislikes:       [],
    diet:           'none',
    difficulties:   new Set(),   // multi-select
    healthGoals:    [],          // max 2
    customMacros:   { enabled, mode, protein_g, carbs_g, fat_g, calories },
    plan:           null,
    selectedSlots:  new Set(),   // selected meal slots on Page 3
    selectedStore:  null,
    shoppingList:   {},
  }

## Key Features
1. CUISINE FILTER — hard filter (not scoring) — only shows selected cuisines
2. DIFFICULTY — multi-select (Super Quick/Easy/Medium/Hard)
   - Auto-expands to adjacent levels if not enough recipes
   - Shows warning banner when expanding
3. HEALTH GOALS — up to 2 selectable
   - Labeling approach (not filtering)
   - Badges show on meal cards
   - Goals: diabetic, heart, low_sodium, kidney, weight_loss, muscle, gerd
4. CUSTOM MACROS — optional, 40/30/30 protein-focused default split
   - Auto-calculates calories from macros
   - Fills missing macros from calorie target
5. MEAL SELECTION on Page 3
   - Circle button on each card to select/keep
   - Reshuffle replaces only unselected meals
   - Shopping list uses only selected meals
6. DIFFICULTY BADGES on meal cards
7. HEALTH COMPLIANCE BADGES on meal cards
8. DAILY MACRO BAR at top of Page 3
9. PLATING NOTES in recipe modal
10. NUTRITION PANEL in recipe modal

## Ingredient Normalization
normalizeIngredientName() function handles:
  - British → American English spellings
  - Common duplicates (flour → all-purpose flour)
  - Taco seasoning variants
  - Quantity text stripping from names

## Scoring vs Filtering
  HARD FILTERS (must pass):  allergens, dislikes, diet, cuisines, difficulty
  SCORING (weighted):        proteins, mediterranean diet boost
  RANDOMIZATION:             Math.random() * 1.5 for variety

## CSS Variables
  --bg, --bg-elev, --ink, --ink-soft, --ink-muted
  --line, --line-strong
  --sage, --sage-deep
  --terracotta, --terracotta-soft
  --gold
  --cream-card
  --shadow-sm, --shadow-md, --shadow-lg
  --radius-sm, --radius-md, --radius-lg

## Known Issues / Watch Out For
- .git folder can disappear if sudo commands run in ~/nutrigen-site
- check-git.sh runs on login to auto-restore .git
- staticwebapp.config.json must exclude password.html from rewrite rule
- apiFetch() renamed from sbFetch() — no Supabase references should exist
- state object must be defined before renderChips() calls

## Sections Removed During Development
- Appetite breakdown (replaced by simple household size)
- Supabase integration (replaced by Azure Functions API)
- MealDB import (replaced by Spoonacular only)
- Macro backfill function (not needed — all imports include macros)

## Page 2 Preference Sections (in order)
  01 / Cuisines
  02 / Proteins
  03 / Allergies
  04 / Dislikes
  05 / Household
  08 / Health Goals
  09 / Custom Macros
  10 / Difficulty
  11 / Diet
