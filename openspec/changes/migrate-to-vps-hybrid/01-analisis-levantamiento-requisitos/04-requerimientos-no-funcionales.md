# 04. Requerimientos No Funcionales (RNF)

## 4.1. Arquitectura y Diseño

| Código | Requisito | Prioridad | Justificación |
|---|---|---|---|
| **RNF-001** | Arquitectura **híbrida**: mantener PostgreSQL + RLS, agregar API Gateway Node/TS como única vía hacia la BD. | Alta | Preserva el activo más valioso (modelo RLS) y permite migrar fuera de BaaS. |
| **RNF-002** | Uso de **imagen `supabase/postgres`** (no `postgres` vanilla). | Crítica | Requiere extensiones/esquema necesarios para `auth.uid()`, `auth.users` y el contrato usado por las políticas RLS. |
| **RNF-003** | **Orquestación con Docker Compose** con perfiles `dev`/`prod`. | Alta | Reproducibilidad, trazabilidad y fácil despliegue en VPS. |
| **RNF-004** | **Reverse proxy con Nginx** + TLS mediante **Certbot**. | Alta | Exposición segura a internet, gestión de certificados y cabeceras. |
| **RNF-005** | Separación clara entre **frontend estático** y **backend Gateway/Worker**. | Alta | Principio de menor privilegio y mejor control de superficie expuesta. |

## 4.2. Seguridad Informática

| Código | Requisito | Prioridad | Justificación |
|---|---|---|---|
| **RNF-006** | **Row Level Security (RLS)** debe permanecer habilitado en todas las tablas sensibles. | Crítica | Control de acceso a nivel de fila por rol/relación. |
| **RNF-007** | **Cookies HttpOnly + Secure** para sesión, evitando exposición al JS del cliente. | Alta | Mitiga robo de sesión vía XSS. |
| **RNF-008** | **CORS con allowlist** (prohibido `Access-Control-Allow-Origin: *`). | Alta | Corregir hallazgo identificado en el proyecto origen. |
| **RNF-009** | **Rate limiting** en endpoints críticos (`/auth/login`, recuperación de contraseña). | Alta | Mitigación contra fuerza bruta. |
| **RNF-010** | **Hardening del VPS**: usuario no-root, UFW, fail2ban, actualizaciones automáticas, SSH solo con llaves. | Alta | Buenas prácticas de seguridad en servidores Linux. |
| **RNF-011** | **Gestión de secretos** fuera del repositorio (variables de entorno, Docker secrets). Solo `VITE_*` expuesto al frontend. | Crítica | Separación de secretos/clave pública. |
| **RNF-012** | **Sanitización** de HTML no confiable (ya existe uso de `dompurify`). Debe mantenerse. | Media-Alta | Prevención XSS en contenido generado por usuarios (anuncios). |
| **RNF-013** | Aplicación de checklist **OWASP ASVS/Top 10** al Gateway y Frontend. | Alta | Evidencia para el punto 6 del SENA (*"Seguridad informática"*). |
| **RNF-014** | Escaneo de dependencias (`npm audit`/`osv-scanner`) y secretos (`gitleaks`) en CI. | Alta | Detección temprana de vulnerabilidades. |

## 4.3. Calidad, Pruebas y Metodología

| Código | Requisito | Prioridad | Justificación |
|---|---|---|---|
| **RNF-015** | Aplicación estricta de **SDD** (Spec-Driven Development): `proposal → spec → design → tasks → apply → verify → sync → archive`. | Crítica | Requisito académico explícito. |
| **RNF-016** | Aplicación estricta de **TDD**: `RED → GREEN → TRIANGULATE → REFACTOR`. Evidencia en `apply-progress.md`. | Crítica | Requisito académico explícito. |
| **RNF-017** | Presupuesto de revisión: **máximo 300 líneas modificadas por PR/cambio**. Usar *chained PRs* si supera. | Alta | Higiene de revisión y criterio de entrega. |
| **RNF-018** | Pruebas unitarias con **Vitest** (frontend) y **Vitest/node:test** (Gateway). | Alta | Cobertura de lógica crítica. |
| **RNF-019** | Pruebas E2E con **Playwright** contra stack completo con Docker Compose. | Alta | Validación de flujos end-to-end. |
| **RNF-020** | Cobertura mínima en módulos críticos: **auth, notas, permisos**. | Alta | Enfoque en lógica sensible y de negocio. |
| **RNF-021** | Integración continua con gates: **lint, typecheck, tests, build** sin warnings. | Alta | Control de calidad automatizado. |

## 4.4. Desempeño, Operación y Observabilidad

| Código | Requisito | Prioridad | Justificación |
|---|---|---|---|
| **RNF-022** | **Logging estructurado** (reemplazar `console.debug` disperso). | Alta | Trazabilidad y diagnóstico. |
| **RNF-023** | **Backups automáticos** de PostgreSQL (`pg_dump` + rotación). Debe existir **prueba de restore documentada**. | Alta | Recuperación ante desastres. |
| **RNF-024** | **Runbook de incidentes** (Gateway caído, Postgres no levanta, restore de backup). | Alta | Operación y respuesta a incidentes. |
| **RNF-025** | Métricas básicas (uptime, latencia, tasa de error 5xx) + alertas mínimas. | Media-Alta | Observabilidad mínima viable. |
| **RNF-026** | Idempotencia/reintentos con **backoff** en jobs del worker (correo). | Alta | Robustez ante fallos transitorios. |
| **RNF-027** | Configuración reproducible: **todo el stack levantable desde `docker compose up`**. | Alta | Cumple objetivo de "instalación local/servidor reproducible". |

## 4.5. Usabilidad y Documentación Académica

| Código | Requisito | Prioridad | Justificación |
|---|---|---|---|
| **RNF-028** | **Manual Técnico** (arquitectura, ADRs, cómo levantar entorno). | Alta | Evidencia para punto 7 SENA. |
| **RNF-029** | **Manual de Usuario** por roles. | Alta | Evidencia para punto 7 SENA. |
| **RNF-030** | **Informe de aplicación de principios de calidad y seguridad de software**. | Alta | Evidencia para punto 7 SENA. |
| **RNF-031** | **Evidencia de TDD** (historial RED→GREEN→REFACTOR) y **SDD** (artefactos `openspec/changes/*` completos). | Crítica | Requisito académico clave. |
| **RNF-032** | **Trazabilidad** entre requisitos (RF/RNF), diseño, tareas, pruebas y verificación. | Alta | Cumple con análisis y control de calidad. |
