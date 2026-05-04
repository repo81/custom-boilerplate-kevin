---
name: custom-boilerplate-kevin
description: Use when starting a new SaaS project from scratch — sets up Next.js App Router with Supabase, Stripe, and Tailwind, installs all required CLIs, scaffolds env vars, database schema, RLS policies, client files, and project structure.
---

# Custom Boilerplate — Kevin

## Before You Start

Launch Claude Code with the `--dangerously-allow-all` flag for the initial scaffold session — this skips all permission prompts so setup runs uninterrupted:

```bash
claude --dangerously-allow-all
```

Step 2b below also writes a `.claude/settings.local.json` with a permanent allowlist for ongoing development sessions where you don't want to use `--dangerously-allow-all`.

## Overview

Full-stack SaaS starter: **Next.js 16 App Router · Supabase (auth + DB) · Stripe (subscriptions) · Tailwind CSS 3**. Follow steps in order.

---

## Step 1 — Install CLIs

### macOS

```bash
# GitHub CLI
brew install gh
gh auth login

# Vercel CLI
npm install -g vercel
vercel login

# Stripe CLI
brew install stripe/stripe-cli/stripe
stripe login

# Supabase CLI
brew install supabase/tap/supabase
supabase login
```

### Windows

`gh` and `stripe` install cleanly via winget. Supabase has no winget package — install manually via the GitHub releases binary and drop it into the npm global bin directory (already on PATH).

```bash
# GitHub CLI — installs to C:\Program Files\GitHub CLI\gh.exe
winget install --id GitHub.cli
gh auth login

# Vercel CLI — installs to C:\Users\<you>\AppData\Roaming\npm\vercel.cmd
npm install -g vercel
vercel login

# Stripe CLI — installs to C:\Users\<you>\AppData\Local\stripe\stripe.exe
winget install --id Stripe.StripeCLI
stripe login

# Supabase CLI — no winget package; download binary from GitHub releases
# 1. Download the Windows zip from https://github.com/supabase/cli/releases
#    (e.g. supabase_windows_amd64.tar.gz)
# 2. Extract and copy the binary to the npm global bin dir so it's on PATH:
cp "/c/Users/<you>/Downloads/supabase.exe" "$(npm config get prefix)/supabase.exe"
supabase --version   # verify
supabase login
```

> **Note:** `winget` commands require Windows Package Manager (built into Windows 11; install from the Microsoft Store on Windows 10). The npm global bin path (`npm config get prefix`) is already on PATH whenever npm is installed.

---

## Step 2 — Scaffold Project

```bash
npx create-next-app@latest my-app \
  --typescript --tailwind --eslint --app --src-dir=no --import-alias="@/*"

cd my-app
npm install @supabase/supabase-js @supabase/ssr stripe
```

---

## Step 2b — Write Claude Permissions Files

### Global permissions (run once, applies to all projects)

Read `~/.claude/settings.json`, then merge the `permissions.allow` array below into it — preserving all existing keys. If the file has no `permissions` key yet, add it at the top level.

```json
"permissions": {
  "allow": [
    "Bash(git init *)",
    "Bash(git add *)",
    "Bash(git commit *)",
    "Bash(git push *)",
    "Bash(git merge *)",
    "Bash(gh auth *)",
    "Bash(gh repo *)",
    "Bash(gh --version)",
    "Bash(gh version *)",
    "PowerShell(gh auth *)",
    "Bash(npx create-next-app@latest *)",
    "Bash(npm install *)",
    "Bash(npm run *)",
    "Bash(npm config *)",
    "Bash(npx tsc *)",
    "Bash(./node_modules/.bin/tsc *)",
    "Bash(supabase --version)",
    "Bash(supabase projects *)",
    "Bash(supabase link *)",
    "Bash(supabase db *)",
    "Bash(supabase migration *)",
    "Bash(vercel --version)",
    "Bash(vercel env *)",
    "Bash(vercel deploy *)",
    "Bash(vercel logs *)",
    "Bash(vercel inspect *)",
    "Bash(stripe --version)",
    "Bash(stripe version *)",
    "Bash(curl *)",
    "Bash(docker info *)",
    "Bash(ls -d .worktrees)",
    "Bash(ls -d worktrees)",
    "Bash(git check-ignore *)",
    "Bash(git worktree *)",
    "Bash(node -e \"const p = require('./package.json'); console.log('next:', p.dependencies.next, 'tailwind:', p.devDependencies?.tailwindcss || p.dependencies?.tailwindcss)\")"
  ]
}
```

After writing, validate the JSON is parseable before continuing.

### Project permissions (per-project)

Create `.claude/settings.local.json` in the project root:

```bash
mkdir -p .claude
```

Write `.claude/settings.local.json`:

```json
{
  "permissions": {
    "allow": [
      "Bash(git init *)",
      "Bash(git add *)",
      "Bash(git commit *)",
      "Bash(git push *)",
      "Bash(git merge *)",
      "Bash(gh auth *)",
      "Bash(gh repo *)",
      "PowerShell(gh auth *)",
      "Bash(npx create-next-app@latest *)",
      "Bash(npm install *)",
      "Bash(npm run *)",
      "Bash(npm config *)",
      "Bash(npx tsc *)",
      "Bash(./node_modules/.bin/tsc *)",
      "Bash(supabase --version)",
      "Bash(supabase projects *)",
      "Bash(supabase link *)",
      "Bash(supabase db *)",
      "Bash(supabase migration *)",
      "Bash(vercel env *)",
      "Bash(vercel deploy *)",
      "Bash(vercel logs *)",
      "Bash(vercel inspect *)",
      "Bash(curl *)",
      "Bash(docker info *)",
      "Bash(ls -d .worktrees)",
      "Bash(ls -d worktrees)",
      "Bash(git check-ignore *)",
      "Bash(git worktree *)",
      "Bash(node -e \"const p = require('./package.json'); console.log('next:', p.dependencies.next, 'tailwind:', p.devDependencies?.tailwindcss || p.dependencies?.tailwindcss)\")"
    ]
  }
}
```

---

## Step 3 — Project Folder Structure

```
my-app/
├── app/
│   ├── actions/          # Server Actions (auth.ts, etc.)
│   ├── api/
│   │   ├── stripe/
│   │   │   ├── checkout/route.ts
│   │   │   └── portal/route.ts
│   │   └── webhooks/
│   │       └── stripe/route.ts
│   ├── auth/
│   │   ├── callback/route.ts
│   │   └── login/page.tsx
│   ├── dashboard/page.tsx
│   ├── pricing/
│   │   ├── page.tsx
│   │   └── components/PricingContent.tsx
│   ├── layout.tsx
│   └── page.tsx
├── components/
│   ├── Nav.tsx
│   └── CourseCard.tsx
├── lib/
│   ├── supabase/
│   │   ├── server.ts
│   │   ├── client.ts
│   │   └── middleware.ts
│   └── stripe/
│       ├── server.ts
│       └── config.ts
├── types/
│   └── database.ts
├── middleware.ts
└── tailwind.config.ts
```

---

## Step 4 — .gitignore Additions

Append to the generated `.gitignore`:

```
# Environment
.env.local
.env.*.local

# Supabase local
supabase/.branches
supabase/.temp
```

---

## Step 5 — Git & GitHub Setup

```bash
git init
git add .
git commit -m "feat: initial scaffold"

# Create remote repo and push
gh repo create my-app --private --source=. --remote=origin --push

# Branch protection (run after first push)
gh api repos/:owner/my-app/branches/main/protection \
  --method PUT \
  --field required_status_checks=null \
  --field enforce_admins=false \
  --field required_pull_request_reviews=null \
  --field restrictions=null
```

---

## Step 6 — Environment Variables

Create `.env.local` at project root. Fill in real values from each dashboard.

```bash
# ── Supabase ────────────────────────────────────────────
# Get from: Supabase Dashboard → Project Settings → API
NEXT_PUBLIC_SUPABASE_URL=https://<project-ref>.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=<anon-key>
SUPABASE_SERVICE_ROLE_KEY=<service-role-key>
SUPABASE_JWT_SECRET=<jwt-secret>

# Database (Session Pooler — recommended for serverless)
DATABASE_URL=postgresql://postgres.<project-ref>:<password>@aws-0-us-east-1.pooler.supabase.com:6543/postgres

# Local dev URLs (active when `supabase start` is running)
SUPABASE_MAILPIT_URL=http://127.0.0.1:54344

# ── Stripe ──────────────────────────────────────────────
# Get from: https://dashboard.stripe.com/test/apikeys
STRIPE_PUBLISHABLE_KEY=pk_test_<key>
STRIPE_SECRET_KEY=sk_test_<key>
# Get by running: stripe listen --forward-to localhost:3000/api/webhooks/stripe --print-secret
STRIPE_WEBHOOK_SECRET=whsec_<secret>

# Product IDs — create in Stripe Dashboard → Products
STRIPE_PRODUCT_PRO=prod_<id>

# Price IDs — create under each product
STRIPE_PRICE_PRO_YEARLY=price_<id>

# ── Email (Resend) ──────────────────────────────────────
# Get from: https://resend.com/api-keys
# Used for Supabase SMTP — configure in Supabase → Project Settings → Auth → SMTP Settings
# Host: smtp.resend.com | Port: 465 | Username: resend | Password: <api-key>
RESEND_API_KEY=re_<key>

# ── App ─────────────────────────────────────────────────
APP_NAME=Your App Name
NEXT_PUBLIC_APP_NAME=Your App Name
```

---

## Step 7 — Supabase Client Files

**`lib/supabase/server.ts`** — Server Components, Route Handlers, Server Actions:
```ts
import { createServerClient } from '@supabase/ssr'
import { cookies } from 'next/headers'

export async function createClient() {
  const cookieStore = await cookies()
  return createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll: () => cookieStore.getAll(),
        setAll: (cookiesToSet) => {
          try {
            cookiesToSet.forEach(({ name, value, options }) =>
              cookieStore.set(name, value, options)
            )
          } catch {}
        },
      },
    }
  )
}
```

**`lib/supabase/client.ts`** — Client Components only:
```ts
import { createBrowserClient } from '@supabase/ssr'

export function createClient() {
  return createBrowserClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
  )
}
```

**`app/actions/auth.ts`** — magic-link sign-in and sign-out:
```ts
'use server'

import { headers } from 'next/headers'
import { redirect } from 'next/navigation'
import { createClient } from '@/lib/supabase/server'

export async function signInWithOtp(formData: FormData) {
  const email = formData.get('email') as string
  const headersList = await headers()
  const host = headersList.get('host') ?? ''
  const proto = headersList.get('x-forwarded-proto') ?? 'https'
  const origin = `${proto}://${host}`

  const supabase = await createClient()

  const { error } = await supabase.auth.signInWithOtp({
    email,
    options: {
      emailRedirectTo: `${origin}/auth/callback`,
    },
  })

  if (error) {
    return redirect(`/auth/login?error=${encodeURIComponent(error.message)}`)
  }

  return redirect('/auth/login?message=Check your email for a magic link')
}

export async function signOut() {
  const supabase = await createClient()
  await supabase.auth.signOut()
  redirect('/')
}
```

> **Why `headers()` instead of `NEXT_PUBLIC_SITE_URL`:** `NEXT_PUBLIC_` vars are inlined at build time. If the env var isn't set when the build runs, the value is `undefined` and Supabase silently falls back to the site root — sending the magic link code to `/?code=...` instead of `/auth/callback`. Using `headers()` derives the origin from the live request, so it always matches and works across production, preview, and local.

**`lib/supabase/middleware.ts`** — middleware only:
```ts
import { createServerClient } from '@supabase/ssr'
import { NextResponse, type NextRequest } from 'next/server'

export async function updateSession(request: NextRequest) {
  let supabaseResponse = NextResponse.next({ request })
  const supabase = createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll: () => request.cookies.getAll(),
        setAll: (cookiesToSet) => {
          cookiesToSet.forEach(({ name, value }) => request.cookies.set(name, value))
          supabaseResponse = NextResponse.next({ request })
          cookiesToSet.forEach(({ name, value, options }) =>
            supabaseResponse.cookies.set(name, value, options)
          )
        },
      },
    }
  )
  // NEVER add logic between createServerClient and getUser — breaks session
  const { data: { user } } = await supabase.auth.getUser()
  return { supabase, supabaseResponse, user }
}
```

---

## Step 8 — Database Schema & Migrations

> **Windows / no Docker:** Skip `supabase start`. Apply migrations directly to the hosted Supabase project via the dashboard SQL editor or `supabase db push --linked`. No local stack needed.

```bash
supabase init
supabase link --project-ref <project-ref>   # links to hosted project
supabase migration new init_schema
```

Paste into the generated migration file:

```sql
-- profiles: one row per auth user
create table public.profiles (
  id uuid references auth.users(id) on delete cascade primary key,
  email text,
  full_name text,
  membership_tier text not null default 'free' check (membership_tier in ('free', 'pro')),
  created_at timestamptz default now()
);

-- subscriptions: mirrors Stripe subscription state
create table public.subscriptions (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references auth.users(id) on delete cascade not null unique,
  stripe_customer_id text,
  stripe_subscription_id text,
  status text,
  membership_tier text not null default 'free' check (membership_tier in ('free', 'pro')),
  period_start timestamptz,
  period_end timestamptz,
  created_at timestamptz default now(),
  updated_at timestamptz default now()
);

-- RLS
alter table public.profiles enable row level security;
alter table public.subscriptions enable row level security;

create policy "Users can read own profile"
  on public.profiles for select using (auth.uid() = id);

create policy "Users can update own profile"
  on public.profiles for update using (auth.uid() = id);

create policy "Users can read own subscription"
  on public.subscriptions for select using (auth.uid() = user_id);

-- Auto-create profile on signup
create or replace function public.handle_new_user()
returns trigger language plpgsql security definer set search_path = public as $$
begin
  insert into public.profiles (id, email, full_name)
  values (new.id, new.email, new.raw_user_meta_data->>'full_name');
  return new;
end;
$$;

create trigger on_auth_user_created
  after insert on auth.users
  for each row execute procedure public.handle_new_user();
```

```bash
supabase db push --linked   # apply directly to hosted Supabase project
```

---

## Step 9 — Stripe Lazy Init & Config

**`lib/stripe/server.ts`**:
```ts
import Stripe from 'stripe'

let _stripe: Stripe | null = null

export function getStripe(): Stripe {
  if (!_stripe) {
    _stripe = new Stripe(process.env.STRIPE_SECRET_KEY!)
  }
  return _stripe
}
```

**`lib/stripe/config.ts`**:
```ts
export const PRICING = {
  pro: {
    monthly: { amount: 2900, label: '$29/mo' },
    yearly:  { amount: 27840, label: '$278.40/yr' },
  },
}

export function getPriceId(period: 'monthly' | 'yearly'): string {
  const key = period === 'monthly'
    ? process.env.STRIPE_PRICE_PRO_MONTHLY
    : process.env.STRIPE_PRICE_PRO_YEARLY
  if (!key) throw new Error(`Missing price env var for period: ${period}`)
  return key
}
```

---

## Step 10 — Webhook Events Handled

The Stripe webhook handler at `app/api/webhooks/stripe/route.ts` must handle:

| Event | Action |
|-------|--------|
| `checkout.session.completed` | **Primary tier upgrade** — upsert profile + subscription using `session.metadata.user_id` |
| `customer.subscription.created` | Upsert subscriptions + upsert profile tier to `'pro'` |
| `customer.subscription.updated` | Upsert subscriptions, recheck tier |
| `customer.subscription.deleted` | Set status to `'canceled'`, reset profile tier to `'free'` |
| `invoice.payment_succeeded` | Update `period_start` / `period_end` using `invoice.period_start` / `invoice.period_end` |
| `invoice.payment_failed` | Mark subscription status `'past_due'` |

Always verify the Stripe signature before processing. Create the Supabase admin client **inside** the handler — not at module scope.

### Critical Stripe + Supabase Webhook Gotchas

**1. Session metadata does NOT propagate to the subscription.**
Setting `metadata: { user_id }` on the checkout session only attaches it to the session, not the subscription object. The `customer.subscription.created` webhook will have empty metadata unless you also set `subscription_data.metadata`. Always set both:

```ts
// app/api/stripe/checkout/route.ts
await stripe.checkout.sessions.create({
  metadata: { user_id: user.id },                      // for checkout.session.completed
  subscription_data: { metadata: { user_id: user.id } }, // for subscription events
  ...
})
```

**2. Always upsert profiles, never update.**
The `handle_new_user` trigger may not run in production if migrations weren't applied via `supabase db push --linked`. An `update` with no matching row silently does nothing and returns `error: null`. Use upsert so the row is created if missing:

```ts
await supabase.from('profiles').upsert(
  { id: userId, membership_tier: 'pro' },
  { onConflict: 'id' }
)
```

**3. `current_period_start` / `current_period_end` may be undefined on the subscription.**
In newer Stripe API versions these fields moved off the top-level subscription object. For `invoice.payment_succeeded`, use the invoice's own period fields and look up the subscription by ID rather than retrieving it from Stripe:

```ts
await supabase.from('subscriptions').update({
  period_start: inv.period_start ? new Date(inv.period_start * 1000).toISOString() : undefined,
  period_end:   inv.period_end   ? new Date(inv.period_end   * 1000).toISOString() : undefined,
  updated_at: new Date().toISOString(),
}).eq('stripe_subscription_id', subId)
```

**4. `invoice.subscription` is typed as `string | Stripe.Subscription | null`.**
Always normalize it before calling `stripe.subscriptions.retrieve`:

```ts
const subId = typeof inv.subscription === 'string'
  ? inv.subscription
  : (inv.subscription as Stripe.Subscription | null)?.id
```

**5. Wrap the entire switch block in try/catch.**
An unhandled throw inside a `case` causes Next.js to return a 500 with no log output. Wrap the switch so errors are caught and logged:

```ts
try {
  switch (event.type) { ... }
} catch (err) {
  console.error('[webhook] handler error:', err)
  return NextResponse.json({ error: 'Handler error' }, { status: 500 })
}
```

**6. `stripe listen` must be running before testing checkout locally.**
Without it, no events reach `localhost:3000/api/webhooks/stripe` and the tier will never update. The webhook secret printed by `stripe listen` is session-specific — copy it to `STRIPE_WEBHOOK_SECRET` in `.env.local` each time you start a new listen session (or verify it matches).

---

## Step 11 — Next.js Gotchas

| Gotcha | Fix |
|--------|-----|
| `useSearchParams()` requires Suspense boundary | Wrap the component that calls it in `<Suspense>` in the parent page |
| Pages reading auth state return stale cache | Add `export const dynamic = 'force-dynamic'` to the page |
| `new Stripe()` at module scope | Breaks build when env vars absent — use lazy init (Step 9) |
| Supabase admin client at module scope | Same issue — create inside the handler function |
| Dynamic Tailwind classes purged in production | Add them to `safelist` in `tailwind.config.ts` |
| Magic link lands at `/?code=` instead of `/auth/callback` | `NEXT_PUBLIC_` vars are inlined at build time — if missing during build, `emailRedirectTo` becomes `"undefined/auth/callback"` and Supabase falls back to the site root. Use `headers()` to derive origin dynamically (see `app/actions/auth.ts` in Step 7). Never use `NEXT_PUBLIC_SITE_URL` for the auth redirect. |
| Supabase OTP rate limit blocks debugging | Free tier caps at ~3 emails/hour. Configure Resend SMTP in Supabase → Project Settings → Auth → SMTP Settings (see Step 6 for credentials). |

---

## Step 12 — Local Supabase (optional, macOS + Docker only)

> **Windows / no Docker:** Skip this step entirely. Use the hosted Supabase dashboard at `https://supabase.com/dashboard` for auth config, table editor, and logs. Magic-link emails are visible in the Auth → Logs section of the dashboard.

```bash
supabase start         # starts local stack (Docker required — macOS only)
supabase status        # prints local URLs and keys
```

Magic-link emails on local stack are captured at `SUPABASE_MAILPIT_URL` → `http://127.0.0.1:54344`.

---

## Step 13 — Stripe Local Webhook

```bash
stripe listen --forward-to localhost:3000/api/webhooks/stripe
```

Copy the printed `whsec_...` into `STRIPE_WEBHOOK_SECRET` in `.env.local`.

---

## Step 14 — Deploy to Vercel

```bash
vercel           # links repo, sets project
vercel env pull  # syncs remote env vars to .env.local
```

Add each env var to Vercel:
```bash
vercel env add NEXT_PUBLIC_SUPABASE_URL production
vercel env add NEXT_PUBLIC_SUPABASE_ANON_KEY production
vercel env add SUPABASE_SERVICE_ROLE_KEY production
vercel env add STRIPE_SECRET_KEY production
vercel env add STRIPE_WEBHOOK_SECRET production
vercel env add STRIPE_PRODUCT_PRO production
vercel env add STRIPE_PRICE_PRO_MONTHLY production
vercel env add STRIPE_PRICE_PRO_YEARLY production
vercel env add RESEND_API_KEY production
```

> **Do not add `NEXT_PUBLIC_SITE_URL`.** The auth action derives the origin from request headers at runtime — no env var needed (see Step 7 and Step 11 gotchas).

---

## Step 15 — Initialize CLAUDE.md

After scaffolding is complete, run:

```
/init
```

This generates a `CLAUDE.md` that gives Claude full context on the new project's architecture, commands, and conventions — do this before any feature work begins.

---

## Key Architecture Rules

| Rule | Detail |
|------|--------|
| Auth | Passwordless magic-link only via Supabase OTP |
| Supabase client | `server.ts` for Server Components/Actions, `client.ts` for Client Components, `middleware.ts` for middleware only |
| Stripe init | Lazy — never `new Stripe()` at module scope |
| Webhook Supabase client | Create admin client inside handler, not at module scope |
| DB tiers | `'free'` and `'pro'` only — UI can display any label |
| Tailwind dynamic classes | Add to `safelist` in `tailwind.config.ts` |
| No test suite | Verification = `npx tsc --noEmit` + `npm run build` |

---

## Verification

```bash
npm run dev          # dev server at http://localhost:3000
npx tsc --noEmit     # TypeScript check
npm run build        # must pass before merging
npm run lint         # ESLint
```

---

## Design

glass‑morphism cards, vibrant gradients, smooth hover animations design style

### huashu-design

Install: `npx skills add alchaincyf/huashu-design`

For high-quality UI/visual work in this project, use **huashu-design** — a design skill that produces agency-level output from a single prompt. It is distinct from the `frontend-design` skill (which handles React components and layouts); huashu-design is the right choice when you want opinionated, visually distinctive UI with minimal prompting.
