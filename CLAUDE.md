# What this project does
Templates and skills for generating effective CLAUDE.md context files - the minimum instructions that make AI coding agents work like you.

# Philosophy
- Less is more: every line must earn its place — if the agent can discover it, don't write it
- Show, don't tell: concrete examples beat abstract rules
- Opinionated over neutral: templates should guide decisions, not list options
- Dogfood everything: this repo follows its own principles

# Patterns
- Templates (global-CLAUDE.md, project-CLAUDE.md) are annotated examples — comments explain why, not what
- Skills live under claude-md/{global,project,audit}/SKILL.md — XML structure: role → context → instructions → examples → output
- README is the educational hub — templates and skills are the tools

# Do NOT
- Do not add generic best practices to templates — they hurt agent performance
- Do not exceed line targets: global ~30, project ~40
- Do not duplicate content between README, templates, and skills
- Do not center the repo on a single study — cite research as one source among many
