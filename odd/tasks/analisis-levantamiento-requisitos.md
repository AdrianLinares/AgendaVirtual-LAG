# Requirements analysis and elicitation (SENA productive stage, point 1)

## Objective
Produce the requirements baseline for the Agenda Virtual migration to a self-hosted VPS, covering point 1 of the instructor's 8-point productive-stage route (*Análisis y levantamiento de requisitos*) for the SENA *Tecnólogo en Análisis y Desarrollo de Software*, and anchor it to the existing SDD backlog in `openspec/changes/migrate-to-vps-hybrid/tasks.md` (task 0.4, SRS mínimo).

Deliverables (under `openspec/changes/migrate-to-vps-hybrid/01-analisis-levantamiento-requisitos/`):
- `01-objetivo-alcance.md` — objective, scope, non-scope.
- `02-actores-y-roles.md` — five actors plus role/permission matrix.
- `03-requerimientos-funcionales.md` — RF-001..RF-035.
- `04-requerimientos-no-funcionales.md` — RNF-001..RNF-032.
- `05-fuentes-de-informacion.md` — primary, planning and normative sources.
- `06-matriz-trazabilidad.md` — requirement ↔ module ↔ SENA stage ↔ backlog task.
- `07-informe-sena-analisis-y-levantamiento-de-requisitos.md` — academic report for annexing to the internship deliverable.

## Problem
The migration already has decisions, a phase backlog and an activation scope, but no formalized requirements baseline. Nothing links the source system's real behaviour (actors, modules, authorization rules, cron/email flow) to the migration phases, so academic traceability depends on implicit knowledge and each later phase risks inventing or dropping requirements.

## Why
- The SENA route requires a documented analysis stage before design; without it, later stages have no traceable origin.
- The source project (`/home/adrian/Projects/agenda-virtual-liceo`, read-only) is the only reliable requirements source; it must be distilled before it becomes stale or diverges.
- The migration's critical constraint (preserve `auth.uid()` + RLS semantics) needs to be visible as a requirement, not just a technical note in the proposal.
- Requirements IDs give later SDD sub-changes and TDD evidence something concrete to trace back to.

## Scope and constraints
- Requirements were elicited from read-only analysis of the source project: `README.md`, `package.json`, `supabase-schema.sql`, `migrations/`, `src/App.tsx`, `src/lib/auth-store.ts`, `src/lib/supabase.ts`, `src/pages/`, `supabase/functions/`, `cloudflare/workers/`.
- Planning sources: `openspec/changes/migrate-to-vps-hybrid/proposal.md`, its `tasks.md`, `openspec/project-context.md`, `openspec/config.yaml`.
- The five actors are taken from `profiles.rol` usage and the role-gated routes in `App.tsx`: administrador, administrativo, docente, estudiante, padre.
- Migration-derived requirements (RF-030..RF-035) are stated as requirements, not as implementation decisions; the proposal remains the authoritative source for the hybrid architecture decision.
- `proposal.md` and `tasks.md` remain the authoritative roadmap; this baseline does not replace the per-phase SDD cycle (`proposal → spec → design → tasks → apply → verify → sync → archive`).
- No source code, schema, or configuration was modified. Documentation only.
- SENA stage ↔ backlog alignment recorded here (kept in one place to avoid duplicated roadmaps):

  | SENA point | Backlog phases |
  |---|---|
  | 1. Análisis y levantamiento de requisitos | Fase 0.4 (this work), plus 0.2/0.3/0.5 as pending inputs |
  | 2. Diseño de la solución | Fase 1 (1.1–1.5) |
  | 3. Configuración del entorno e infraestructura | Fase 0.2, Fase 2 |
  | 4. Desarrollo del sistema | Fases 3–4 |
  | 5. Pruebas y control de calidad | Fase 6 (6.1–6.2) |
  | 6. Seguridad informática | Fase 5, plus RNF-006..RNF-014 |
  | 7. Documentación | Fase 8 (8.1–8.4), plus 7.3 runbook |
  | 8. Despliegue y puesta en marcha | Fase 2.3, Fase 6.3, Fase 7 |

- Explicitly out of scope for this unit: the Fase 0.2 auth/RLS spike, the 0.3 migration consolidation, and the 0.5 STRIDE threat model. They are inputs to design and are listed under Next step.

## TDD and delivery
- TDD: not applicable to this unit — it is documentation only and writes no executable behaviour. Strict TDD resumes with the first implementation-bearing task (Fase 0.2 spike), which must record RED → GREEN → TRIANGULATE → REFACTOR evidence in its own `apply-progress.md`.
- Verification performed for this unit: file presence, unique requirement ID counts (35 RF / 32 RNF), and cross-reference consistency between the traceability matrix, the academic report and this document.
- Delivery: documentation on `master`, pushed to `origin/master` on explicit user instruction. Change is 7 new files, 369 lines total, which exceeds the 300-line review budget if treated as a single PR; since the files are additive documentation with no executable behaviour, no split was made and no PR was opened.

## Authorized scope and route
- Authorized by the user's explicit request to start point 1 of the instructor's route, followed by the request to create/update the ODD documentation.
- Route: direct execution in the parent session. No delegation, because the work is documentation and needs no parallel writer.
- Edit surfaces used: `openspec/changes/migrate-to-vps-hybrid/01-analisis-levantamiento-requisitos/` (new) and `odd/tasks/` (new). `proposal.md`, `tasks.md` and the source project were read-only.

## Delivery plan
- Strategy: single documentation slice, no chain required (369 lines total across 7 files, all additive).
- Committed as `ee56271` (`docs(sena): add requirements analysis baseline and ODD task doc`) and pushed to `origin/master`; no PR was opened.
- Engram mirror under topic `odd/analisis-levantamiento-requisitos/tasks`.

## Remote delivery status
- Commit `ee56271` pushed to `origin/master`. No remote PR or issue exists for this unit.

## Checklist and progress
- [x] A1 — Elicit actors and role/permission matrix from role-gated routes and `profiles.rol`.
  - Acceptance: all five roles identified, each module mapped to at least one actor, authorization source named (RLS helpers `get_user_role()`, `is_admin()`, `is_docente()`).
  - Verification: `02-actores-y-roles.md`; cross-checked against `App.tsx` routes and `RoleProtectedRoute` allowlists.
- [x] A2 — Elicit functional requirements for the existing feature set plus migration constraints.
  - Acceptance: one stable ID per requirement, priority, and origin (file or artifact) for every requirement.
  - Verification: RF-001..RF-035, 35 unique IDs in `03-requerimientos-funcionales.md`.
- [x] A3 — Elicit non-functional requirements across architecture, security, quality/process, operations and documentation.
  - Acceptance: each RNF states justification; security and process requirements cover the known source-project gaps (wildcard CORS, no rate limiting, no CAPTCHA, no observability).
  - Verification: RNF-001..RNF-032, 32 unique IDs in `04-requerimientos-no-funcionales.md`.
- [x] A4 — Record sources and elicitation technique.
  - Acceptance: every primary source is a concrete path in the source or target repo; the technique section explains how requirements were derived.
  - Verification: `05-fuentes-de-informacion.md`.
- [x] A5 — Build traceability: requirements ↔ modules, SENA points, backlog tasks.
  - Acceptance: every SENA point and every migration-relevant backlog task (0.2, 0.3, 0.4, 0.5, 4.7, 4.8, 5.5, 8.4) appears in the matrix.
  - Verification: `06-matriz-trazabilidad.md`.
- [x] A6 — Write the academic report for the internship annex.
  - Acceptance: objective, justification, scope, actors, RF/RNF summary, sources, traceability, conclusions and recommendations, signed with location of evidence.
  - Verification: `07-informe-sena-analisis-y-levantamiento-de-requisitos.md`.
- [x] A7 — Verify ID uniqueness and cross-file consistency.
  - Acceptance: no duplicate or skipped IDs; counts stated in the report match the requirement files.
  - Verification: `grep`-based unique ID count returned 35 RF and 32 RNF, matching the summary text.

## Progress evidence
- Read from the source project: `README.md`, `package.json`, `supabase-schema.sql` (18 tables), `src/App.tsx`, `src/lib/auth-store.ts`, `src/lib/supabase.ts`, and the page inventory under `src/pages/`.
- 18 tables confirmed at the schema level, matching the proposal's "18 tables, all RLS-enabled" baseline.
- Known gaps already captured in the proposal were converted into requirements rather than restated as prose: wildcard CORS → RNF-008, missing rate limiting → RNF-009, console-only telemetry → RNF-022, unconsolidated migrations → RF-034. CAPTCHA is covered only by backlog task 5.3 and currently has no requirement ID; add one when Fase 5 is formalized instead of implying it already exists.
- The auth/RLS risk was captured as RF-031/RF-032/RNF-002 so it is traceable to the Fase 0.2 spike instead of living only in the proposal's risk section.
- Requirements are written as obligations on the target system; source-project behaviour is cited as origin, not treated as automatically correct.
- Documentation is in Spanish because it is an academic deliverable for a Spanish-language SENA program; this ODD task document stays in English to match the sibling repository and the existing `openspec` artifacts.
- Engram mirror status: recorded under topic `odd/analisis-levantamiento-requisitos/tasks`; readback verified in the same session.

## Next step
Point 1 is complete as a documentation baseline. Point 2 (design) should start with the blocking Fase 0.2 spike before any gateway design is committed:

1. Fase 0.2 — stand up `supabase/postgres` + standalone GoTrue in Docker Compose, load the consolidated schema/policies, prove `auth.uid()` and one real policy behave identically to the hosted project, and record the result as `adr-auth-rls-contract.md`.
2. Fase 0.3 — consolidate `supabase-schema.sql` plus the ~30 migrations into one reproducible, versioned set.
3. Fase 0.5 — lightweight STRIDE threat model, cross-referenced to RNF-006..RNF-014.
4. Then Fase 1 design per subsystem (auth/gateway contract, data strategy, cron/worker, infra, C4 diagrams).

If the spike disproves the `auth.uid()` contract, RF-030..RF-032 must be renegotiated before design proceeds.
