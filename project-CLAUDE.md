# Project CLAUDE.md — ./CLAUDE.md (repo root)
# Specific to THIS project. Keep it focused (~40 lines).
# Rule: only write what the agent CANNOT discover from the code.

# Philosophy
# [This project's design principles — not generic advice.]
# [Test: "If I removed this, would the agent make a different (wrong) choice?" If not, cut it.]
- Convention over configuration: follow established patterns, don't invent new ones
- Thin components, thick hooks: UI components are dumb, logic lives in hooks
- Fail loudly: errors bubble up to boundaries, never swallowed silently
- Schema-first: Zod schemas define the contract, everything else derives from them

# What this project does
# [1-2 sentences. Domain context — not marketing copy, not a README summary.]
SaaS billing API that handles subscriptions, invoicing, and payment webhooks for Stripe.

# Stack
# [Only non-obvious choices. Skip things discoverable from package.json/pyproject.toml.]
- Supabase (Postgres) with RLS policies on every table — migrations in /supabase/migrations
- Auth: Supabase Auth — every new table needs RLS policies before merging

# Commands
# [Only commands with non-standard setup. Skip pnpm dev, pnpm test, etc.]
pnpm db:migrate   # applies pending Supabase migrations locally
pnpm db:gen       # regenerates TypeScript types from DB schema

# Architecture decisions
# [Decisions the agent would get wrong without guidance. Include WHY.]
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
# [Guardrails — always include WHY so the agent can generalize.]
- Do not create barrel files (index.ts re-exports) — causes circular deps and hides import costs
- Do not add console.log — use the logger from /lib/logger for structured output
- Do not modify /lib/db/client.ts — configures connection pooling, changes break prod

# [If this file exceeds ~50 lines, split into .claude/rules/ files with globs.]
# [See examples/rules/ in this repo for the pattern.]
