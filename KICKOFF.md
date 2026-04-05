# How to Use This Bootstrap Kit with Claude Code

## Setup (do this first)

```bash
# 1. Install Claude Code if you haven't
npm install -g @anthropic-ai/claude-code

# 2. Navigate to this project folder
cd click-app

# 3. Initialize git
git init
git add -A
git commit -m "Initial bootstrap: docs, CLAUDE.md, skills, env template"

# 4. Copy env template
cp .env.example .env.local
# Fill in values as you set up each service (Supabase, Stream, PayPal)

# 5. Launch Claude Code
claude
```

## Your First Prompt (paste this into Claude Code)

```
Read CLAUDE.md and all linked docs. Then start Phase 1 from the build-plan skill:

Initialize the Next.js 15 project with TypeScript and Tailwind CSS 4.
Set up the design tokens from docs/design-system.md as CSS variables and in the Tailwind config.
Build all the base UI components: Button, Chip, Card, Avatar, Badge, BottomSheet, BottomNav, Toast, SkeletonLoader, EmptyState.
Create the app shell layout with the bottom navigation bar.
Set up a Zustand store for auth state.

Use Plan mode first — show me what you'll create, then implement.
```

## Phase-by-Phase Prompts

After Phase 1 is done and verified, start each new Claude Code session with:

```
Read CLAUDE.md. Start Phase [N] from the build-plan skill.
```

For example:

### Phase 2
```
Read CLAUDE.md. Start Phase 2 from the build-plan skill:
Build the auth and onboarding flow. Set up Supabase auth with phone/OTP,
create the welcome screen, phone input, OTP verification, and the full
7-step profile creation wizard. All using the design system components
from Phase 1.
```

### Phase 3
```
Read CLAUDE.md. Start Phase 3 from the build-plan skill:
Create all Supabase database migrations from docs/data-model.md.
Enable RLS on every table and write policies.
Seed the interest_tags table with predefined tags.
Generate TypeScript types.
```

...and so on for each phase.

## Tips for Each Session

1. **Always start with:** "Read CLAUDE.md" — this ensures all context is loaded.

2. **Use Plan mode for complex phases.** Say "plan first, then implement" or
   start in plan mode with `/plan`.

3. **One phase per session.** Don't try to build everything at once.
   Start a fresh session (`/clear`) for each phase.

4. **Verify before moving on.** At the end of each phase, say:
   "Run typecheck and lint. Fix any errors. Then show me what we built."

5. **When things go wrong:** Say "knowing everything you know now, scrap this
   and implement the elegant solution" — this forces a fresh approach.

6. **Context management:** If a session gets long, use `/compact` at ~50%
   context usage. Watch the context meter.

7. **Git after each phase:**
   ```
   git add -A && git commit -m "Phase N: [description]"
   ```

## File Structure Reference

```
click-app/
├── CLAUDE.md                     ← Main context file (Claude reads this first)
├── .env.example                  ← All env vars template (empty values)
├── .gitignore
├── docs/
│   ├── product-spec.md           ← Full product specification
│   ├── design-system.md          ← Colors, typography, components
│   ├── data-model.md             ← Database schema + RLS policies
│   ├── user-flows.md             ← Screen states and transitions
│   └── api-contracts.md          ← All API route contracts
├── .claude/
│   ├── settings.json             ← Permissions + auto-format hook
│   └── skills/
│       ├── build-plan/SKILL.md   ← 9-phase implementation plan
│       ├── supabase-patterns/SKILL.md
│       ├── stream-chat/SKILL.md
│       └── paypal-integration/SKILL.md
└── (project files created by Claude Code during build)
```

## When to Fill In .env.local Values

You don't need all values upfront. Fill them in as each phase needs them:

| Phase | Services Needed |
|-------|----------------|
| 1     | None — just UI components |
| 2     | SUPABASE_URL, SUPABASE_ANON_KEY (create Supabase project, enable Phone auth) |
| 3     | SUPABASE_SERVICE_ROLE_KEY, SUPABASE_DB_URL |
| 4     | Same as Phase 3 |
| 5     | PAYPAL_CLIENT_ID, PAYPAL_CLIENT_SECRET (create PayPal sandbox app) |
| 6     | Same as Phase 3 |
| 7     | STREAM_API_KEY, STREAM_API_SECRET (create Stream Chat app) |
| 8     | Same as Phase 5 (PayPal subscription) |
