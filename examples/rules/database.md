---
globs: ["src/db/**", "lib/db/**", "supabase/**"]
---
- Never call the database client directly outside /lib/db — all queries go through repository functions
- Every new table needs a migration file AND a type update in /lib/db/types.ts
- RLS policies are mandatory — no table without row-level security
- Use parameterized queries exclusively — never interpolate user input into SQL
