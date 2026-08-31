# AgendaVirtual-LAG Project Context

- **Project:** AgendaVirtual-LAG
- **Engram project:** `agendavirtual-lag`
- **Intent:** Reimplement the sibling serverless school-management application as a locally hosted VPS deployment.
- **Repository state at initialization:** Fresh scaffold with empty `apps/frontend/`, `apps/gateway/`, `db/`, and `docker/` directories. No `package.json` or detectable test command exists yet.
- **Target stack:** pnpm; React + Vite + TypeScript frontend; Node + TypeScript gateway; PostgreSQL using `supabase/postgres`; Docker Compose orchestration.
- **Target testing:** Vitest frontend unit tests, Playwright frontend E2E tests, and Vitest or `node:test` for the gateway. This is target configuration, not currently executable.
- **SDD choices:** artifact store `both`; interactive execution; `ask-on-risk` delivery; review budget 300 changed lines per PR; strict TDD enabled and non-negotiable.
- **Conventions consulted:** sibling repository `AGENTS.md` read-only for pnpm, Vitest, Playwright, and strict-TDD conventions.
- **Skill registry:** `.atl/skill-registry.md` is absent; no clear registry source was available in this new repository, so none was invented.
- **Persistence note:** Engram save was attempted for topic `sdd-init/agendavirtual-lag`, but the local Engram provider was unavailable during initialization.
