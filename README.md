# CLAUDE.md Templates

Two ready-to-use templates + two installable skills for structuring your CLAUDE.md files — based on findings from the ETH Zurich research paper ["Evaluating AGENTS.md: Are Repository-Level Context Files Helpful for Coding Agents?"](https://arxiv.org/abs/2602.11988).

## Key findings from the paper

- **LLM-generated context files**: -3% performance, +20% cost
- **Human-written context files**: slight performance gain
- Context files that duplicate existing documentation **hurt** performance
- Agents **do follow** instructions — which means bad instructions actively make things worse
- Repository overviews in context files are **not effective** (agents can explore on their own)

**The golden rule: write only what the agent cannot discover on its own.**

## The 2 files you need

### 1. Global — `~/.claude/CLAUDE.md`

Your personal defaults that apply to **every** project.

→ [`global-CLAUDE.md`](./global-CLAUDE.md)

**What goes here:**
- **Philosophy** — your core engineering values (DRY, YAGNI, simplicity over cleverness)
- Response language and style preferences
- Code conventions you always want (naming, strictness)
- Preferred tools (package manager, test runner)
- Git commit conventions
- Workflow rules (read before editing, one task at a time)

**~30 lines. If it's discoverable from `package.json`, don't repeat it.**

### 2. Project — `./CLAUDE.md` (repo root)

Context specific to **this** repository.

→ [`project-CLAUDE.md`](./project-CLAUDE.md)

**What goes here:**
- **Philosophy** — design principles behind THIS project's architecture
- What the project does (1-2 sentences)
- Non-obvious stack choices
- Essential commands (only if they have special flags/setup)
- Architecture decisions the agent would get wrong
- Patterns to follow that aren't enforced by linters
- Explicit "do not" guardrails

**~40 lines. If the agent can figure it out by reading the code, skip it.**

## How to use

1. **Fork this repo**
2. **Copy `global-CLAUDE.md`** to `~/.claude/CLAUDE.md` and edit to match your preferences
3. **Copy `project-CLAUDE.md`** to the root of your project as `CLAUDE.md` and fill in your specifics
4. **Delete every comment line** (lines starting with `#` followed by `[...]`) — they're just guides

## Why Philosophy matters

The Philosophy section is the most important part of your CLAUDE.md. It shapes **how** the agent thinks, not just what it does. Without it, the agent defaults to generic patterns. With it, the code feels like **yours**.

**Global philosophy** = your engineering identity (DRY, YAGNI, simplicity over cleverness)
**Project philosophy** = this project's design principles (schema-first, fail loudly, thin components)

Keep it to 3-5 bullet points. These are the tiebreakers when the agent faces ambiguous decisions.

## Install as skills

Instead of copy-pasting, you can install these as Claude Code skills that **generate** CLAUDE.md files tailored to your setup:

```bash
npx skills add reizam/claude-md-templates
```

This installs two skills:
- `/claude-md-global` — interactively generates your `~/.claude/CLAUDE.md`
- `/claude-md-project` — analyzes your repo and generates a project `./CLAUDE.md`

The skills apply the same anti-bloat principles from the paper: they read your project's config files first and only include what the agent can't discover on its own.

## What NOT to write

Based on the paper, these patterns **hurt** performance:

| Avoid | Why |
|---|---|
| Directory tree listings | Agent can run `ls` and `find` |
| Listing every dependency | Agent reads `package.json` / `pyproject.toml` |
| Explaining obvious patterns | Agent infers from existing code |
| Copy-pasting README content | Redundancy increases cost, not accuracy |
| Generic best practices | "Write clean code" adds nothing |
| Long context files (100+ lines) | More tokens = more cost, less focus |

## References

- Paper: [Evaluating AGENTS.md](https://arxiv.org/abs/2602.11988) — Gloaguen, Mündler, Müller, Raychev, Vechev (ETH Zurich, 2026)
- [Anthropic — Using CLAUDE.md files](https://claude.com/blog/using-claude-md-files)
- [AGENTS.md specification](https://agents.md/)
