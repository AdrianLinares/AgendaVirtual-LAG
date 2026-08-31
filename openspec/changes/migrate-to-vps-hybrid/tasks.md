# Task backlog: migrate-to-vps-hybrid

Pre-formal capture of the full roadmap agreed with the project owner. Each phase should be run through the real SDD cycle (`proposal → spec → design → tasks → apply → verify → sync → archive`) as its own sub-change when work on it starts; this file is the seed backlog, not a substitute for that.

Strict TDD applies to every implementation task below marked (TDD): RED (failing test first) → GREEN (minimal implementation) → TRIANGULATE (edge cases) → REFACTOR, with evidence recorded in the change's `apply-progress.md`.

## Fase 0 — Fundación y spike técnico (bloqueante)
- [ ] 0.1 Repo scaffolding: `openspec/config.yaml` (done by `sdd-init`), base folder structure (done), README stub, `.gitignore`.
- [ ] 0.2 **Spike bloqueante**: `supabase/postgres` + GoTrue standalone en Docker Compose local; cargar schema/policies; probar que `auth.uid()` y una policy real responden igual que en el proyecto hosteado. Documentar como ADR (`openspec/changes/migrate-to-vps-hybrid/adr-auth-rls-contract.md`).
- [ ] 0.3 Consolidar `supabase-schema.sql` + las ~30 migraciones del proyecto original en un set único versionado y reproducible (elegir herramienta: `node-pg-migrate`, `dbmate`, o `sqitch`).
- [ ] 0.4 SRS mínimo (requisitos funcionales/no funcionales) formalizado como `proposal.md`/`spec.md` de un sub-change dedicado.
- [ ] 0.5 Modelo de amenazas inicial (STRIDE liviano) documentado.

## Fase 1 — Diseño (SDD: spec + design por subsistema)
- [ ] 1.1 Subsistema Auth/Gateway: contrato de sesión (cookie httpOnly + JWT), refresh, logout, roles.
- [ ] 1.2 Subsistema Datos: estrategia de migraciones, backups/restore, auditoría completa de RLS existente.
- [ ] 1.3 Subsistema Cron/Notificaciones: reemplazo del Cloudflare Worker por job en Docker.
- [ ] 1.4 Subsistema Infra: `docker-compose.yml`, Nginx, Certbot, logs, gestión de secretos.
- [ ] 1.5 Diagramas de arquitectura y despliegue (C4 nivel contenedor).

## Fase 2 — Infraestructura base
- [ ] 2.1 `docker-compose.yml` con Postgres, gateway, frontend, nginx, worker (perfiles dev/prod).
- [ ] 2.2 Nginx: TLS (Certbot), cabeceras de seguridad (HSTS, CSP, X-Frame-Options, Referrer-Policy), reverse proxy.
- [ ] 2.3 Backups automáticos de Postgres (pg_dump + rotación) con prueba de restore documentada.
- [ ] 2.4 Hardening del VPS: usuario no-root, firewall (ufw), fail2ban, updates automáticas, SSH solo con llaves.

## Fase 3 — Auth y Gateway (TDD)
- [ ] 3.1 (TDD) GoTrue/auth propio + login/logout/refresh en el gateway.
- [ ] 3.2 (TDD) Middleware `SET LOCAL request.jwt.claims` por request, probado contra RLS real (test de integración).
- [ ] 3.3 (TDD) Puerto de `manage-users` (Deno) a endpoint Node con mismos guards de rol + rate limiting.
- [ ] 3.4 (TDD) Rate limiting / anti-fuerza-bruta en login y recuperación de contraseña.

## Fase 4 — Migración funcional del dominio (por módulo, cada uno su ciclo SDD+TDD)
- [ ] 4.1 (TDD) Notas/calificaciones
- [ ] 4.2 (TDD) Asistencia
- [ ] 4.3 (TDD) Anuncios/Mensajes
- [ ] 4.4 (TDD) Boletines (PDF)
- [ ] 4.5 (TDD) Calendario/eventos
- [ ] 4.6 (TDD) Permisos/citaciones
- [ ] 4.7 Reemplazar en el frontend todas las llamadas `supabase.from/functions.invoke` por el cliente HTTP del gateway.
- [ ] 4.8 (TDD) Puerto de `send-message-emails` a job Node del worker (reintentos/backoff).

## Fase 5 — Seguridad
- [ ] 5.1 Checklist OWASP ASVS/Top10 aplicado contra gateway + frontend.
- [ ] 5.2 CORS: allowlist real de origen (no `*`).
- [ ] 5.3 CAPTCHA (u equivalente) en login/recuperación.
- [ ] 5.4 Escaneo de dependencias (`npm audit`/`osv-scanner`) y de secretos (gitleaks) en CI.
- [ ] 5.5 Auditoría adversarial de RLS: intentar romper cada policy con rol incorrecto.

## Fase 6 — Calidad y CI/CD
- [ ] 6.1 Pipeline CI: lint, typecheck, tests (unit + integración) + Playwright E2E contra el stack Docker Compose completo, build.
- [ ] 6.2 Gate de calidad: cobertura mínima en módulos críticos (auth, notas, permisos), lint sin warnings.
- [ ] 6.3 Pipeline CD: build de imágenes, push a registry, deploy al VPS.

## Fase 7 — Observabilidad y operación
- [ ] 7.1 Logging estructurado real (reemplaza `console.debug`).
- [ ] 7.2 Métricas básicas (uptime, latencia, tasa de error 5xx) + alertas mínimas.
- [ ] 7.3 Runbook de incidentes (gateway caído, Postgres no levanta, restore de backup).

## Fase 8 — Entrega académica
- [ ] 8.1 Manual técnico (arquitectura, ADRs, cómo levantar el entorno).
- [ ] 8.2 Manual de usuario.
- [ ] 8.3 Informe de aplicación de principios de calidad y seguridad de software.
- [ ] 8.4 Evidencia de TDD (historial RED→GREEN→REFACTOR) y de SDD (artefactos `openspec/changes/*` completos) como anexo.

## Review Workload Forecast
- Chained PRs recommended: TBD per phase — this backlog spans far more than 300 changed lines in aggregate; each Fase (or sub-item) should be its own PR/change under the 300-line budget. Re-forecast at `sdd-tasks` time for each sub-change.
