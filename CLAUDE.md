# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

```bash
# Development
npm run dev          # Start Vite dev server (port 8080)

# Building
npm run build        # Production build
npm run build:dev    # Development build

# Code Quality
npm run lint         # ESLint code checking

# Preview
npm run preview      # Preview production build
```

## Project Architecture

This is a React 18 + TypeScript marketplace application built with Vite, using Supabase for backend services and TailwindCSS for styling.

### Core Technologies
- **Frontend**: React 18, TypeScript, Vite
- **UI Framework**: shadcn/ui components with Radix UI primitives
- **Styling**: TailwindCSS with CSS variables for theming
- **Backend**: Supabase (database, auth, real-time)
- **Data Management**: Tanstack React Query for server state
- **Routing**: React Router DOM

### Key Directory Structure
```
/src
├── components/ui/     # shadcn/ui component library (50+ components)
├── components/        # App-specific components (ProductCard, NavigationBar, etc.)
├── pages/            # Route components (Auth, Categories, ProductDetail, etc.)
├── hooks/            # Custom hooks for data management (useAuth, usePublicProducts, etc.)
├── contexts/         # React contexts (PresenceProvider for real-time features)
├── integrations/supabase/  # Supabase client and configurations
└── lib/              # Utilities (imageUtils, utils)
```

### Application Features
- **User Authentication** via Supabase
- **Product Marketplace** with categories and listings
- **Real-time Messaging** system between users
- **Saved Items** functionality
- **Real-time Presence** tracking for online users

### Development Patterns
- **Path Aliases**: `@/` maps to `src/` directory
- **TypeScript**: Strict typing throughout
- **Custom Hooks**: Business logic encapsulated in reusable hooks
- **React Query**: Server state management with caching
- **Component Composition**: shadcn/ui + custom components

### Database & Backend
- **Supabase PostgreSQL** with migrations in `/supabase/migrations/`
- **Real-time features** using Supabase channels for presence and messaging
- **Authentication** handled by Supabase built-in auth

### Styling Conventions
- **TailwindCSS** utility classes
- **Dark mode** support via `next-themes`
- **Responsive design** with mobile-first approach
- **CSS variables** for consistent theming across components

## Supabase Development

### Local Development
```bash
# Re-apply existing migrations 
supabase logout
supabase login
supabase db reset
supabase start 
# Access local server at http://localhost:54323
```

### Migration Management
```bash
# Add new migrations 
supabase migration new xxxxx_table
# Update the SQL in the migration file
supabase db reset
```

### Production Deployment
```bash
supabase logout
supabase login
SUPABASE_PROJECT_REF=XXXXX
supabase link --debug --project-ref $SUPABASE_PROJECT_REF
supabase db push
```

### Supabase Best Practices
- Always look up existing RPC functions in Supabase and consider reusing them
- When accessing data across multiple tables, create RPC functions to avoid RLS permission issues
- Use camelCase for naming files (e.g., CreateListingForm.tsx)
- Never commit PII information such as email addresses

## Environment Configuration

### Supabase Credentials
- `clients.ts`: SUPABASE_URL, SUPABASE_PUBLISHABLE_KEY
- `.mcp.json`: PROJECT_REF, SUPABASE_ACCESS_TOKEN
- `config.toml`: PROJECT_ID

### Local vs Production
The app automatically detects local development and uses appropriate Supabase endpoints:
- Local: `http://127.0.0.1:54321`
- Production: `https://aonhrhzuntjkskglqdwv.supabase.co`