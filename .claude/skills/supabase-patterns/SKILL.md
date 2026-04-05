---
name: supabase-patterns
description: Supabase patterns for Click app. Use when creating database migrations, writing RLS policies, setting up storage buckets, implementing auth flows, or working with Supabase Realtime subscriptions.
---

# Supabase Patterns for Click

## Client Setup
Always use the pattern from `src/lib/supabase/`:
- `client.ts` — browser client (uses NEXT_PUBLIC_ keys)
- `server.ts` — server client (uses cookies + service role for admin ops)
- `types.ts` — auto-generated from `npm run db:types`

## Migrations
- Create in `supabase/migrations/` with timestamp prefix
- ALWAYS enable RLS: `alter table X enable row level security;`
- ALWAYS add RLS policies after creating a table
- Test policies by querying as different user roles

## RLS Policy Patterns
```sql
-- User can read own rows
create policy "Users read own" on table_name
  for select using (auth.uid() = user_id);

-- Anyone authenticated can read
create policy "Authenticated read" on table_name
  for select using (auth.role() = 'authenticated');

-- User can modify own rows
create policy "Users modify own" on table_name
  for all using (auth.uid() = user_id);
```

## Realtime Subscriptions
Use for: new clicks, new messages (backup to Stream), event attendance updates.
```typescript
supabase
  .channel('clicks')
  .on('postgres_changes', {
    event: 'INSERT',
    schema: 'public',
    table: 'clicks',
    filter: `user_a_id=eq.${userId}`,
  }, handleNewClick)
  .subscribe();
```

## Storage Buckets
- `profile-photos`: public read, authenticated write (own folder)
- `event-covers`: public read, authenticated write (event host only)

## Phone Auth
Use Supabase's built-in phone provider. Configure Twilio in dashboard.
```typescript
// Send OTP
await supabase.auth.signInWithOtp({ phone: '+972501234567' });
// Verify OTP
await supabase.auth.verifyOtp({ phone, token: otpCode, type: 'sms' });
```
