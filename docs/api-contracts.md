# Click — API Route Contracts

> All API routes are Next.js Route Handlers in `src/app/api/`.
> Supabase handles most CRUD directly from the client via its JS SDK + RLS.
> These routes are for operations that MUST run server-side.

## Auth & Tokens

### POST /api/auth/stream-token
Generate a Stream Chat user token for the authenticated user.
```typescript
// Request: none (uses session)
// Response:
{ token: string, userId: string, userName: string }
// Auth: Requires authenticated session
// Logic: Verify Supabase session, generate Stream Chat token with STREAM_API_SECRET
```

## Events

### POST /api/events/[eventId]/register
Register the current user for an event. Handles approval logic server-side.
```typescript
// Request:
{ /* none — uses session user */ }
// Response:
{ status: 'approved' | 'waitlisted' | 'pending', attendanceId: string }
// Auth: Requires authenticated session
// Logic:
//   1. Check membership status
//   2. If member: check gender balance → auto-approve or waitlist
//   3. If guest first event: check newcomer slots → approve or queue
//   4. If guest returning: check history → approve or queue
//   5. Create event_attendance record
//   6. If member: decrement monthly event counter
```

### POST /api/events/[eventId]/cancel-registration
Cancel the current user's event registration.
```typescript
// Response:
{ success: boolean }
// Logic: Delete attendance, restore event counter if member
```

## Payments

### POST /api/payments/create-ticket-order
Create a PayPal order for event ticket purchase.
```typescript
// Request:
{ eventId: string }
// Response:
{ orderId: string, approvalUrl: string }
// Logic: Create PayPal order with event ticket price
```

### POST /api/payments/capture-ticket
Capture a PayPal payment after user approval.
```typescript
// Request:
{ orderId: string, eventId: string }
// Response:
{ success: boolean, attendanceId: string }
// Logic: Capture payment, update attendance status to confirmed
```

### POST /api/payments/create-subscription
Create a PayPal subscription for membership.
```typescript
// Request: none
// Response:
{ subscriptionId: string, approvalUrl: string }
// Logic: Create PayPal subscription plan
```

### POST /api/payments/webhook
PayPal webhook handler for subscription events.
```typescript
// Events handled:
//   BILLING.SUBSCRIPTION.ACTIVATED → set membership_status = 'active'
//   BILLING.SUBSCRIPTION.CANCELLED → set membership_status = 'lapsed' at period end
//   BILLING.SUBSCRIPTION.PAYMENT.FAILED → notify user
//   PAYMENT.CAPTURE.COMPLETED → confirm ticket purchase
// Auth: Verify PayPal webhook signature with PAYPAL_WEBHOOK_ID
```

## Matching

### POST /api/clicks/generate
Generate daily clicks for all users. Called by cron job (Vercel Cron).
```typescript
// Auth: Requires CRON_SECRET header
// Logic:
//   1. For each active user:
//   2. Find candidates (not blocked, not shown in 30 days, not self)
//   3. Score by shared interests + MBTI compatibility
//   4. Select top 10 general clicks
//   5. For each registered event: score by questionnaire + interests → top 10
//   6. Create click records with 24h expiry
```

## Moderation

### POST /api/reports
Submit a report.
```typescript
// Request:
{ reportedUserId: string, reason: string, description?: string }
// Response:
{ reportId: string }
```

## Users

### POST /api/users/[userId]/block
Block a user. Handles Stream Chat channel cleanup server-side.
```typescript
// Request: none
// Response:
{ success: boolean }
// Logic:
//   1. Insert into blocked_users
//   2. Hide shared Stream Chat channels
//   3. Remove from future click generation
```

### GET /api/users/me/stream-channels
Get the current user's Stream Chat channel list (for initial load).
```typescript
// Response:
{ channels: StreamChannel[] }
```

## Cron Jobs (Vercel Cron)

### GET /api/cron/generate-clicks
Daily at midnight. Triggers click generation.
```
// vercel.json cron config:
{ "crons": [{ "path": "/api/cron/generate-clicks", "schedule": "0 0 * * *" }] }
```

### GET /api/cron/post-event-surveys
24 hours after each event ends. Creates survey records and sends push notifications.
```
// Schedule: runs hourly, checks for events that ended 24h ago
{ "crons": [{ "path": "/api/cron/post-event-surveys", "schedule": "0 * * * *" }] }
```

### GET /api/cron/expire-surveys
Expires surveys older than 72 hours.
```
{ "crons": [{ "path": "/api/cron/expire-surveys", "schedule": "0 */6 * * *" }] }
```
