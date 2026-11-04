# 02. Actores y Roles

El sistema cuenta con un modelo de autorización basado en roles, almacenados en la tabla `profiles.rol` y reforzados por **Row Level Security (RLS)** en PostgreSQL.

## 2.1. Identificación de Actores

| Actor | Tipo | Descripción |
|---|---|---|
| **Administrador** | Interno (Gestión) | Responsable de la configuración y administración integral del sistema. |
| **Administrativo** | Interno (Gestión Académica) | Apoyo a la coordinación académica. Gestiona boletines, permisos y procesos administrativos. |
| **Docente** | Interno (Académico) | Encargado de la labor académica: notas, asistencia, anuncios, comunicación y citaciones. |
| **Estudiante** | Externo/Interno (Usuario final) | Usuario del sistema para consulta académica y gestión de solicitudes. |
| **Padre/Acudiente** | Externo/Interno (Usuario final) | Acudiente responsable, vinculado a uno o varios estudiantes. |

## 2.2. Matriz de Roles y Permisos

| Módulo / Funcionalidad | Administrador | Administrativo | Docente | Estudiante | Padre/Acudiente |
|---|---|---|---|---|---|
| **Autenticación / Sesión** | Sí | Sí | Sí | Sí | Sí |
| **Dashboard personalizado** | Sí (completo) | Sí (acorde a su rol) | Sí (acorde a su rol) | Sí (acorde a su rol) | Sí (acorde a su rol) |
| **Gestión de Usuarios (Panel Admin)** | Sí | No | No | No | No |
| **Boletines** | Sí | Sí (creación/gestión) | Consultivo/según asignación* | Sí (solo sus boletines) | Sí (solo boletines de estudiante asociado) |
| **Notas** | Sí | Sí* | Sí (registro/consulta por grupos/asignaturas asignadas) | Sí (solo las suyas) | Sí (solo notas del estudiante asociado) |
| **Asistencia** | Sí | Sí* | Sí (registro por grupo/asignatura) | Sí (solo la suya) | Sí (solo asistencia del estudiante asociado) |
| **Anuncios** | Sí | Sí | Sí (creación/comunicación) | Sí (lectura según alcance) | Sí (lectura según alcance) |
| **Mensajes Internos** | Sí | Sí | Sí | Sí | Sí |
| **Permisos / Excusas** | Sí (aprobación/gestión) | Sí (aprobación) | Sí (revisión según contexto) | Sí (solicitud propia) | Sí (solicitud en nombre del estudiante asociado) |
| **Seguimiento Estudiantil** | Sí | Sí | Sí (según grupos/asignación) | Limitado | Limitado (del estudiante asociado) |
| **Horarios** | Sí | Sí | Sí (consulta de sus horarios) | Sí (consulta) | Sí (consulta del estudiante asociado) |
| **Citaciones** | Sí | Sí | Sí (creación/gestión) | Sí (consulta) | Sí (consulta del estudiante asociado) |
| **Calendario de Eventos** | Sí | Sí | Sí | Sí | Sí |
| **Cambio de Contraseña** | Sí | Sí | Sí | Sí | Sí |
| **Recuperación/Restablecimiento de Contraseña** | Sí (flujo disponible) | Sí | Sí | Sí | Sí |

\* El acceso se encuentra estrictamente limitado por **RLS** (`auth.uid()`, `get_user_role()`, `is_admin()`, `is_docente()`) y por las relaciones `padres_estudiantes`, `asignaciones_docentes`, `estudiantes_grupos`.

## 2.3. Notas sobre el modelo de autorización

- **Autenticación**: Gestionada por GoTrue/Supabase Auth en el origen. En la migración se busca replicar el **contrato JWT** para mantener compatibilidad con las políticas RLS existentes.
- **Políticas RLS**: Presentes en todas las tablas. Se consideran **activos críticos de seguridad** y deben conservarse sin alteración funcional.
- **Relaciones**: `padres_estudiantes` vincula padres con estudiantes; `asignaciones_docentes` vincula docentes con grupos/asignaturas.
- **Sesión**: En el origen se usa `localStorage` (con comportamiento documentado y con `sessionStorage` mencionado en README como nota histórica). Este detalle se tendrá en cuenta al migrar el flujo de autenticación al Gateway.
