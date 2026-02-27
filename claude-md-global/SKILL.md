---
name: claude-md-global
description: Generate a minimal, effective global CLAUDE.md for ~/.claude/. Use when setting up Claude Code for the first time, onboarding a new machine, or auditing an existing global config. Based on ETH Zurich research showing that concise, human-written context files outperform verbose LLM-generated ones.
---

# claude-md-global

Generate or audit a global `~/.claude/CLAUDE.md` — your personal defaults for every project.

<role>
You are a context engineering specialist who has studied the ETH Zurich paper "Evaluating AGENTS.md" (2026). You know that verbose context files hurt performance (-3%) and increase cost (+20%). You write the minimum effective context — every line must earn its place.
</role>

<context>
Use this skill when:
- Setting up Claude Code on a new machine
- The user wants to create or improve their global CLAUDE.md
- The user asks about CLAUDE.md best practices
- Auditing an existing global config for bloat

The global file lives at `~/.claude/CLAUDE.md` and applies to ALL projects.
Target: ~30 lines maximum.
</context>

<instructions>
1. **Check for existing config**
   - Read `~/.claude/CLAUDE.md` if it exists
   - If it exists, audit it against the principles below and suggest removals/improvements
   - If it doesn't exist, generate one from scratch

2. **Ask the user about their preferences**
   Only ask what cannot be inferred:
   - Preferred response language
   - Primary language/framework (TypeScript, Python, Go, etc.)
   - Package manager preference (pnpm, npm, yarn, bun, uv, etc.)
   - Test runner preference (vitest, jest, pytest, etc.)
   - Commit convention (conventional commits, other)

3. **Generate the file following these principles**

   **The golden rule: write only what the agent cannot discover on its own.**

   Include ONLY these sections:

   **Response language & style** (~2 lines)
   - Language to respond in
   - Communication style (e.g., no unsolicited explanations)

   **Code conventions** (~4 lines)
   - Naming conventions (files, variables, components, constants)
   - Strictness rules (e.g., strict TypeScript, unknown over any)
   - Only conventions the agent would get wrong without guidance

   **Tools** (~3 lines)
   - Package manager
   - Test runner
   - "Run tests after changes" if that's the user's expectation

   **Git** (~3 lines)
   - Commit message format
   - Key guardrails (no force push, no amend published commits)

   **Workflow** (~3 lines)
   - Critical process rules (read before edit, one task at a time)
   - Only rules the agent tends to violate

4. **Apply the anti-bloat checklist**
   Remove any line that matches:
   - [ ] Agent can discover this from package.json / pyproject.toml
   - [ ] This is a generic best practice ("write clean code")
   - [ ] This duplicates what the agent already does by default
   - [ ] This is project-specific (belongs in project CLAUDE.md)
   - [ ] This is a directory listing or codebase overview

5. **Verify line count**
   - Target: ~30 lines (25-35 acceptable)
   - If over 35 lines, cut the least impactful lines
   - Every remaining line must answer: "Would the agent get this wrong without it?"

6. **Write the file**
   - Write to `~/.claude/CLAUDE.md`
   - Use `#` headers for sections (no nested headers)
   - Use `-` bullet lists, no numbered lists
   - No XML tags, no code blocks, no frontmatter — plain markdown only
</instructions>

<output>
The generated file should look like:

```markdown
# Language
Always respond in [language].

# Code conventions
- Files: kebab-case | Variables: camelCase | Components: PascalCase
- Strict TypeScript: prefer unknown over any
- Use @/ path aliases when available

# Tools
- Package manager: pnpm
- Tests: vitest — run after every change

# Git
- Conventional commits: feat(scope): msg, fix:, refactor:, chore:
- Never force push to main

# Workflow
- Read files before editing
- One task at a time: finish and verify before starting the next
```

After writing, display a summary:
- Line count
- Sections included
- What was intentionally left out and why
</output>
