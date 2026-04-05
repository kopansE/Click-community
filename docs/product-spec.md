# Click — Product Design Specification

> This is the full product spec. Claude MUST consult this before implementing any feature.
> When in doubt about behavior, this document is the source of truth.

## 1. Product Overview

**Click** is a community-centric mobile social platform that bridges algorithm-driven digital matching ("Clicks") with curated real-world event experiences. Unlike swipe-based dating apps, Click removes user selection bias — the system surfaces high-potential connections based on shared interests and personality compatibility, encouraging users to meet in person at organized events.

### Core Loop
```
Onboard → Get Matched (Clicks) → Message → Attend Event → Post-Event Categorize → Repeat
```

### User Roles
- **Guest**: Authenticated user without membership. Can browse Clicks (read-only), attend events via one-time ticket purchase.
- **Community Member**: Paying subscriber with full access. Unlocked after community approval (net score +3).
- **Event Host**: Community member with 2+ months tenure who creates/manages events.
- **Super-Admin**: Single app-level admin. Manages reports, moderation, platform settings via separate web dashboard.

### Access Matrix
| Feature        | Guest                                                          | Member                                        |
|----------------|----------------------------------------------------------------|-----------------------------------------------|
| Profile        | Full access                                                    | Full access                                   |
| Clicks         | Visible — browse matches, see shared interests. CANNOT message | Full access                                   |
| Chats          | Hidden — tab not shown in bottom nav                           | Full access                                   |
| Events         | Browse, apply, purchase one-time tickets                       | Full access + registration with monthly quota |
| Membership tab | Hidden until newcomer score >= +3                              | Member dashboard                              |

### Navigation
- **Guests:** 4-tab bottom nav (Profile, Events, Clicks, and Membership once unlocked)
- **Members:** 5-tab bottom nav (Profile, Chats, Events, Clicks, Membership)

---

## 2. Onboarding & Authentication

### Authentication
- Method: Phone number + OTP verification (via Supabase Auth / Twilio)
- Flow: Welcome screen → Phone input → OTP → New user: profile creation / Returning user: home

### Profile Creation (Multi-Step Wizard)
Progress bar at top. Required steps cannot be skipped.

**Step 1 — Basics:** First name (required), DOB (required, 18+), Gender (Male/Female/Non-binary/Prefer not to say)
**Step 2 — Photos:** 1-6 photos, first = avatar, square crop, drag to reorder
**Step 3 — About You:** Bio (300 chars max), Occupation (50 chars max)
**Step 4 — Interests:** Min 3 tags from predefined library, custom tag submission available
**Step 5 — MBTI Quiz:** 16 Personalities questionnaire, binary choices, results visible on profile
**Step 6 — Deep Questionnaire:** For matching algorithm only, NOT displayed publicly
**Step 7 — Completion:** Profile preview → "Looks good!" → Clicks tab

---

## 3. Profile

### Layout
- Hero photo (full-width, 4:5) with gradient overlay → Name, age, occupation over gradient
- MBTI badge chip below name
- Swipeable photo gallery (dots indicator)
- Bio card
- Interests chip grid

### Actions
- Own profile: "Edit Profile" button, inline edit mode
- Other user: "Send Message" bottom banner (guests see disabled "Become a member to message")
- Overflow menu: Report, Block

### Edit Profile
- All onboarding fields editable inline
- Photo add/remove/reorder
- MBTI retake option
- Questionnaire retake option

---

## 4. Chats (Members Only)

### Two Tabs: Direct Messages | Event Chats

### Direct Messages
- List: Avatar, name, last message preview, timestamp, unread badge
- Sorted by recency, unread pinned to top
- Swipe left: Mute/Block/Report
- Chat bubbles: user right-aligned (primary-700 bg), other left-aligned (neutral-100 bg)
- Text only in v1
- Timestamps every 15-min gap

### Icebreaker Flow
- Tapping "Send Message" from Click profile opens bottom sheet
- System-generated icebreaker pre-filled (e.g., "You both love hiking!")
- User edits before sending
- First message has special "Icebreaker" styling

### Event Chats
- Group chatrooms per event
- Shows registered events (upcoming + 7 days past)
- Sender name + avatar above messages
- Participant count in header

### Behaviors
- DMs persist indefinitely; Event chats archived 7 days post-event
- Typing indicators: DMs only
- Read receipts: DMs only (double-check marks)
- Blocked users: conversation hidden, messages silently dropped

---

## 5. Events

### Two Tabs: Discover | My Events

### Discover Tab
Event cards: Cover image (16:9), name, date/time, venue, attendee count/capacity, type badge, gender balance indicator
Filters: All, This Weekend, Community, Member Hosted (horizontal chip bar)
Sort: by date (nearest first)

### My Events Tab
- Attending section: cards with status badge (Confirmed/Waiting List/Pending Approval)
- Hosting section: cards with management gear icon (2+ months tenure only)

### Event Detail Screen
- Hero image with gradient overlay
- Event name, date, time, venue, host info
- About section (500 chars max)
- Attendees horizontal avatar scroll (+N more)
- Event Chat shortcut button
- Bottom CTA varies by user status (see spec for full matrix)

### Attendee Approval System
1. Member? → Check gender balance → Auto-approve or waitlist
2. Guest first event? → Reserved newcomer slot → Approved
3. Guest returning? → Auto-approve if positive newcomer history

### Post-Event: Newcomer Scoring (Members vote on newcomers)
- Click (+1), No Click (-1), Didn't Talk (0)
- Net score +3 → Membership unlocks
- 72-hour expiry

### Post-Event: Experience Feedback
- 5-star rating, quick tags, open-ended comment
- Anonymous to host, visible in Host Dashboard
- 72-hour expiry

### Host Dashboard
Event details edit, attendee management, newcomer queue, event chat, capacity bar, feedback summary

### Event Creation (2+ months tenure)
Name, description, cover image, date/time, venue, capacity, ticket price → Preview → Publish

---

## 6. Clicks (Discovery)

### Two Tabs: General Clicks | Event Clicks

### General Clicks
- Algorithm: shared interests + MBTI compatibility + recency + negative signals
- Horizontally swipeable cards: photo (4:5), name, age, shared interests, event status
- Tap card → full profile
- "Send Message" banner (members) triggers icebreaker bottom sheet
- Guests see disabled "Become a member to message"

### Event Clicks
- Event selector: horizontal chip bar of registered events
- Algorithm: deep personality questionnaire + interests
- Cards with compatibility tier badge: High Match / Great Match / Top Match

### Refresh Rules
- Daily at midnight local time
- Up to 10 General + 10 Event Clicks per day
- 30-day repeat prevention

---

## 7. Membership

### Tab Visibility
- Guest pre-event: Hidden
- Guest with score < +3: Hidden
- Guest with score >= +3: Visible (pulsing badge)
- Member: Visible (dashboard)

### Membership Landing Page (score >= +3)
- Confetti animation
- "You're In!" header
- Benefit cards (events, clicks, messaging, hosting)
- Price + "Join the Community" CTA → in-app purchase
- "Maybe Later" dismisses but tab stays

### Member Dashboard
- Event tracker: circular progress ring (X of 3 used this month)
- Hosted events list
- Create Event button (if tenure >= 2 months)
- Post-event recaps with categorization

### Lifecycle
- Subscription: full access, 3 events/month
- Monthly renewal: counter resets
- Cancellation: access until billing period end → reverts to guest
- Lapsed: loses messaging, chat, event registration. Can resubscribe without re-approval.
- Tenure: counted from first subscription date. Hosting at 2 months.

---

## 8. Notifications

| Trigger                      | Template                                                      | Priority |
|------------------------------|---------------------------------------------------------------|----------|
| New Click available          | "You have a new Click! See who matched with you."             | Normal   |
| New direct message           | "[Name]: [preview]"                                           | High     |
| Event registration confirmed | "You're in! See you at [Event] on [Date]."                    | High     |
| Waiting list spot opened     | "A spot opened at [Event]! Confirm your attendance."          | High     |
| Post-event surveys available | "How was [Event]? Share your feedback."                       | Normal   |
| Membership unlocked          | "The community wants you! Unlock your membership now."        | High     |
| Mutual post-event Click      | "It's a match! You and [Name] both clicked. Start chatting!"  | High     |

Settings: Global toggle, per-category toggles, per-conversation mute, quiet hours (default 11pm-8am)

---

## 9. Safety & Moderation

### Reporting
Overflow menu → "Report" → Select reason → Optional description → Toast confirmation
All reports routed to super-admin.

### Blocking
Overflow menu → "Block" → Confirmation → Immediate effect
Effects: Chat hidden, Clicks removed, Event attendee lists filtered, Profile blocked, Unblock in Settings

### Content Policy (v1)
No automated moderation. Community event descriptions reviewed before publishing. Everything else reactive via reports.
