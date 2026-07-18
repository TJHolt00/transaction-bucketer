# Page-Level Overrides

Files here override `../MASTER.md` for a single screen. Create `<page-name>.md` only when a
screen genuinely needs to deviate — otherwise Master applies.

**Lookup order when building a page:**
1. Read `../MASTER.md` (global source of truth)
2. If `<page-name>.md` exists here, its rules **override** Master
3. Otherwise use Master exclusively

**Candidate pages in this app:** `auth`, `dashboard`, `review`, `transactions`, `budget`,
`rules`, `settings`.

Note: the auth screen is a deliberate exception — it renders a light card on a fixed dark
gradient regardless of theme. If you formalize that, document it in `auth.md`.
