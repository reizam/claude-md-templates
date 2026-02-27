# Global CLAUDE.md — ~/.claude/CLAUDE.md
# Applies to ALL your projects. Keep it short (~30 lines).
# Rule: only write what the agent CANNOT discover on its own.

# Language & response style
Always respond in English.
Generate code, comments, and commits in English.

# Code conventions
- Naming: files in kebab-case, variables in camelCase, components in PascalCase
- Prefer `function` declarations over arrow functions (except callbacks)
- Strict TypeScript: prefer `unknown` over `any`, no implicit returns

# Tools
- Package manager: pnpm (never npm or yarn)
- Test runner: vitest
- Run tests after every change

# Git
- Conventional commits: feat(scope): msg, fix:, refactor:, chore:
- Never amend published commits
- Never force push to main

# Workflow
- Read files before editing — understand context first
- One task at a time: finish and verify before starting the next
- Do not add explanations after completing work unless asked
