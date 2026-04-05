# Click — Mobile-First Social Events App

## What This Is
A community-centric social platform bridging algorithm-driven matching ("Clicks") with real-world event experiences. Users get matched, message, attend events, and build community. Native-feel PWA built with Next.js.

## Design Vibe — IMPORTANT
This app targets 20-30 year olds. It MUST feel young, colorful, interactive, and alive — not corporate. Micro-animations on everything, bold purple palette, mobile-native feel. See @docs/design-system.md for full design philosophy.

## Tech Stack
- **Framework:** Next.js 15 (App Router) with TypeScript strict mode
- **Styling:** Tailwind CSS 4 + CSS variables for design tokens
- **Backend:** Supabase (Auth, Database, Realtime, Storage)
- **Chat:** Stream Chat (GetStream.io) for DMs and event group chats
- **Payments:** PayPal SDK for event ticket purchases + membership subscriptions
- **State:** Zustand for client state
- **OTP Auth:** Supabase phone auth (Twilio under the hood)
- **Deployment:** Vercel

## Key Commands
```bash
npm run dev          # Start dev server
npm run build        # Production build
npm run lint         # ESLint
npm run typecheck    # tsc --noEmit
npm run db:migrate   # Run Supabase migrations
npm run db:types     # Generate types from Supabase schema
npm run db:seed      # Seed dev data
npm run test         # Vitest
npm run test:e2e     # Playwright
```

## Code Rules — MUST FOLLOW
- MUST use TypeScript strict mode everywhere. NEVER use `any` type.
- MUST use ES modules (import/export), NEVER CommonJS (require).
- MUST use functional components with hooks. NEVER class components.
- MUST run `npm run typecheck && npm run lint` after any code changes.
- MUST NEVER commit secrets or .env values. All secrets go in `.env.local`.
- MUST use the design tokens defined in `docs/design-system.md` — NEVER hardcode colors.
- MUST use Supabase Row Level Security (RLS) on every table. NEVER bypass it.
- MUST handle loading, error, and empty states for every data-fetching component.
- MUST make all UI mobile-first (375px base), responsive up to desktop.
- Prefer server components. Use 'use client' only when interactivity is needed.
- Prefer Supabase Realtime subscriptions over polling.
- Write JSDoc on all exported functions.

## Environment Variables
All 3rd-party service config goes in `.env.local`. See `.env.example` for the full list.
NEVER put values in `.env.example` — it's a template only.

## Full Spec & Context
@docs/product-spec.md        # Complete product design specification
@docs/design-system.md       # Color tokens, typography, components
@docs/data-model.md          # Database schema and relationships
@docs/user-flows.md          # Screen-by-screen flows and states
@docs/api-contracts.md       # API route contracts
