---
globs: ["src/api/**", "app/api/**"]
---
- API routes return DTOs, never raw database rows — see /lib/dto for the pattern
- Always validate request body with Zod schema before processing
- Return 4xx for client errors, 5xx only for unexpected failures
- One route file per resource: /app/api/[resource]/route.ts
