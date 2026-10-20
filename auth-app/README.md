# Hyperfya Auth (Next.js App Router)

This is a separate Next.js 16 App Router app added alongside the existing Node.js funnel. The root application is intentionally unchanged.

## Requirements

- Node.js 22 or newer
- Supabase project `yxrspvibmnfgnytijedd`

## Run locally

```bash
cd auth-app
cp .env.example .env.local
# Fill NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY from the Supabase project's publishable API keys.
npm install
npm run dev
```

Open http://localhost:3000. Routes: `/login`, `/signup`, and authenticated `/dashboard`.

## Environment variables

- `NEXT_PUBLIC_SUPABASE_URL`: Supabase project URL (already set in the example)
- `NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY`: project publishable key (required)
- `NEXT_PUBLIC_SITE_URL`: exact public base URL, with no trailing slash; configure this to the deployed auth app origin before production

Never add a Supabase service-role/secret key to this app or any `NEXT_PUBLIC_*` variable.

## Auth and data security

- Cookie-based sessions through `@supabase/ssr`.
- Next.js `proxy.ts` refreshes sessions; `getClaims()` validates identity. Protected dashboard also checks claims server-side.
- Email/password sign-up and sign-in; email confirmation is exchanged in `/auth/confirm`; logout is a server action.
- Redirect destinations are restricted to relative same-origin paths.
- Profile rows are protected by RLS policies that bind each row to `auth.uid()`, including both `USING` and `WITH CHECK` for updates.
- No service-role key is used by the app.

## Supabase setup

The `profiles` table and row-level-security policies have been applied to project `yxrspvibmnfgnytijedd`. The SQL migration is included in `supabase/migrations/` for source control.

In Supabase Auth URL Configuration, set the Site URL to your deployed auth app origin and add exact redirect URLs for local development (`http://localhost:3000/auth/confirm`) and production (`https://YOUR-AUTH-APP-DOMAIN/auth/confirm`). Replace the production placeholder with the actual deployment domain. Keep email confirmation enabled.

## Verification

```bash
npm run typecheck
npm run build
```

Then test signup, email confirmation, login, dashboard access in a logged-out browser, profile save, and logout. The GitHub connection cannot run the Next.js build or verify a live email delivery from this editing session.
