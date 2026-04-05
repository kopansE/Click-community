---
name: paypal-integration
description: PayPal payment integration patterns. Use when implementing event ticket purchases, membership subscriptions, payment webhooks, or refund handling.
---

# PayPal Integration for Click

## Two Payment Flows

### 1. Event Ticket (One-Time Payment)
For guests purchasing single event tickets.
```
User clicks "Purchase Ticket — $XX"
  → Client calls POST /api/payments/create-ticket-order
  → Server creates PayPal order → returns approvalUrl
  → Client redirects to PayPal / opens PayPal popup
  → User approves → redirect back to app
  → Client calls POST /api/payments/capture-ticket
  → Server captures payment → updates attendance to confirmed
```

### 2. Membership Subscription (Recurring)
For users subscribing to monthly membership.
```
User clicks "Join the Community"
  → Client calls POST /api/payments/create-subscription
  → Server creates PayPal subscription → returns approvalUrl
  → User approves → redirect back
  → Webhook: BILLING.SUBSCRIPTION.ACTIVATED
  → Server sets membership_status = 'active', membership_start_date = now()
```

## Server-Side SDK
```typescript
// Use @paypal/paypal-server-sdk
import { PayPalHttpClient, OrdersCreateRequest, OrdersCaptureRequest } from '@paypal/paypal-server-sdk';

const environment = process.env.PAYPAL_ENVIRONMENT === 'live'
  ? new LiveEnvironment(clientId, clientSecret)
  : new SandboxEnvironment(clientId, clientSecret);

const client = new PayPalHttpClient(environment);
```

## Webhook Verification
ALWAYS verify webhook signatures:
```typescript
// In POST /api/payments/webhook
const isValid = await verifyWebhookSignature({
  webhookId: process.env.PAYPAL_WEBHOOK_ID,
  headers: request.headers,
  body: rawBody,
});
if (!isValid) return new Response('Invalid', { status: 401 });
```

## Environment Variables
- NEXT_PUBLIC_PAYPAL_CLIENT_ID — for client-side PayPal buttons
- PAYPAL_CLIENT_SECRET — server-side only
- PAYPAL_WEBHOOK_ID — for webhook verification
- PAYPAL_ENVIRONMENT — "sandbox" or "live"
