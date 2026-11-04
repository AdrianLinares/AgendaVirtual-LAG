# 06. Matriz de Trazabilidad Requisitos - Módulos

## 6.1. Trazabilidad RF - Módulos del Sistema

| Código RF | Módulo / Funcionalidad | Actor(es) | Archivo/Fuente | Estado en Origen |
|---|---|---|---|---|
| **RF-001 a RF-008** | Autenticación, Sesión, Recuperación/Restablecimiento/Cambio de Contraseña | Todos | `auth-store.ts`, `LoginPage.tsx`, `RecuperarContrasenaPage.tsx`, `RestablecerContrasenaPage.tsx`, `CambiarContrasenaPage.tsx` | Operativo |
| **RF-009 a RF-013** | Dashboard y Protección de Rutas | Todos / Por rol | `App.tsx`, `DashboardPage.tsx` | Operativo |
| **RF-014** | Boletines | Admin, Administrativo, Estudiante, Padre | `BoletinesPage.tsx` | Operativo |
| **RF-015** | Notas | Admin, Docente, Estudiante, Padre | `NotasPage.tsx`, RPC `notas_count` | Operativo |
| **RF-016** | Asistencia | Admin, Docente, Estudiante, Padre | `AsistenciaPage.tsx` | Operativo |
| **RF-017** | Horarios | Todos (según contexto) | `HorariosPage.tsx` | Operativo |
| **RF-018** | Calendario/Eventos | Todos | `CalendarioPage.tsx` | Operativo |
| **RF-019** | Anuncios | Admin, Docente, Estudiante, Padre | `AnunciosPage.tsx` | Operativo |
| **RF-020 a RF-021** | Mensajes Internos + Notificaciones por Correo | Todos | `MensajesPage.tsx`, `supabase/functions/send-message-emails/`, `cloudflare/workers/` | Operativo |
| **RF-022** | Permisos/Excusas | Admin, Administrativo, Docente, Estudiante, Padre | `PermisosPage.tsx` | Operativo |
| **RF-023** | Citaciones | Admin, Docente, Estudiante, Padre | `CitacionesPage.tsx` | Operativo |
| **RF-024** | Seguimiento Estudiantil | Admin, Administrativo, Docente | `SeguimientoPage.tsx` | Operativo |
| **RF-025 a RF-029** | Administración de Usuarios, Roles, Carga Masiva, Vinculaciones | Administrador | `AdminPage.tsx`, `profiles`, `padres_estudiantes`, `asignaciones_docentes` | Operativo |
| **RF-030 a RF-035** | Migración VPS Híbrido (Gateway, RLS, Worker, Migraciones, Docker) | Técnico/Proyecto | `proposal.md`, `tasks.md` | Pendiente (por implementar) |

## 6.2. Trazabilidad RF/RNF - Fases SENA

| Punto SENA | Artefacto / Actividad | RF/RNF Relacionados | Evidencia |
|---|---|---|---|
| **1. Análisis y levantamiento de requisitos** | SRS mínimo, actores, RF/RNF, fuentes, trazabilidad | RF-001 a RF-035, RNF-001 a RNF-032 | `01-analisis-levantamiento-requisitos/*` |
| **2. Diseño de la solución** | Arquitectura híbrida, contrato Auth/Gateway, diagramas C4, migraciones, estrategia datos | RNF-001-RNF-005, RF-030-RF-035 | `openspec/changes/migrate-to-vps-hybrid/` (futuro `spec/design`) |
| **3. Configuración del entorno de desarrollo e infraestructura** | Docker Compose, Nginx, Certbot, Postgres `supabase/postgres`, GoTrue spike, hardening | RNF-002-RNF-005, RNF-010-RNF-011, RF-031-RF-034 | Fase 0-2 tareas |
| **4. Desarrollo del sistema** | Gateway, worker, migración frontend a cliente HTTP, módulos por ciclo SDD+TDD | RF-030-RF-033, RNF-015-RNF-016 | Fase 3-4 + `apply-progress.md` |
| **5. Pruebas y control de calidad** | Unitarias, integración, E2E, cobertura, lint/typecheck | RNF-017-RNF-021 | Fase 6, CI |
| **6. Seguridad informática** | OWASP ASVS/Top10, CORS, rate-limit, CAPTCHA, escaneo, auditoría RLS adversarial | RNF-006-RNF-014 | Fase 5 |
| **7. Documentación** | Manual técnico, manual usuario, informe calidad/seguridad, evidencias TDD/SDD | RNF-028-RNF-032 | Fase 8 |
| **8. Despliegue y puesta en marcha** | CD, VPS, backups, restore, runbook, observabilidad | RNF-022-RNF-027 | Fase 2.3, Fase 7, Fase 6.3 |

## 6.3. Trazabilidad con Backlog `migrate-to-vps-hybrid`

| Tarea Backlog | Relación con este Análisis | Observación |
|---|---|---|
| **0.2 Spike bloqueante (auth/RLS)** | RF-031, RF-032, RNF-002 | Crítico para validar contrato `auth.uid()` antes de Gateway. |
| **0.3 Consolidación de migraciones** | RF-034 | Base para reproducibilidad del esquema. |
| **0.4 SRS mínimo** | **Cumplido con este documento** | Se formaliza como evidencia del punto 1 SENA. |
| **0.5 Modelo de amenazas STRIDE** | RNF-013 | Pendiente (siguiente paso tras análisis). |
| **4.7 Reemplazo `supabase.from/functions.invoke`** | RF-030 | Eliminación de dependencia SDK en cliente. |
| **4.8 Puerto `send-message-emails` a worker Node** | RF-033 | Migración de cron/notificaciones. |
| **5.5 Auditoría adversarial de RLS** | RF-032, RNF-006 | Validación de políticas tras migración. |
| **8.4 Evidencia TDD/SDD** | RNF-015-RNF-016, RNF-031 | Requisito clave para entrega académica. |
