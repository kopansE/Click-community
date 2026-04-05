# Click — Design System

> Source of truth for all visual decisions. NEVER hardcode colors — always use CSS variables / Tailwind tokens.

## Design Philosophy — IMPORTANT

Click targets 20-30 year olds. The design MUST feel:

- **Young and energetic** — not corporate, not sterile. Think Instagram meets Eventbrite, not LinkedIn.
- **Colorful and vibrant** — use the purple palette boldly. Gradient overlays, tinted backgrounds, colored shadows. Avoid large areas of plain white.
- **Interactive and alive** — micro-animations on EVERYTHING: button taps should scale, cards should have hover/press states, page transitions should slide, lists should stagger-animate in. Use spring/bounce easing, not linear.
- **Playful but trustworthy** — rounded corners everywhere (16px+), soft shadows, emoji as visual accents. But keep text readable and actions clear.
- **Mobile-native feel** — haptic feedback patterns, swipe gestures, bottom sheets instead of modals, pull-to-refresh. It should feel like an app, not a website.

### Specific Animation Requirements
- Page transitions: slide left/right for navigation, slide up for detail views
- Card entrances: stagger fade-up with 50ms delay between items
- Button press: scale(0.97) on press, spring back on release
- Bottom sheets: spring animation on open, drag-to-dismiss
- Tab switches: crossfade content, underline slides to active tab
- Skeleton loaders: purple-tinted shimmer (primary-50 → primary-100 pulse)
- Confetti: on membership unlock, mutual Click match
- Pull-to-refresh: custom purple spinner
- Toast notifications: slide up from bottom with spring, auto-dismiss with fade

### What to AVOID
- Large empty white spaces — fill with tinted backgrounds (primary-50, gradients)
- Generic/default component styling — every element should feel custom
- Static pages — if nothing moves, something is wrong
- Desktop-first patterns (dropdowns, hover-only states, modals)
- Small/thin fonts — keep everything bold and readable on mobile

## Color Tokens

Map these to CSS variables AND Tailwind config:

```
--color-primary-900: #4C1D95    → Headers, active nav icons
--color-primary-700: #6D28D9    → Primary buttons, CTAs
--color-primary-500: #7C3AED    → Accents, selected states
--color-primary-100: #EDE9FE    → Card backgrounds, highlights
--color-primary-50:  #F5F3FF    → Screen backgrounds
--color-neutral-900: #111827    → Body text
--color-neutral-500: #6B7280    → Secondary text
--color-neutral-100: #F3F4F6    → Dividers, input backgrounds
--color-surface:     #FFFFFF    → Cards, modals
--color-success:     #10B981    → Confirmations, "It was a Click"
--color-warning:     #F59E0B    → Waiting list, pending states
--color-error:       #EF4444    → Destructive actions, "No Click"
```

## Typography

Use system fonts for performance: SF Pro (iOS), Roboto (Android), system-ui fallback.

| Style    | Size  | Weight    | Tailwind Class          | Usage                |
|----------|-------|-----------|-------------------------|----------------------|
| H1       | 28px  | Bold      | text-3xl font-bold      | Screen titles        |
| H2       | 22px  | Semi-bold | text-xl font-semibold   | Section headers      |
| H3       | 18px  | Semi-bold | text-lg font-semibold   | Card titles, names   |
| Body     | 16px  | Regular   | text-base               | General content      |
| Caption  | 14px  | Regular   | text-sm                 | Timestamps, metadata |
| Overline | 12px  | Medium    | text-xs font-medium     | Tags, labels, badges |

## Component Specs

### Bottom Nav Bar
- Active tab: primary-700 icon + label
- Inactive: neutral-500
- Floating style with subtle shadow (shadow-lg)
- Safe area padding on iOS

### Cards
- Rounded corners: rounded-2xl (16px)
- Background: surface
- Shadow: shadow-sm (2dp equivalent)
- Used for: click profiles, event listings, chat previews

### Chips / Tags
- Rounded pill: rounded-full
- Default: primary-100 bg, primary-700 text
- Active/selected: primary-700 bg, white text
- Outlined variant for filters: border border-primary-700

### Buttons
- Primary: bg-primary-700, text-white, rounded-xl (12px)
- Secondary: border border-primary-700, text-primary-700, rounded-xl
- Destructive: bg-error, text-white, rounded-xl
- Full-width on mobile by default

### Avatars
- Circular (rounded-full)
- 3 sizes: 40px (list items), 56px (cards), 96px (profile header)
- Object-fit: cover

### Badges
- Notification dot on nav icons
- Small red circle, white count text
- Min-width 18px, rounded-full

### Bottom Sheet
- Slide-up panel for actions
- Drag handle at top (centered, 40px wide, 4px tall, rounded, neutral-300)
- Rounded top corners: rounded-t-2xl
- Backdrop: bg-black/50

### Skeleton Loaders
- Shimmer animation from primary-50 to primary-100
- Match the shape of content being loaded

## Interaction Patterns
- Pull-to-refresh on scrollable lists
- Haptic feedback on key actions (use navigator.vibrate where supported)
- Horizontal swipe on Click cards
- Toast notifications for confirmations (3 second duration, bottom-positioned)
- Empty states: illustration + action CTA

## Spacing Scale
Use Tailwind defaults: 4px base unit (p-1 = 4px, p-2 = 8px, p-4 = 16px, etc.)
Standard content padding: px-5 (20px horizontal)
Card internal padding: p-4 (16px)
Section gaps: space-y-3 or gap-3 (12px)

## Responsive Breakpoints
- Base: 375px (mobile, design target)
- sm: 640px (large phone / small tablet)
- md: 768px (tablet)
- lg: 1024px (desktop — show phone frame mockup or 2-column layout)
