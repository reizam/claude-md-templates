---
globs: ["**/*.test.*", "**/*.spec.*"]
---
- Use describe/it blocks, not test() — consistent with the rest of the codebase
- One assertion per test when possible — makes failures easier to diagnose
- Mock external services, never the module under test
- Test file lives next to source file, not in a separate __tests__ directory
