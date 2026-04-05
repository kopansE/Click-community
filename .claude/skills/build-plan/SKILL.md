---
name: build-plan
description: Phased build plan for Click app. Use this to understand the implementation order and what to build in each phase.
---

# Click App — Build Plan

Build in this exact order. Complete and TEST each phase before moving to the next.

## Phase 1: Foundation
- [ ] Initialize Next.js 15 with TypeScript, Tailwind CSS 4, App Router
- [ ] Configure `tailwind.config.ts` with Click design tokens from @docs/design-system.md
- [ ] Create CSS variables in `globals.css`
- [ ] Set up `.env.local` from `.env.example`
- [ ] Create base UI components: Button, Chip, Card, Avatar, Badge, BottomSheet, BottomNav, Toast, SkeletonLoader, EmptyState
- [ ] Create app layout with bottom nav + status bar
- [ ] Set up Zustand store for auth state and UI state
- [ ] Verify: all components render, design tokens match spec

## Phase 2: Auth & Onboarding
- [ ] Set up Supabase client (browser + server)
- [ ] Welcome screen with "Get Started" CTA
- [ ] Phone input with country code selector
- [ ] OTP verification screen (6-digit, auto-fill)
- [ ] Profile creation wizard (7 steps with progress bar)
- [ ] Photo upload to Supabase Storage
- [ ] Interest tag selector from predefined library
- [ ] MBTI quiz (binary choice questions)
- [ ] Deep questionnaire (multiple choice + sliders)
- [ ] Profile preview + completion
- [ ] Auth guards: redirect logged-in users, protect routes
- [ ] Verify: full onboarding flow works end-to-end

## Phase 3: Database & Schema
- [ ] Create all Supabase migrations from @docs/data-model.md
- [ ] Enable RLS on every table
- [ ] Write RLS policies for every table
- [ ] Seed interest_tags with predefined tags
- [ ] Generate TypeScript types: `npm run db:types`
- [ ] Create database helper functions in `src/lib/supabase/`
- [ ] Verify: can CRUD all tables, RLS blocks unauthorized access

## Phase 4: Profile
- [ ] Profile screen layout (hero photo, gradient, bio, interests)
- [ ] Photo gallery with swipe and dot indicators
- [ ] MBTI badge display
- [ ] Edit profile (inline editing for all fields)
- [ ] View other user's profile (from Clicks, Chats, Events)
- [ ] Report and Block via overflow menu
- [ ] Verify: own profile editable, other profiles read-only, block works

## Phase 5: Events
- [ ] Events screen with Discover/My Events tabs
- [ ] Event card component with all metadata
- [ ] Filter chip bar (All, This Weekend, Community, Member Hosted)
- [ ] Event detail screen with hero, attendees, CTA
- [ ] Registration flow with approval logic (server-side)
- [ ] Attendee list with avatar scroll
- [ ] My Events: attending with status badges
- [ ] PayPal ticket purchase integration for guests
- [ ] Event creation flow (for eligible hosts)
- [ ] Host Dashboard
- [ ] Verify: register, waitlist, approve flows all work

## Phase 6: Clicks
- [ ] Clicks screen with General/Event tabs
- [ ] Click card carousel (horizontal swipe)
- [ ] Matching algorithm (shared interests + MBTI)
- [ ] Event Clicks with compatibility tier badges
- [ ] "Send Message" banner (member) / "Become a member" (guest)
- [ ] Daily click generation cron job
- [ ] 30-day repeat prevention
- [ ] Verify: clicks appear, cards swipe, tap opens profile

## Phase 7: Chat
- [ ] Stream Chat SDK setup (client + server)
- [ ] Stream token generation API route
- [ ] Chat screen with DMs/Event Chats tabs
- [ ] DM list view with unread badges
- [ ] DM conversation view with bubble layout
- [ ] Icebreaker bottom sheet + flow
- [ ] Event group chat creation on registration
- [ ] Typing indicators + read receipts (DMs only)
- [ ] Chat hidden for guests (nav guard)
- [ ] Verify: can send/receive messages, icebreaker works

## Phase 8: Membership
- [ ] Membership tab visibility logic (hidden, pulsing, active)
- [ ] Membership landing page (confetti, benefits, CTA)
- [ ] PayPal subscription integration
- [ ] Member dashboard (event tracker ring, hosted events, recaps)
- [ ] Post-event categorization in recaps
- [ ] Newcomer voting survey
- [ ] Experience feedback survey
- [ ] Score calculation + membership unlock notification
- [ ] Monthly counter reset
- [ ] Verify: full membership lifecycle works

## Phase 9: Polish & Safety
- [ ] Push notifications setup
- [ ] Notification settings screen
- [ ] Report flow (full)
- [ ] Block flow with Stream Chat cleanup
- [ ] Loading states everywhere (skeleton loaders)
- [ ] Empty states everywhere (illustrations + CTAs)
- [ ] Toast notifications for all confirmations
- [ ] Error handling on all API calls
- [ ] Pull-to-refresh on lists
- [ ] Responsive desktop layout
- [ ] Verify: no screen without loading/empty/error states
