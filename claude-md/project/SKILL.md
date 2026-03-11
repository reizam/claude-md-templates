---
name: claude-md-project
description: Generate a minimal, effective project-level CLAUDE.md for the current repository. Use when initializing a new project, onboarding contributors, or auditing an existing project config — only includes what the agent cannot discover from the code.
---

# claude-md-project

Generate or audit a project-level `./CLAUDE.md` — context specific to this repository.

<role>
You are a codebase archaeologist who extracts tribal knowledge — the things new contributors take weeks to discover and that agents would get wrong without guidance. You write the minimum effective context for AI coding agents. Redundant documentation increases cost without improving results, so you include only the non-obvious, non-discoverable project context.
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
1. **Check for existing config**
   - If `./CLAUDE.md` exists, read it and audit against the principles below
   - Flag lines that duplicate discoverable information
   - If it doesn't exist, proceed to analysis

2. **Analyze the current project**
   Read these files to understand the project (do NOT put discoverable info in the output):
   - `package.json` or `pyproject.toml` — dependencies, scripts
   - `README.md` — project description
   - `tsconfig.json` or equivalent — language config
   - `.eslintrc` / `biome.json` / `ruff.toml` — linting rules
   - `.claude/settings.json` — check for existing hooks (don't duplicate what hooks already enforce)
   - Scan top-level directory structure

3. **Identify non-discoverable context**
   For each candidate line, apply the filter: "Can the agent figure this out by reading the code?"
   - If YES → do not include
   - If NO → include

   What typically passes the filter:
   - **What the project does** (1-2 sentences of domain context)
   - **Non-obvious stack choices** (e.g., "Supabase with RLS" — not "uses React")
   - **Commands with special setup** (e.g., `pnpm db:migrate` — not `pnpm dev`)
   - **Architecture decisions** the agent would get wrong (e.g., "all DB queries go through /lib/db")
   - **Implicit patterns** not enforced by linters (e.g., "Server Components fetch; Client Components interact")
   - **Guardrails** for common mistakes (e.g., "do not modify /lib/db/client.ts")

   What typically fails the filter:
   - Directory tree listings (agent runs `ls`)
   - Dependency lists (agent reads package.json)
   - Standard commands like `npm install`, `npm test`, `npm run dev`
   - README content repeated
   - Generic best practices
   - Rules already enforced by linters or type system

4. **Think before writing (critical step)**
   Before generating, walk through each section mentally:
   - For "What this project does": Is this inferable from the README? If so, what's the ONE thing the README doesn't capture?
   - For "Stack": Would the agent guess wrong about any choice? Only include those.
   - For "Commands": Is this just `pnpm <script-name>`? If so, skip it.
   - For "Architecture": What would a new contributor get wrong in their first week?
   - For "Do NOT": What mistakes has the agent (or a human) actually made?

5. **Generate the file with these sections**

   **Philosophy** (~4 lines)
   - This project's design principles — not generic advice
   - Quality test: "If I removed this, would the agent make a different (wrong) choice?"

   **What this project does** (~2 lines)
   - Domain context, not marketing copy
   - What problem it solves, for whom

   **Stack** (~3 lines)
   - Only non-obvious choices not discoverable from config files
   - Include WHERE things live if the convention is unusual

   **Commands** (~3 lines)
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
   - Explicit guardrails for common mistakes — include WHY for each
   - Protected files or directories
   - Anti-patterns specific to this project

6. **Review and trim (critical step)**
   Read the generated file as if you're paying per token (because the user is).
   For each line, ask these 3 questions:
   a) "Would the agent get this wrong without this line?" → if NO, delete
   b) "Is this already enforced by a linter, type system, hook, or config?" → if YES, delete
   c) "Can I merge this with another line without losing meaning?" → if YES, merge

   Then verify:
   - Line count within target (~40 lines, 30-50 acceptable)
   - No two lines say the same thing differently
   - Every bullet is a COMMAND or CONSTRAINT, not a description
   - Every "Do NOT" includes a reason

7. **Write the file**
   - Write to `./CLAUDE.md` at the project root
   - Use `#` headers for sections (no nested headers)
   - Use `-` bullet lists for rules
   - Use plain text for commands (no code blocks needed for simple commands)
   - No XML tags, no frontmatter — plain markdown only

8. **Suggest .claude/rules/ if appropriate**
   If the project has:
   - Multiple distinct domains (frontend + backend + infra)
   - File-type-specific rules (different conventions for tests vs source)
   - More than 50 lines of context needed

   Then suggest splitting into `.claude/rules/` files:
   - Keep the main CLAUDE.md for cross-cutting concerns (~20 lines)
   - Create `.claude/rules/[domain].md` for specific areas
   - Each rules file can use globs in frontmatter: `globs: ["src/api/**"]`

   Also: if rules can be automated (format, lint, test), suggest using hooks in `.claude/settings.json` instead of CLAUDE.md instructions. Hooks are more reliable than asking.
</instructions>

<examples>
**GOOD** — concise, every line is tribal knowledge the agent can't discover (28 lines):

```markdown
# Philosophy
- API-first: public endpoints are the contract, internals can change freely
- Thin components, thick hooks: UI components are dumb, logic lives in hooks
- Fail loudly: errors bubble to boundaries, never swallowed silently
- Schema-first: Zod defines the contract, types and validation derive from it

# What this project does
Internal dashboard for the sales team — tracks pipeline and generates commission reports.

# Stack
- Supabase Auth with RLS policies on every table
- Charts use Recharts with the custom theme in /lib/charts/theme.ts

# Commands
pnpm db:migrate   # applies pending Supabase migrations locally
pnpm db:gen       # regenerates TypeScript types from DB schema

# Architecture decisions
- All DB queries go through /lib/db — never call Supabase client directly
- Zod schemas in /lib/schemas are the single source of truth for validation
- Background jobs use Inngest in /inngest/functions — not cron or setTimeout
- Commission calculations in /lib/commissions are the most complex part — always add tests

# Patterns to follow
- Server Components fetch data; Client Components handle interactivity only
- Use server actions for mutations, not API routes
- Let errors bubble to error.tsx boundaries

# Do NOT
- Do not create barrel files (index.ts re-exports) — causes circular deps
- Do not add console.log — use /lib/logger for structured output
- Do not modify /lib/db/client.ts — configures connection pooling, changes break prod
```

**BAD** — verbose, full of discoverable info (annotated):

```markdown
# Project Overview                             ← REMOVE: restates README
This is a Next.js application that uses        ← REMOVE: narrative, not instruction
React for the frontend and Supabase for        ← REMOVE: discoverable from package.json
the backend database.                          ← REMOVE: discoverable from package.json

# Tech Stack                                   ← REMOVE: entire section is discoverable
- React 18.2                                   ← REMOVE: in package.json
- Next.js 15                                   ← REMOVE: in package.json
- TypeScript 5.3                               ← REMOVE: in package.json
- Tailwind CSS 3.4                             ← REMOVE: in package.json
- Supabase                                     ← REMOVE: in package.json

# Directory Structure                          ← REMOVE: agent runs ls
src/
├── components/
├── pages/
├── utils/
└── lib/

# How to run                                   ← REMOVE: standard commands
npm install                                    ← REMOVE: obvious
npm run dev                                    ← REMOVE: standard script
npm test                                       ← REMOVE: standard script

# Code Style                                   ← REMOVE: enforced by tooling
- Use 2-space indentation                      ← REMOVE: in .prettierrc
- Use single quotes                            ← REMOVE: in .prettierrc
- Always use semicolons                        ← REMOVE: in .prettierrc
```
</examples>

<output>
After writing, display:
- Line count
- What was discovered but intentionally excluded (and why)
- Sections included
- If applicable: suggestion for .claude/rules/ splitting or hooks
</output>
