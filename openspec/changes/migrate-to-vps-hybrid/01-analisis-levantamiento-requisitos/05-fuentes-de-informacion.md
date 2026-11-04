# 05. Fuentes de Información

## 5.1. Fuentes Primarias (Análisis Directo)

| Fuente | Tipo | Uso |
|---|---|---|
| **`/home/adrian/Projects/agenda-virtual-liceo/`** | Proyecto original (código fuente) | Fuente principal para levantar requisitos reales. |
| **`agenda-virtual-liceo/README.md`** | Documentación técnica | Stack, instalación, roles, despliegue, notificaciones por correo, seguridad. |
| **`agenda-virtual-liceo/package.json`** | Configuración proyecto | Dependencias, scripts, stack tecnológico. |
| **`agenda-virtual-liceo/supabase-schema.sql`** | Esquema de base de datos | Tablas, enums, índices, políticas RLS, datos base. |
| **`agenda-virtual-liceo/migrations/`** (~30 archivos) | Migraciones históricas | Identificación de cambios posteriores al schema base (ej. `feature_flags`, `email_notifications_queue`, `notificaciones`). |
| **`agenda-virtual-liceo/src/App.tsx`** | Rutas y protección | Mapeo de rutas, `ProtectedRoute`, `RoleProtectedRoute`, módulos por rol. |
| **`agenda-virtual-liceo/src/lib/auth-store.ts`** | Lógica de autenticación | Flujos `signIn`, `signOut`, recuperación/restablecimiento/cambio de contraseña, sincronización de sesión. |
| **`agenda-virtual-liceo/src/lib/supabase.ts`** | Cliente Supabase | Configuración de cliente, patrón de uso actual. |
| **`agenda-virtual-liceo/src/pages/`** | Vistas/Módulos | Identificación funcional por módulo (Notas, Asistencia, Mensajes, etc.). |
| **`agenda-virtual-liceo/supabase/functions/`** | Edge Functions (Deno) | Función `send-message-emails` a migrar. |
| **`agenda-virtual-liceo/cloudflare/workers/`** | Worker cron (Cloudflare) | Lógica de disparo de notificaciones a migrar. |

## 5.2. Fuentes Secundarias (Planificación del Proyecto)

| Fuente | Tipo | Uso |
|---|---|---|
| **`AgendaVirtual-LAG/openspec/changes/migrate-to-vps-hybrid/proposal.md`** | Propuesta de migración | Arquitectura híbrida, decisiones, riesgos críticos, alcance/no-alcance. |
| **`AgendaVirtual-LAG/openspec/changes/migrate-to-vps-hybrid/tasks.md`** | Backlog faseado | Hoja ruta Fase 0-8, alineación con ruta SENA, TDD/SDD. |
| **`AgendaVirtual-LAG/openspec/project-context.md`** | Contexto del proyecto destino | Stack objetivo, configuración SDD, convenciones. |
| **`AgendaVirtual-LAG/openspec/config.yaml`** | Configuración SDD | Reglas `strict_tdd`, `artifact_store: both`, `review_budget: 300`. |

## 5.3. Fuentes Normativas/Académicas

| Fuente | Tipo | Uso |
|---|---|---|
| **Ruta de etapa productiva SENA** | Guía académica (instructor) | Estructura obligatoria: 1) Análisis y levantamiento de requisitos, 2) Diseño, 3) Configuración del entorno, 4) Desarrollo, 5) Pruebas y control de calidad, 6) Seguridad informática, 7) Documentación, 8) Despliegue y puesta en marcha. |
| **OWASP Top 10 / OWASP ASVS** | Referencia de seguridad | Base para checklist de seguridad (RNF-013). |
| **SDD/TDD (Spec-Driven Development / Test-Driven Development)** | Metodología | Aplicación disciplinada con evidencia. |

## 5.4. Técnica de Levantamiento Utilizada

- **Análisis de código estático**: Revisión de componentes, rutas, store, cliente, funciones y esquema SQL.
- **Ingeniería inversa funcional**: Identificación de módulos operativos desde `src/pages/` y `App.tsx`.
- **Análisis de configuración**: Revisión de `package.json`, `README.md`, archivos de despliegue.
- **Análisis documental**: Revisión de propuesta, backlog y contexto SDD.
- **Trazabilidad cruzada**: Comparación entre funcionalidades documentadas, rutas protegidas y estructura de BD.
