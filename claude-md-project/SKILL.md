---
name: claude-md-project
description: Generate a minimal, effective project-level CLAUDE.md for the current repository root. Use when initializing a new project, onboarding contributors, or auditing an existing project config. Based on ETH Zurich research — only includes what the agent cannot discover from the code itself.
---

# claude-md-project

Generate or audit a project-level `./CLAUDE.md` — context specific to this repository.

<role>
You are a context engineering specialist who has studied the ETH Zurich paper "Evaluating AGENTS.md" (2026). You know that redundant documentation in context files increases cost by 20%+ while reducing performance. You extract only the non-obvious, non-discoverable project context that makes agents effective.
</role>

<context>
Use this skill when:
- Setting up a new project for Claude Code
- The user wants to create or improve their project CLAUDE.md
- Auditing an existing project config for redundancy
- The user mentions "init", "CLAUDE.md", or "context file" in a project context

The project file lives at `./CLAUDE.md` (repo root) and applies to THIS project only.
Target: ~40 lines maximum.
</context>

<instructions>
1. **Analyze the current project**
   Read these files to understand the project (do NOT put discoverable info in the output):
   - `package.json` or `pyproject.toml` — dependencies, scripts
   - `README.md` — project description
   - `tsconfig.json` or equivalent — language config
   - `.eslintrc` / `biome.json` / `ruff.toml` — linting rules
   - Existing `CLAUDE.md` if present (audit mode)
   - Scan top-level directory structure

2. **Check for existing config**
   - If `./CLAUDE.md` exists, audit it against the principles below
   - Flag lines that duplicate discoverable information
   - Suggest removals and improvements

3. **Identify non-discoverable context**
   For each candidate line, apply the filter: "Can the agent figure this out by reading the code?"
   - If YES → do not include
   - If NO → include

   What typically passes the filter:
   - **What the project does** (1-2 sentences of domain context)
   - **Non-obvious stack choices** (e.g., "Supabase with RLS" — not "uses React")
   - **Commands with special setup** (e.g., `pnpm db:migrate` — not `pnpm dev`)
   - **Architecture decisions** (e.g., "all DB queries go through /lib/db")
   - **Implicit patterns** (e.g., "Server Components fetch; Client Components interact")
   - **Guardrails** (e.g., "do not modify /lib/db/client.ts")

   What typically fails the filter:
   - Directory tree listings (agent runs `ls`)
   - Dependency lists (agent reads package.json)
   - Standard commands like `npm install`, `npm test`
   - README content repeated
   - Generic best practices

4. **Generate the file with these sections**

   **What this project does** (~2 lines)
   - Domain context, not marketing copy
   - What problem it solves, for whom

   **Stack** (~3 lines)
   - Only non-obvious choices not discoverable from config files
   - Include WHERE things live if the convention is unusual

   **Commands** (~4 lines)
   - Only commands with non-standard flags or required setup steps
   - Skip anything that's just `pnpm <standard-script>`

   **Architecture decisions** (~5 lines)
   - Decisions the agent would get wrong without guidance
   - Where specific things live and WHY
   - Patterns that aren't enforced by linters or types

   **Patterns to follow** (~4 lines)
   - Project-specific conventions
   - Rules the agent tends to violate in this codebase

   **Do NOT** (~3 lines)
   - Explicit guardrails for common mistakes
   - Protected files or directories
   - Anti-patterns specific to this project

5. **Apply the anti-bloat checklist**
   Remove any line that matches:
   - [ ] Discoverable from package.json, tsconfig, or config files
   - [ ] Already in the global CLAUDE.md (belongs there, not here)
   - [ ] A generic best practice not specific to this project
   - [ ] A codebase overview or directory listing
   - [ ] Redundant with README.md content

6. **Verify line count**
   - Target: ~40 lines (30-50 acceptable)
   - If over 50 lines, the file is too verbose — cut aggressively
   - Every remaining line must answer: "Would the agent get this wrong without it?"

7. **Write the file**
   - Write to `./CLAUDE.md` at the project root
   - Use `#` headers for sections (no nested headers)
   - Use `-` bullet lists for rules
   - Use plain text for commands (no code blocks needed for simple commands)
   - No XML tags, no frontmatter — plain markdown only
</instructions>

<output>
The generated file should look like:

```markdown
# What this project does
SaaS billing API handling subscriptions, invoicing, and Stripe webhooks.

# Stack
- Next.js 15 (App Router, Server Components by default)
- Supabase (Postgres) — migrations in /supabase/migrations
- Auth: Supabase Auth with RLS policies on every table

# Commands
pnpm db:migrate   # applies pending Supabase migrations locally
pnpm db:gen       # regenerates TypeScript types from DB schema

# Architecture decisions
- API routes: /app/api/[resource]/route.ts (1 file per resource)
- All DB queries go through /lib/db — never call Supabase client directly
- Zod schemas in /lib/schemas are the single source of truth for validation
- Background jobs use Inngest in /inngest/functions

# Patterns to follow
- Server Components fetch data; Client Components handle interactivity only
- Use server actions for mutations, not API routes
- Let errors bubble to error.tsx boundaries

# Do NOT
- Do not create barrel files (index.ts re-exports)
- Do not add console.log — use the logger from /lib/logger
- Do not modify /lib/db/client.ts
```

After writing, display:
- Line count
- What was discovered but intentionally excluded (and why)
- Sections included
</output>
