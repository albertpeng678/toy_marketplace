# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm run dev        # Start dev server (http://localhost:5173)
npm run build      # Production build
npm run build:dev  # Development build
npm run lint       # Run ESLint
npm run preview    # Preview production build
```

### Supabase Local Development

```bash
supabase start             # Start local Supabase (http://localhost:54323)
supabase db reset          # Re-apply all migrations from scratch
supabase migration new <name>  # Create a new migration file, then edit the SQL

# Deploy to remote
supabase link --project-ref $SUPABASE_PROJECT_REF
supabase db push
```

## Architecture Overview

**Stack:** React 18 + TypeScript + Vite, Tailwind CSS + shadcn/ui, React Query, React Router v6, Supabase.

### Routing (`src/App.tsx`)

All routes are defined in `App.tsx`. The root `/` redirects to `Categories`. Route structure:
- `/` → `Categories` (product browsing)
- `/product/:id` → `ProductDetail`
- `/create-listing` → `CreateListing` (choose new/edit)
- `/create-listing/new` and `/create-listing/edit/:id` → `CreateListingForm`
- `/messages` → `ConversationList`
- `/conversation/:conversationId` → `ConversationDetail`
- `/profile` → `Profile`
- `/saved-items` → `SavedItems`
- `/auth` → `Auth` (Google OAuth only)

### Supabase Integration (`src/integrations/supabase/`)

- `client.ts` — Supabase client singleton (reads `SUPABASE_URL` and `SUPABASE_PUBLISHABLE_KEY`)
- `types.ts` — Auto-generated TypeScript types from the DB schema; **do not edit manually**. Regenerate via `supabase gen types typescript`.

**Database tables:** `products`, `product_images`, `profiles`, `conversations`, `participants`, `messages`, `message_status`, `saved_products`.

**Business logic lives in Supabase RPC functions** (not the frontend). Key RPCs:
- `get_public_products(search_term?, sort_by?)` — browsing listings
- `get_public_product_detail(product_id)` — product detail page
- `create_conversation(prod_id)` — creates conversation (idempotent; returns existing if one exists)
- `get_user_conversations()` — inbox list
- `get_conversation_messages_with_read_status(conv_id)` — messages with read receipts
- `mark_conversation_read(conv_id)` — marks all messages read
- `toggle_saved_product(product_uuid)` — save/unsave a listing
- `get_user_saved_products()` — saved items list

New DB logic should be added as a new migration file, not by editing the consolidated migration.

### Authentication (`src/hooks/useAuth.tsx`)

`useAuth` hook wraps Supabase auth state. Auth is Google OAuth only. The hook is used throughout — do not call `supabase.auth` directly in components.

### Realtime Presence (`src/contexts/PresenceProvider.tsx`)

Wraps the app at root level. Tracks which users are currently online via Supabase Realtime Presence on the `global-presence` channel. Use `usePresence()` to read online status in components.

### Data Fetching Pattern

Server state is managed with **React Query**. Custom hooks in `src/hooks/` (e.g., `usePublicProducts`, `useUserProducts`, `useSavedProducts`) call Supabase RPCs and wrap the results. Add new data-fetching logic as new hooks following this pattern rather than fetching directly in page components.

### Component Library

UI primitives are shadcn/ui components in `src/components/ui/`. Do not modify these files — add new shadcn components via `npx shadcn-ui@latest add <component>`. App-specific components live in `src/components/` (e.g., `NavigationBar`, `ProductCard`, `HamburgerMenu`, `FilterSheet`).

## Pending Work

- Image upload in conversation (see `README_TODO.md`)
