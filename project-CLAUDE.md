# Project CLAUDE.md — ./CLAUDE.md (repo root)
# Specific to THIS project. Keep it focused (~40 lines).
# Rule: only write what the agent CANNOT discover from the code.

# Philosophy
# [The design principles behind THIS project's architecture.]
# [Guides the agent when making judgment calls — not generic advice.]
- Convention over configuration: follow established patterns, don't invent new ones
- Thin components, thick hooks: UI components are dumb, logic lives in hooks
- Fail loudly: errors bubble up to boundaries, never swallowed silently
- Schema-first: Zod schemas define the contract, everything else derives from them

# What this project does
# [1-2 sentences. Not a sales pitch — just enough to understand the domain.]
Example: SaaS billing API that handles subscriptions, invoicing, and payment webhooks for Stripe.

# Stack
# [Only list non-obvious choices. Skip things discoverable from package.json/pyproject.toml.]
- Framework: Next.js 15 (App Router, Server Components by default)
- Database: Supabase (Postgres) — migrations in /supabase/migrations
- Auth: Supabase Auth with RLS policies on every table

# Commands
# [Only commands with non-standard flags or setup steps.]
pnpm dev          # starts dev server on port 3000
pnpm test         # runs vitest in watch mode
pnpm db:migrate   # applies pending Supabase migrations locally
pnpm db:gen       # regenerates TypeScript types from DB schema

# Architecture decisions
# [Decisions the agent would get wrong without guidance.]
- API routes use /app/api/[resource]/route.ts (1 file per resource)
- All DB queries go through /lib/db/* — never call Supabase client directly in components
- Zod schemas in /lib/schemas are the single source of truth for validation (API + forms)
- Background jobs use Inngest in /inngest/functions — not cron or setTimeout

# Patterns to follow
# [Rules that aren't enforced by linters or types.]
- Server Components fetch data; Client Components handle interactivity only
- Use server actions for mutations, not API routes
- Error handling: let errors bubble to error.tsx boundaries, don't try/catch in components

# Do NOT
# [Guardrails for things the agent tends to get wrong in this project.]
- Do not create barrel files (index.ts re-exports)
- Do not add console.log — use the logger from /lib/logger
- Do not modify /lib/db/client.ts — it configures connection pooling
