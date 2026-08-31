# Proposal: Migrate Agenda Virtual to a self-hosted VPS (hybrid architecture)

Status: draft (hand-captured from planning session; formal `sdd-spec`/`sdd-design` passes still pending)
Related source project (read-only reference): `/home/adrian/Projects/agenda-virtual-liceo`
Engram project: `agendavirtual-lag`

## 1. Problem / motivation

The original project (`agenda-virtual-liceo`) is a school-management SPA built to run entirely on managed serverless infrastructure: Cloudflare Pages (frontend) + Cloudflare Worker (email cron) + hosted Supabase (Postgres + GoTrue auth + Edge Functions on Deno). It was built as a practice project for the "Análisis y Desarrollo de Software" degree track.

The goal now is to re-platform it as a **self-hosted, locally-installed application running on a VPS**, in a **new repository**, while explicitly applying software engineering, software quality, and software security principles using **SDD (Spec-Driven Development)** and **strict TDD**. This is itself an academic deliverable: the process discipline (SDD artifacts + TDD evidence + security review) is as important as the running system.

## 2. Evaluation of the source project (baseline)

Captured from a full read-only architecture scan of `agenda-virtual-liceo` (frontend React 18 + Vite 5 + TS, Zustand, Tailwind/Radix; `@supabase/supabase-js`; Vitest + Playwright; Cloudflare Pages + a Cloudflare Worker cron; Supabase Postgres with RLS + two Deno edge functions).

### What to preserve as-is
- The **RLS authorization model** in Postgres: 18 tables, all RLS-enabled, `SECURITY DEFINER` helper functions (`get_user_role()`, `is_admin()`, `is_docente()`) with `search_path` pinned to avoid privilege-escalation-by-search-path and RLS-recursion bugs. This is the single most valuable asset of the source project — carry the schema and policies over essentially unchanged.
- Clear secret/public boundary: only `VITE_*` vars reach the browser; service-role/cron secrets stay server-side.
- Existing Vitest + Playwright suites are a good regression net; port their intent (not necessarily their `supabase-js` call sites) to the new gateway-backed client.
- `dompurify` sanitization for untrusted HTML (announcements, Drive embeds).

### Known gaps / debt to fix during migration (do not silently carry these over)
- CORS wide open (`Access-Control-Allow-Origin: *`) in edge functions.
- No rate limiting, no CAPTCHA anywhere (login, password recovery) — becomes materially more dangerous once self-hosted and internet-facing without Cloudflare's edge protections.
- `supabase-schema.sql` (base schema) is out of sync with ~30 ad-hoc files under `migrations/` (`feature_flags`, `email_notifications_queue`, `notificaciones` exist only in migrations, not in the base schema). Must be consolidated into one versioned, replayable migration history before porting.
- Cron/email worker is tightly coupled to Cloudflare (`scheduled`, `ctx.waitUntil`, `wrangler.worker.toml`) and to Deno (`Deno.serve`, `Deno.env`) — neither runs unmodified on a plain VPS/Docker Compose.
- Session handling assumes GoTrue-issued JWT stored client-side (`localStorage`); README claims `sessionStorage` (stale docs — a symptom, not just a detail).
- No real observability (`telemetry.ts` only logs to `console.debug`).
- No `openspec/config.yaml` / formal SDD artifacts existed yet in the source repo — only ad-hoc `openspec/changes/*` folders.

## 3. Decisions made (already settled with the project owner — do not re-litigate without new information)

| Decision | Choice | Rationale |
|---|---|---|
| Backend/data strategy | **Hybrid**: keep Postgres + existing RLS model as-is; put a thin Node/TypeScript API gateway (BFF) in front; remove `supabase-js` from the client entirely | Preserves the valuable RLS security model and avoids a full rewrite of authorization logic, while decoupling the client from Supabase's SDK/hosted services — required for the "no serverless / self-hosted" goal |
| Infra orchestration | **Docker Compose** | Reproducible, versionable, good documentation artifact for the academic deliverable; avoids undocumented manual VPS state |
| Process rigor | **SDD (full per-feature: proposal → spec → design → tasks → apply → verify → sync → archive) + strict TDD (RED → GREEN → TRIANGULATE → REFACTOR) on every implementation task** | Explicit academic requirement: demonstrate software engineering discipline, not just a working app |
| SDD artifact store | `both` (OpenSpec files in this repo **and** Engram memory) | Redundant continuity: OpenSpec is human-readable/versioned; Engram survives across Pi sessions even before commits happen |
| SDD execution mode | `interactive` | Owner wants to review/adjust after each phase, not a fully autonomous chain |
| Delivery strategy | `ask-on-risk`, review budget **300 changed lines per PR** | Keeps each PR reviewable in one sitting — also graded criterion (change hygiene) |

## 4. Target architecture (summary — formal design doc pending)

```
[Nginx: TLS via Certbot, reverse proxy, security headers, basic rate limiting]
        |
   [Frontend: static build of apps/frontend (React + Vite + TS), served by Nginx]
        |
   [apps/gateway — Node/TypeScript API gateway, the ONLY thing that talks to Postgres]
   - Auth: session cookie (httpOnly) <-> JWT, backed by either a standalone GoTrue
     container or a self-issued JWT compatible with Postgres's `request.jwt.claims`
     convention
   - Per-request: opens a pg connection/transaction and runs
     `SET LOCAL request.jwt.claims = '<json>'` (and `SET LOCAL role ...` if needed)
     BEFORE running any business query, so existing RLS policies and `auth.uid()`
     keep working unmodified
   - Business endpoints replace today's direct `supabase.from(...)` calls: notas,
     asistencia, anuncios/mensajes, boletines (PDF), calendario/eventos,
     permisos/citaciones, manage-users (ported from the Deno edge function)
        |
   [Postgres — image `supabase/postgres` (NOT vanilla postgres), because it ships the
    `auth` schema / `auth.uid()` / RLS-related plumbing the existing policies rely on]
        |
   [Worker — cron replacement for the Cloudflare Worker: node-cron / a small scheduler
    container calling the gateway's internal endpoints for the email-notification queue
    (ported from `send-message-emails`)]

All of the above as services in one docker-compose.yml (dev and prod profiles).
```

### 4.1 Critical technical risk — resolve BEFORE Fase 1 design work

`auth.uid()` and the `auth.users` table are **not part of vanilla Postgres** — they come from Supabase's Postgres image/extensions (normally paired with GoTrue). The hybrid approach only works if this contract is reproduced exactly, otherwise every existing RLS policy silently breaks (or worse, silently returns empty results instead of erroring).

**Mandatory Fase-0 spike** (already scoped as task #2 in the task backlog): stand up `supabase/postgres` + a standalone GoTrue container in Docker Compose locally, load the (consolidated) schema + policies, and manually prove that a request carrying a JWT reproduces the same `auth.uid()` / RLS behavior as the hosted Supabase project. Record the outcome as an ADR. Do not proceed to gateway implementation (Fase 3) until this is proven.

## 5. Scope / non-goals for this change

**In scope:** everything needed to reach a working, secured, tested, self-hosted deployment of the existing feature set (notas, asistencia, mensajes, anuncios, boletines, calendario, permisos, auth) on a VPS via Docker Compose, developed under SDD+TDD.

**Out of scope for this proposal:** new product features not already present in `agenda-virtual-liceo`; multi-tenant support; horizontal scaling / HA (single-VPS deployment is the target); mobile app.

## 6. Full phased roadmap

See `tasks.md` in this same change folder for the complete, checkable phase-by-phase backlog (Fase 0 → Fase 8). Each numbered item there is expected to become (or feed) its own SDD sub-change (`proposal → spec → design → tasks → apply → verify`) once formal phase work starts — this document and `tasks.md` are the pre-formal capture that seeds that work, not a replacement for running the real SDD phases.

## 7. How to resume this work in a new Pi session

1. Run `mem_context` / `mem_search` (project `agendavirtual-lag`) to recover the architecture-decision, source-project-evaluation, and auth-rls-contract-risk observations.
2. Read this proposal and `tasks.md`.
3. Use `/sdd-continue` (or the native SDD dispatcher) to resolve status against `openspec/config.yaml` and pick up at the next ready phase — starting with the Fase 0 spike (task #2), since it blocks all downstream design work.
