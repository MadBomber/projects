# Inbox

Ideas, questions, and recommendations that need Dewayne's decision. Append with a date; Dewayne prunes.

## 2026-11-05

- Two untracked notes already sit at the repo root (`target_1k_monthly.md`, `starter_rails_app_foundations.md`). Decide whether they live here, in a `notes/` directory, or elsewhere.
- ragdoll: CI starts a pgvector Postgres service but sets `RAGDOLL_SKIP_DATABASE_TESTS=true`, so no database test runs in CI (361 skips once #5 lands). Pointing the suite at that service (create `ragdoll_test`, drop the flag, stop treating `CI=true` as "no database" in `ci_environment?`) would give real DB coverage. Locally every DB test passes against pgvector except finding F-3. Needs your call; it changes what CI enforces.
