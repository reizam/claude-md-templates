# CLAUDE.md Templates

Ready-to-use templates and skills for writing effective CLAUDE.md files — the context files that make AI coding agents work like you.

> **The golden rule** — Write only what the agent cannot discover on its own. Every other line is noise that costs tokens and hurts focus.

## Why this matters

Agents follow your instructions literally. Bad instructions — generic advice, discoverable information, verbose descriptions — actively make things worse. Research confirms that verbose, auto-generated context files reduce performance while increasing cost. The fix is simple: write less, but write what matters.

## The files you need

| File | Location | Scope | Target |
|---|---|---|---|
| [`global-CLAUDE.md`](./global-CLAUDE.md) | `~/.claude/CLAUDE.md` | All projects | ~30 lines |
| [`project-CLAUDE.md`](./project-CLAUDE.md) | `./CLAUDE.md` (repo root) | This project | ~40 lines |
| [`.claude/rules/*.md`](#scaling-with-rules-files) | `.claude/rules/` | Targeted file patterns | ~10 lines each |

### Global — your engineering identity

> [`global-CLAUDE.md`](./global-CLAUDE.md)

- **Philosophy** — DRY, YAGNI, simplicity over cleverness
- Response language and style
- Code conventions (naming, strictness)
- Preferred tools (package manager, test runner)
- Git commit conventions
- Workflow rules

### Project — this repo's context

> [`project-CLAUDE.md`](./project-CLAUDE.md)

- **Philosophy** — this project's design principles
- What the project does (1-2 sentences)
- Non-obvious stack choices
- Commands with special setup
- Architecture decisions
- Patterns to follow
- Explicit "do not" guardrails (with reasons)

## Why Philosophy matters

The Philosophy section shapes **how** the agent thinks, not just what it does. Without it, the agent defaults to generic patterns. With it, the generated code feels like **yours**.

| Level | What it captures | Example |
|---|---|---|
| Global | Your engineering identity | "YAGNI: only implement what's needed NOW" |
| Project | This project's design principles | "Schema-first: Zod defines the contract, everything derives from it" |

3-5 bullet points. These are the tiebreakers when the agent faces ambiguous decisions.

**Quality test**: "Would two reasonable engineers disagree on this?" If not, it's too generic.

## Should this line be in your CLAUDE.md?

```
Can the agent discover this from code or config?
├── YES → Don't include
└── NO
    └── Is it a generic best practice?
        ├── YES → Don't include
        └── NO
            └── Would the agent get it wrong without it?
                ├── YES → Include ✓
                └── NO
                    └── Is it enforced by a linter, type system, or hook?
                        ├── YES → Don't include
                        └── NO → Include ✓
```

## Bad vs. Good

<details>
<summary>A bloated CLAUDE.md (52 lines — mostly noise)</summary>

```markdown
# Project Overview
This is a Next.js application that uses React for the frontend
and Supabase for the backend database.

# Tech Stack
- React 18.2
- Next.js 15
- TypeScript 5.3
- Tailwind CSS 3.4
- Supabase

# Directory Structure
src/
├── components/
├── pages/
├── utils/
└── lib/

# How to run
npm install
npm run dev
npm test

# Code Style
- Use meaningful variable names
- Write clean, maintainable code
- Use 2-space indentation
- Always use semicolons
- Use single quotes

# Architecture
- Components go in src/components
- Utilities go in src/utils
```

**Problems:**
- "Tech Stack" → agent reads `package.json`
- "Directory Structure" → agent runs `ls`
- "How to run" → standard commands, zero value
- "Code Style" → generic advice + enforced by Prettier
- "Project Overview" → restates the README
- "Architecture" → agent infers from directory names

</details>

<details>
<summary>The same project, effective (18 lines — all signal)</summary>

```markdown
# What this project does
Internal dashboard for the sales team — tracks pipeline and generates commission reports.

# Stack
- Supabase Auth with RLS — every table has row-level policies
- Charts use Recharts with the custom theme in /lib/charts/theme.ts

# Architecture
- Commission calculations in /lib/commissions are the most complex part — always add tests
- API routes return DTOs, never raw DB rows — see /lib/dto for the pattern

# Do NOT
- Do not use `any` in /lib/commissions — strict types prevent rounding bugs
- Do not query Supabase directly in components — use the hooks in /lib/hooks
```

**Why this works:** every line is something the agent would get wrong without it.

</details>

## What NOT to write

| Avoid | Why |
|---|---|
| Directory tree listings | Agent can run `ls` and `find` |
| Listing every dependency | Agent reads `package.json` / `pyproject.toml` |
| Explaining obvious patterns | Agent infers from existing code |
| Copy-pasting README content | Redundancy increases cost, not accuracy |
| Generic best practices | "Write clean code" adds nothing |
| Long context files (100+ lines) | More tokens = more cost, less focus |
| Rules already enforced by linters | Agent reads config files |

## CLAUDE.md vs. hooks

Some rules are better enforced by [hooks](https://docs.claude.com/en/hooks) than by CLAUDE.md:

| Rule | CLAUDE.md | Hook |
|---|---|---|
| "Run tests after changes" | Agent may forget | Hook guarantees it |
| "Format code before committing" | Agent may skip | Hook automates it |
| "Never push to main" | Agent respects it | Git hook blocks it |

**Rule of thumb**: if it can be automated, prefer a hook. Use CLAUDE.md for judgment calls that can't be scripted.

## Scaling with rules files

For projects that need more than ~50 lines of context, split into `.claude/rules/` files:

```
.claude/rules/
├── api.md         (globs: src/api/**)
├── testing.md     (globs: **/*.test.*)
└── database.md    (globs: src/db/**)
```

Each rules file uses frontmatter globs to target specific file patterns. Keep the main `CLAUDE.md` for cross-cutting concerns only. See [`examples/rules/`](./examples/rules/) for ready-to-use examples.

## Quick start

### Option 1 — Copy the templates

```bash
# Global (run once)
cp global-CLAUDE.md ~/.claude/CLAUDE.md

# Project (run per repo)
cp project-CLAUDE.md /path/to/your/project/CLAUDE.md
```

Then edit each file: fill in your values, delete the comment lines (`# [...]`).

### Option 2 — Install as skills

The skills **generate** CLAUDE.md files tailored to your setup — they analyze your project and only include what the agent can't discover on its own.

```bash
npx skills add reizam/claude-md-templates
```

Then use:
- `/claude-md-global` — interactively generates your `~/.claude/CLAUDE.md`
- `/claude-md-project` — analyzes your repo and generates a `./CLAUDE.md`
- `/claude-md-audit` — audits an existing CLAUDE.md for bloat and suggests cuts

## FAQ

**Should I use CLAUDE.md or AGENTS.md?**
Both work. CLAUDE.md is Claude-specific, AGENTS.md follows the cross-agent specification. The same principles apply to both — write only what the agent can't discover.

**My CLAUDE.md is over 100 lines. Is that bad?**
Probably. Longer files increase cost without improving results. Split into `.claude/rules/` files that target specific globs, and keep the main file for cross-cutting concerns.

**Should I commit CLAUDE.md to git?**
Project CLAUDE.md: yes — it's project documentation. Global CLAUDE.md: no — it's personal preference (lives in `~/.claude/`).

**What about .claude/rules/?**
For projects that need more than ~50 lines of context, split domain-specific rules into `.claude/rules/[domain].md`. Each file can target specific file globs. The main CLAUDE.md stays short.

**How is this different from code comments?**
CLAUDE.md captures cross-cutting concerns that don't belong in any single file: architecture decisions, project-wide patterns, workflow rules. Comments are local; CLAUDE.md is global.

## References

- [Anthropic — Using CLAUDE.md files](https://claude.com/blog/using-claude-md-files)
- [Anthropic — Prompt engineering best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview)
- [AGENTS.md specification](https://agents.md/)
- [Evaluating AGENTS.md](https://arxiv.org/abs/2602.11988) — Gloaguen, Mündler, Müller, Raychev, Vechev (ETH Zurich, 2026)
