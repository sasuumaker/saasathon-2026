![Next.js](https://img.shields.io/badge/Next.js-16-black?logo=next.js)
![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-5-blue?logo=typescript&logoColor=white)
![Tailwind](https://img.shields.io/badge/Tailwind-4-38BDF8?logo=tailwindcss&logoColor=white)
![Supabase](https://img.shields.io/badge/Supabase-PostgreSQL-3ECF8E?logo=supabase&logoColor=white)
![Claude](https://img.shields.io/badge/Claude-Sonnet%20%2B%20Haiku-D97757?logo=anthropic&logoColor=white)
[![Vercel](https://img.shields.io/badge/Vercel-Deployed-black?logo=vercel)](https://saasathon-2026.vercel.app)

# ShopMate

> This is my fork of a hackathon team project. See [My Contributions](#my-contributions) below for what I built.

Cook any cuisine with confidence in New Zealand. Search recipes from around the world, find ingredients at local supermarkets, compare prices across stores, and get smart substitutions for hard-to-find items.

**Live**: https://saasathon-2026.vercel.app  
**Original repo**: https://github.com/takahiro-okada/saasathon-2026

## My Contributions

This app was built at SaaSathon 2026 (hackathon). I was responsible for **the entire application development** -- from architecture design to frontend/backend implementation and UI design. The team collaborated on ideation and product direction.

### What I built (53 of 87 commits, 61%)

- **Full-stack architecture** -- Designed and implemented the Next.js App Router structure, API routes, database schema, and Supabase integration from scratch
- **AI-powered recipe engine** -- Built the recipe generation pipeline using Claude Sonnet for structured JSON output, with DB caching for instant repeat lookups
- **Supermarket price scraping** -- Implemented real-time product scrapers for Woolworths, Pak'nSave, and New World APIs, with 24h TTL caching
- **AI ingredient substitution** -- Claude Haiku suggests NZ-available alternatives when Japanese ingredients are not found, then searches substitutes in supermarket APIs
- **Cross-store price comparison** -- Side-by-side ingredient pricing across 3 stores with cheapest-store recommendations
- **Multilingual support (i18n)** -- Full EN/JA/ZH internationalization with locale-aware recipe name display
- **UI/UX design** -- Designed the entire interface with a warm sage green / cream NZ-inspired palette, real store logos, onboarding tutorial, and responsive mobile-first layout
- **Component architecture** -- Refactored from a monolithic page into modular components (15+ files), types, constants, and utility modules
- **CI/CD pipeline** -- Jest test suites (i18n + scraper), GitHub Actions (CI, CodeQL, Lighthouse, PR-title lint, bundle-size tracking, Dependabot)

### Tech decisions I made

| Decision | Reasoning |
|----------|-----------|
| Claude Sonnet for recipe generation | Structured JSON output quality was critical; Haiku wasn't reliable enough for complex recipe schemas |
| Cache-first architecture | First search hits AI + scraper (slow); subsequent searches are instant from Supabase |
| Real scraper APIs over mock data | Hackathon judges value real data; invested time in reverse-engineering supermarket APIs |
| Tailwind CSS 4 with CSS custom properties | Design tokens as CSS variables for consistent theming without a component library |
| Component extraction post-MVP | Built fast in one file first, then refactored for maintainability after core features were stable |

## Features

- **AI Recipe Generation** -- Type any dish name (English, Japanese, or Chinese) and get a full recipe with NZ supermarket ingredients
- **Real-time Price Lookup** -- Live pricing from Woolworths, Pak'nSave, and New World
- **Cross-store Price Comparison** -- See which supermarket is cheapest for your recipe
- **Smart Substitutions** -- AI suggests alternatives when ingredients aren't available
- **Recipe Caching** -- First search generates via AI; subsequent searches are instant from DB
- **Multilingual** -- Full support for English, Japanese, and Chinese

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | Next.js 16 (App Router) |
| Language | TypeScript |
| Styling | Tailwind CSS 4 |
| Database | Supabase (PostgreSQL) |
| AI (Recipe) | Claude Sonnet |
| AI (Chat/Substitution) | Claude Haiku |
| Deployment | Vercel |

## Project Structure

```
.
├── app/                        # Next.js App Router
│   ├── api/                    # API Routes
│   │   ├── recipes/
│   │   │   ├── search/         # Recipe search + AI generation + DB cache
│   │   │   ├── compare/        # Cross-store price comparison
│   │   │   └── suggestions/    # Popular recipe suggestions
│   │   ├── chat/               # AI chat assistant
│   │   ├── substitution/       # AI ingredient substitution
│   │   ├── recommendations/    # AI recipe recommendations
│   │   ├── activity/           # User activity logging
│   │   ├── ingredients/        # Cross-store ingredient lookup
│   │   ├── products/           # Product search
│   │   └── stores/             # Nearby store search
│   ├── lib/                    # Server-side utilities
│   │   ├── supabase.ts         # Supabase client
│   │   ├── scraper.ts          # Supermarket API scrapers
│   │   ├── recipes.ts          # Recipe types + preset data
│   │   └── i18n.ts             # Internationalization
│   ├── layout.tsx
│   └── page.tsx                # Home page (imports from components/)
│
├── components/                 # UI Components
│   ├── recipe-result.tsx       # Recipe card with ingredients + steps
│   ├── ingredient-card.tsx     # Single ingredient with price/stock/alternatives
│   ├── price-compare-panel.tsx # Cross-store price comparison table
│   ├── store-tabs.tsx          # Woolworths / Pak'nSave / New World selector
│   ├── badges.tsx              # PriceBadge, StockBadge, StoreBadge
│   ├── ai-chat-panel.tsx       # Floating AI chat assistant
│   ├── welcome-page.tsx        # First-time user welcome screen
│   ├── onboarding-overlay.tsx  # Step-by-step onboarding tour
│   ├── bottom-nav.tsx          # Bottom navigation bar
│   ├── language-toggle.tsx     # EN / JA / ZH language switcher
│   ├── icons.tsx               # SVG icons and decorative elements
│   ├── nearby-stores-panel.tsx # Nearby supermarket finder
│   └── ai-insights-panel.tsx   # AI shopping insights
│
├── types/                      # TypeScript type definitions
│   └── index.ts                # Shared interfaces (Recipe, Ingredient, Store, etc.)
│
├── constants/                  # App constants
│   ├── stores.ts               # Store labels, colors, logos
│   └── onboarding.ts           # Onboarding step definitions
│
├── lib/                        # Client-side utilities
│   └── activity.ts             # User ID + activity logging
│
└── public/
    └── logos/                  # Store logo SVGs
```

## Architecture

```
User Input (any dish name, any language)
  |
  v
DB Cache Check (recipes + recipe_ingredients + ingredients)
  |-- HIT --> Return instantly
  |-- MISS --v
             Claude Sonnet generates recipe JSON
               |
               v
             Save to DB (async, non-blocking)
               |
               v
             Match ingredients against DB
               |
               v
             Supermarket API for live prices
               |-- Not found --> Claude Haiku suggests alternatives
               |                   |
               |                   v
               |                 Search alternatives in supermarket
               |
               v
             Cache in store_products (24h TTL)
               |
               v
             Return to frontend (prices, stock, alternatives)
```

## Getting Started

```bash
pnpm install
cp .env.local.example .env.local  # Add your API keys
pnpm dev
```

### Environment Variables

| Variable | Description |
|----------|------------|
| `NEXT_PUBLIC_SUPABASE_URL` | Supabase project URL |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Supabase anonymous key |
| `ANTHROPIC_API_KEY` | Anthropic API key for Claude |

## AI Models

| Route | Model | Purpose |
|-------|-------|---------|
| `/api/recipes/search` (generation) | Claude Sonnet | Recipe JSON generation |
| `/api/recipes/search` (alternatives) | Claude Haiku | Substitute ingredient suggestions |
| `/api/chat` | Claude Haiku | Chat assistant |
| `/api/substitution` | Claude Haiku | Ingredient substitution |
| `/api/recommendations` | Claude Haiku | Recipe recommendations |
