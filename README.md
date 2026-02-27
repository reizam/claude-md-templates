# CLAUDE.md Templates

Ready-to-use templates for structuring your CLAUDE.md files — based on findings from the ETH Zurich research paper ["Evaluating AGENTS.md: Are Repository-Level Context Files Helpful for Coding Agents?"](https://arxiv.org/abs/2602.11988).

> **TL;DR** — LLM-generated context files: **-3% performance, +20% cost**. Human-written ones: slight gain. Write less, but better.

## The golden rule

**Write only what the agent cannot discover on its own.**

The paper shows that:
- Context files that duplicate existing documentation **hurt** performance
- Agents **do follow** instructions — bad instructions actively make things worse
- Repository overviews are **not effective** (agents can `ls` and `find` on their own)
- More tokens = more cost, not more accuracy

## The 2 files you need

| File | Location | Scope | Target |
|---|---|---|---|
| [`global-CLAUDE.md`](./global-CLAUDE.md) | `~/.claude/CLAUDE.md` | All projects | ~30 lines |
| [`project-CLAUDE.md`](./project-CLAUDE.md) | `./CLAUDE.md` (repo root) | This project | ~40 lines |

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
- Explicit "do not" guardrails

## Why Philosophy matters

The Philosophy section shapes **how** the agent thinks, not just what it does. Without it, the agent defaults to generic patterns. With it, the generated code feels like **yours**.

| Level | What it captures | Example |
|---|---|---|
| Global | Your engineering identity | "YAGNI: only implement what's needed NOW" |
| Project | This project's design principles | "Schema-first: Zod defines the contract, everything derives from it" |

3-5 bullet points. These are the tiebreakers when the agent faces ambiguous decisions.

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
- `/claude-md-project` — reads your repo's config files and generates a `./CLAUDE.md`

## What NOT to write

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
