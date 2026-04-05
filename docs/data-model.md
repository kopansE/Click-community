# Click — Data Model & Supabase Schema

> This defines the database schema. Use Supabase migrations to create tables.
> EVERY table MUST have RLS enabled. No exceptions.

## Tables

### users
```sql
create table users (
  id uuid primary key references auth.users(id) on delete cascade,
  phone text unique not null,
  name text not null,
  dob date not null,
  gender text not null check (gender in ('male', 'female', 'non-binary', 'prefer-not-to-say')),
  occupation text,
  bio text check (char_length(bio) <= 300),
  mbti_type text,
  membership_status text not null default 'guest' check (membership_status in ('guest', 'unlocked', 'active', 'lapsed')),
  membership_start_date timestamptz,
  newcomer_score int not null default 0,
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now()
);
-- RLS: Users can read any profile. Users can update only their own.
```

### user_photos
```sql
create table user_photos (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references users(id) on delete cascade,
  storage_path text not null,
  position int not null default 0,
  created_at timestamptz not null default now()
);
-- RLS: Anyone authed can read. Owner can insert/update/delete.
```

### interest_tags
```sql
create table interest_tags (
  id uuid primary key default gen_random_uuid(),
  label text unique not null,
  is_custom boolean not null default false,
  is_approved boolean not null default true,
  created_at timestamptz not null default now()
);
-- RLS: Anyone authed can read. Only admin can insert/update.
```

### user_interests
```sql
create table user_interests (
  user_id uuid not null references users(id) on delete cascade,
  tag_id uuid not null references interest_tags(id) on delete cascade,
  primary key (user_id, tag_id)
);
-- RLS: Anyone authed can read. Owner can insert/delete own.
```

### questionnaire_responses
```sql
create table questionnaire_responses (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references users(id) on delete cascade,
  questionnaire_type text not null check (questionnaire_type in ('mbti', 'deep_personality')),
  responses jsonb not null,
  created_at timestamptz not null default now()
);
-- RLS: MBTI responses readable by anyone. Deep personality readable only by owner.
```

### events
```sql
create table events (
  id uuid primary key default gen_random_uuid(),
  name text not null,
  description text check (char_length(description) <= 500),
  cover_image_path text,
  date_time timestamptz not null,
  venue_name text not null,
  venue_address text,
  capacity int not null,
  newcomer_reserved_slots int not null default 10,
  ticket_price decimal(10,2) default 0,
  type text not null check (type in ('community', 'member_hosted')),
  host_id uuid not null references users(id),
  status text not null default 'published' check (status in ('draft', 'published', 'cancelled', 'completed')),
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now()
);
-- RLS: Anyone authed can read published. Host can update own. Admin can update any.
```

### event_attendances
```sql
create table event_attendances (
  id uuid primary key default gen_random_uuid(),
  event_id uuid not null references events(id) on delete cascade,
  user_id uuid not null references users(id) on delete cascade,
  status text not null default 'pending' check (status in ('pending', 'approved', 'waitlisted', 'rejected', 'confirmed')),
  is_newcomer boolean not null default false,
  ticket_purchase_id text,
  created_at timestamptz not null default now(),
  unique (event_id, user_id)
);
-- RLS: Users can read own. Host can read for their events. Users can insert own.
```

### clicks
```sql
create table clicks (
  id uuid primary key default gen_random_uuid(),
  user_a_id uuid not null references users(id) on delete cascade,
  user_b_id uuid not null references users(id) on delete cascade,
  type text not null check (type in ('general', 'event_specific')),
  event_id uuid references events(id),
  shared_interests uuid[] default '{}',
  compatibility_score decimal(5,2),
  generated_at timestamptz not null default now(),
  expires_at timestamptz not null,
  check (user_a_id < user_b_id)
);
-- RLS: Users can read clicks where they are user_a or user_b.
```

### newcomer_votes
```sql
create table newcomer_votes (
  id uuid primary key default gen_random_uuid(),
  voter_id uuid not null references users(id),
  newcomer_id uuid not null references users(id),
  event_id uuid not null references events(id),
  vote text not null check (vote in ('click', 'no_click', 'didnt_talk')),
  created_at timestamptz not null default now(),
  unique (voter_id, newcomer_id, event_id)
);
-- RLS: Members can insert own votes. Newcomer cannot read votes about them.
```

### post_event_categorizations
```sql
create table post_event_categorizations (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references users(id),
  target_user_id uuid not null references users(id),
  event_id uuid not null references events(id),
  category text not null check (category in ('click', 'didnt_meet', 'no_click')),
  created_at timestamptz not null default now(),
  unique (user_id, target_user_id, event_id)
);
-- RLS: Users can read/insert own categorizations.
```

### event_feedbacks
```sql
create table event_feedbacks (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references users(id),
  event_id uuid not null references events(id),
  rating int not null check (rating between 1 and 5),
  tags text[] default '{}',
  comment text check (char_length(comment) <= 200),
  created_at timestamptz not null default now(),
  unique (user_id, event_id)
);
-- RLS: Users can insert own. Host can read aggregated (not individual user_id). Admin can read all.
```

### reports
```sql
create table reports (
  id uuid primary key default gen_random_uuid(),
  reporter_id uuid not null references users(id),
  reported_user_id uuid not null references users(id),
  reason text not null,
  description text check (char_length(description) <= 300),
  status text not null default 'pending' check (status in ('pending', 'warned', 'suspended', 'banned', 'dismissed')),
  resolved_by uuid references users(id),
  created_at timestamptz not null default now()
);
-- RLS: Reporter can read own reports. Admin can read/update all.
```

### blocked_users
```sql
create table blocked_users (
  blocker_id uuid not null references users(id) on delete cascade,
  blocked_id uuid not null references users(id) on delete cascade,
  created_at timestamptz not null default now(),
  primary key (blocker_id, blocked_id)
);
-- RLS: Users can read/insert/delete own blocks.
```

## Key Relationships
- Clicks are always generated with user_a_id < user_b_id to prevent duplicates
- Chats (DMs and event chats) are managed in Stream Chat, NOT in Supabase
- Stream Chat user tokens are generated server-side using STREAM_API_SECRET
- Photos stored in Supabase Storage bucket "profile-photos"
- Event cover images in Supabase Storage bucket "event-covers"

## Indexes (create after tables)
```sql
create index idx_clicks_user_a on clicks(user_a_id);
create index idx_clicks_user_b on clicks(user_b_id);
create index idx_clicks_expires on clicks(expires_at);
create index idx_events_datetime on events(date_time);
create index idx_event_attendances_event on event_attendances(event_id);
create index idx_event_attendances_user on event_attendances(user_id);
create index idx_newcomer_votes_newcomer on newcomer_votes(newcomer_id);
create index idx_user_interests_user on user_interests(user_id);
```

## Database Functions (Supabase Edge Functions or SQL functions)
- `calculate_newcomer_score(user_id)` → returns int (sum of votes)
- `generate_daily_clicks()` → cron job, runs at midnight, creates click records
- `check_gender_balance(event_id)` → returns { male: int, female: int, other: int, balanced: boolean }
- `auto_approve_attendee(attendance_id)` → applies approval logic from spec
