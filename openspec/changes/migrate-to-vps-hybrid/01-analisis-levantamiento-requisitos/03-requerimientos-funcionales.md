# 03. Requerimientos Funcionales (RF)

Los siguientes requisitos fueron identificados a partir del análisis del proyecto `agenda-virtual-liceo` (código fuente, rutas, `auth-store`, esquema SQL y funcionalidades operativas).

## 3.1. Autenticación y Sesión

| Código | Requisito | Prioridad | Origen |
|---|---|---|---|
| **RF-001** | El sistema debe permitir iniciar sesión con correo electrónico y contraseña. | Alta | `LoginPage.tsx`, `auth-store.ts` (`signIn`) |
| **RF-002** | El sistema debe validar las credenciales contra el proveedor de autenticación. | Alta | `auth-store.ts` |
| **RF-003** | El sistema debe cargar el perfil del usuario (`profiles`) asociado al usuario autenticado. | Alta | `auth-store.ts` (`signIn`, `initialize`, `syncSession`) |
| **RF-004** | El sistema debe permitir cerrar sesión (`signOut`). | Alta | `auth-store.ts` |
| **RF-005** | El sistema debe permitir solicitar recuperación de contraseña (`requestPasswordReset`). | Alta | `auth-store.ts` |
| **RF-006** | El sistema debe permitir restablecer contraseña mediante enlace de recuperación (`updatePasswordWithRecovery`). | Alta | `auth-store.ts`, `RestablecerContrasenaPage.tsx` |
| **RF-007** | El sistema debe permitir al usuario autenticado cambiar su contraseña (`changePassword`). | Alta | `auth-store.ts`, `CambiarContrasenaPage.tsx` |
| **RF-008** | El sistema debe mantener la sesión y sincronizarla con el estado de autenticación. | Alta | `auth-store.ts` (`initialize`, `onAuthStateChange`, `syncSession`) |

## 3.2. Dashboard y Navegación

| Código | Requisito | Prioridad | Origen |
|---|---|---|---|
| **RF-009** | El sistema debe mostrar un *dashboard* personalizado según el rol del usuario. | Alta | `DashboardPage.tsx`, `App.tsx` |
| **RF-010** | El sistema debe proteger rutas privadas mediante `ProtectedRoute`. | Alta | `App.tsx` |
| **RF-011** | El sistema debe proteger rutas por rol mediante `RoleProtectedRoute`. | Alta | `App.tsx` |
| **RF-012** | El sistema debe redirigir usuarios no autenticados a `/login`. | Alta | `App.tsx` |
| **RF-013** | El sistema debe controlar el acceso a rutas administrativas (`/dashboard/admin`). | Alta | `App.tsx` |

## 3.3. Gestión Académica

| Código | Requisito | Prioridad | Origen |
|---|---|---|---|
| **RF-014** | El sistema debe permitir gestionar/consultar **boletines**. | Alta | `BoletinesPage.tsx` |
| **RF-015** | El sistema debe permitir registrar y consultar **notas** por estudiante, grupo, asignatura y periodo. | Alta | `NotasPage.tsx`, RPC `notas_count` |
| **RF-016** | El sistema debe permitir registrar y consultar **asistencias**. | Alta | `AsistenciaPage.tsx` |
| **RF-017** | El sistema debe permitir gestionar **horarios** de clase. | Alta | `HorariosPage.tsx` |
| **RF-018** | El sistema debe permitir gestionar **calendario/eventos**. | Alta | `CalendarioPage.tsx` |

## 3.4. Comunicación y Seguimiento

| Código | Requisito | Prioridad | Origen |
|---|---|---|---|
| **RF-019** | El sistema debe permitir crear, publicar y visualizar **anuncios**. | Alta | `AnunciosPage.tsx` |
| **RF-020** | El sistema debe permitir el envío y recepción de **mensajes internos** entre usuarios. | Alta | `MensajesPage.tsx` |
| **RF-021** | El sistema debe generar **notificaciones por correo** para nuevos mensajes internos (flujo con cola y worker/cron). | Alta | `supabase/functions/send-message-emails/`, `cloudflare/workers/` |
| **RF-022** | El sistema debe permitir gestionar **permisos/excusas** (solicitud, revisión, aprobación). | Alta | `PermisosPage.tsx` |
| **RF-023** | El sistema debe permitir gestionar **citaciones**. | Alta | `CitacionesPage.tsx` |
| **RF-024** | El sistema debe permitir el **seguimiento estudiantil** (académico/disciplinario). | Alta | `SeguimientoPage.tsx` |

## 3.5. Administración

| Código | Requisito | Prioridad | Origen |
|---|---|---|---|
| **RF-025** | El sistema debe permitir la **gestión de usuarios** desde el panel administrativo. | Alta | `AdminPage.tsx` |
| **RF-026** | El sistema debe permitir **asignar roles** (`administrador`, `administrativo`, `docente`, `estudiante`, `padre`). | Alta | `AdminPage.tsx`, `profiles.rol` |
| **RF-027** | El sistema debe permitir **carga masiva de usuarios** desde archivo/paste Excel (con formato predefinido). | Media-Alta | `README.md` sección "Carga Masiva De Usuarios" |
| **RF-028** | El sistema debe vincular **padres/acudientes** con **estudiantes** (`padres_estudiantes`). | Alta | Esquema SQL |
| **RF-029** | El sistema debe vincular **docentes** with **grupos/asignaturas** (`asignaciones_docentes`). | Alta | Esquema SQL |

## 3.6. Requerimientos derivados de la migración (VPS híbrido)

| Código | Requisito | Prioridad | Origen |
|---|---|---|---|
| **RF-030** | El frontend **no debe** depender directamente del SDK `@supabase/supabase-js` para operaciones CRUD/funciones; debe consumir el **API Gateway**. | Alta | Propuesta migración (`proposal.md` 4) |
| **RF-031** | El Gateway debe establecer la sesión/claims JWT por petición (`SET LOCAL request.jwt.claims`) para que las **políticas RLS** sigan funcionando sin cambios. | Crítica | Propuesta migración, tareas 3.2 |
| **RF-032** | Las **políticas RLS** existentes deben seguir siendo válidas tras la migración (paridad funcional de autorización). | Crítica | Propuesta migración 2.1 |
| **RF-033** | El **worker cron** debe reemplazar al Cloudflare Worker para procesar la cola de notificaciones por correo. | Alta | Tareas 4.8, propuesta 2.4 |
| **RF-034** | Las **migraciones de BD** deben consolidarse en un set único, versionado y reproducible. | Alta | Tarea 0.3 |
| **RF-035** | El sistema debe poder levantarse íntegramente con **Docker Compose** (dev/prod). | Alta | Tareas 2.1, propuesta 4 |
