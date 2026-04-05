# Click — User Flows & Screen States

> Reference for every screen's states, transitions, and edge cases.

## Flow 1: Authentication & Onboarding

```
[Welcome Screen]
  ↓ "Get Started"
[Phone Input]
  ↓ Submit
[OTP Input] ← auto-fill from SMS
  ↓ Verify
  ├── New user → [Profile Creation Wizard Step 1]
  └── Returning user → [Clicks Tab]

Profile Creation Wizard:
  [Step 1: Basics] → [Step 2: Photos] → [Step 3: About] →
  [Step 4: Interests] → [Step 5: MBTI Quiz] →
  [Step 6: Deep Questionnaire] → [Step 7: Preview] → [Clicks Tab]
```

### Screen States
| Screen        | Loading          | Empty               | Error              |
|---------------|------------------|----------------------|--------------------|
| Welcome       | N/A              | N/A                  | N/A                |
| Phone Input   | Sending OTP      | N/A                  | Invalid phone      |
| OTP Input     | Verifying        | N/A                  | Wrong code, Expired|
| Wizard Steps  | Saving step      | N/A                  | Validation errors  |

---

## Flow 2: Clicks Discovery

```
[Clicks Tab]
  ├── [General Clicks] (default)
  │     ↓ Swipe cards horizontally
  │     ↓ Tap card → [Profile Detail]
  │     ↓ "Send Message" → [Icebreaker Bottom Sheet] → [Chat Conversation]
  │
  └── [Event Clicks]
        ↓ Select event from chip bar
        ↓ Same card behavior + compatibility badge
```

### Screen States
| State              | Visual                                                    |
|--------------------|-----------------------------------------------------------|
| Loading            | Purple shimmer skeleton cards                             |
| Has clicks         | Swipeable card carousel with dots                         |
| No clicks          | Illustration + "No new Clicks right now" + update CTA    |
| Guest viewing      | Cards visible, banner shows "Become a member to message"  |
| Icebreaker sheet   | Bottom sheet with pre-filled message, editable            |
| Message sent       | Toast "Message sent" + navigate to chat                   |

---

## Flow 3: Events

```
[Events Tab]
  ├── [Discover]
  │     ↓ Filter chips (All / This Weekend / Community / Member Hosted)
  │     ↓ Tap event card → [Event Detail]
  │
  └── [My Events]
        ├── Attending: cards with status badges
        └── Hosting: cards with gear icon → [Host Dashboard]

[Event Detail]
  ↓ CTA button (varies by status — see matrix below)
  ↓ "Event Chat" → [Event Chat in Chats tab]

[Event Creation] (FAB, 2+ months tenure)
  ↓ Multi-step form → Preview → Publish
```

### Event Detail CTA Matrix
| User Status              | CTA Label               | Action                                |
|--------------------------|-------------------------|---------------------------------------|
| Guest, not applied       | "Request to Join — $XX" | Submit join request                   |
| Guest, pending           | "Pending Approval"      | Disabled                              |
| Guest, approved          | "Purchase Ticket — $XX" | PayPal payment flow                   |
| Member, spots available  | "Register"              | Instant confirm, decrement counter    |
| Member, waitlisted       | "Join Waiting List"     | Queue, notify when spot opens         |
| Already registered       | "Registered ✓"          | Tap to cancel with confirmation       |
| Event full               | "Event Full"            | Disabled                              |

---

## Flow 4: Chats

```
[Chats Tab] (Members only, hidden for guests)
  ├── [Direct Messages]
  │     ↓ Tap conversation → [DM Conversation]
  │     ↓ Swipe left on row → Mute / Block / Report
  │
  └── [Event Chats]
        ↓ Tap event chat → [Group Chat]
```

### DM Creation Sources
1. Icebreaker from Clicks → creates DM with icebreaker message
2. Mutual post-event "Click" categorization → creates DM with icebreaker
3. Existing conversation → continue

---

## Flow 5: Membership

```
Guest (score < +3):
  → Membership tab hidden

Guest (score >= +3):
  → Membership tab appears with pulsing badge
  → [Membership Landing Page]
    ↓ "Join the Community" → PayPal subscription → [Member Dashboard]
    ↓ "Maybe Later" → dismisses, tab stays

Member:
  → [Member Dashboard]
    ├── Event tracker ring (X of 3)
    ├── Hosted events list → [Host Dashboard]
    ├── Create Event (if 2+ months)
    └── Post-event recaps → categorize attendees
```

---

## Flow 6: Post-Event (triggered 24h after event)

```
[Newcomer Scoring Survey] (members only)
  ↓ Rate each newcomer: Click / No Click / Didn't Talk
  ↓ Submit → updates newcomer scores
  ↓ Expires 72 hours

[Experience Feedback Survey] (all attendees)
  ↓ 5-star rating
  ↓ Quick tags (multi-select)
  ↓ Optional comment
  ↓ Submit → visible in Host Dashboard
  ↓ Expires 72 hours

[Post-Event Categorization] (in Member Dashboard recaps)
  ↓ Per attendee: "It was a Click" / "Didn't Get to Meet" / "No Click"
  ↓ Mutual "Click" → opens DM with icebreaker
  ↓ "Didn't Get to Meet" → saved to Missed Connections
  ↓ Locks after 7 days
```

---

## Flow 7: Safety

```
[Report]
  Any profile/chat/event → overflow menu → "Report"
  → Select reason → Optional description → Submit
  → Toast: "Report submitted"
  → Routed to super-admin queue

[Block]
  Overflow menu → "Block" → Confirmation dialog → Immediate effect
  → Chat hidden, Clicks removed, Events filtered, Profile blocked
  → Unblock via Profile > Settings > Blocked Users
```

---

## Navigation Guard Rules
| Route              | Guest | Member | Redirect                        |
|--------------------|-------|--------|---------------------------------|
| /clicks            | ✅ RO  | ✅      |                                 |
| /chats             | ❌     | ✅      | /clicks with toast              |
| /events            | ✅     | ✅      |                                 |
| /membership        | Conditional | ✅  | Hidden if score < +3            |
| /profile           | ✅     | ✅      |                                 |
| /events/create     | ❌     | 2+mo   | /events with toast              |
| /onboarding/*      | ✅     | ❌      | /clicks if profile complete     |
