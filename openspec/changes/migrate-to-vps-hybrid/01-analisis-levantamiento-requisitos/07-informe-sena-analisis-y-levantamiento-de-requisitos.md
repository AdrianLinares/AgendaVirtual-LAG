# INFORME ACADÉMICO - SENA

**Programa:** Tecnólogo en Análisis y Desarrollo de Software  
**Etapa:** Productiva (Pasantía)  
**Proyecto:** Agenda Virtual - Liceo Ángel de la Guarda  
**Modalidad:** Migración de arquitectura *Serverless* a *Self-Hosted VPS* (Arquitectura Híbrida)  
**Alumno:** Adrian  
**Fecha:** Octubre 2026  

---

## 1. ANÁLISIS Y LEVANTAMIENTO DE REQUISITOS

### 1.1. Objetivo

Identificar, documentar y formalizar los requisitos funcionales y no funcionales del sistema *Agenda Virtual - Liceo Ángel de la Guarda*, con base en el proyecto original `agenda-virtual-liceo`, para dar soporte a su migración hacia una infraestructura autoalojada en VPS, aplicando los principios de **Análisis y Desarrollo de Software**, **Calidad de Software** y **Seguridad Informática**, bajo metodologías **SDD** (Spec-Driven Development) y **TDD** (Test-Driven Development).

### 1.2. Justificación

El sistema actual funciona sobre infraestructura administrada (*Supabase*, *Cloudflare Pages*, *Cloudflare Workers*). La migración tiene como propósito comprender, replicar y reforzar la arquitectura, preservando el modelo de seguridad basado en **Row Level Security (RLS)**, garantizando la **paridad funcional**, **reproducibilidad** con **Docker Compose** y dejando evidencia documental de todo el proceso, acorde a lo exigido en la etapa productiva del SENA.

### 1.3. Alcance

Se considera el conjunto de módulos operativos del sistema original: Autenticación, Boletines, Notas, Asistencia, Anuncios, Mensajes, Permisos/Excusas, Seguimiento, Horarios, Citaciones, Calendario y Panel de Administración de Usuarios. La migración contempla la creación de un **API Gateway propio**, la migración del cron de notificaciones y el despliegue con **Nginx + Certbot**.

### 1.4. Actores del Sistema

| Actor | Descripción |
|---|---|
| **Administrador** | Control total del sistema. Gestión de usuarios, roles y configuración. |
| **Administrativo** | Gestión académica, creación de boletines, aprobación de permisos. |
| **Docente** | Registro de notas y asistencia, anuncios, citaciones, comunicación. |
| **Estudiante** | Consulta académica, solicitud de permisos, mensajería. |
| **Padre/Acudiente** | Seguimiento del estudiante asociado, solicitudes en su nombre, comunicación. |

### 1.5. Requisitos Funcionales (Resumen)

Los requisitos fueron levantados mediante análisis del código fuente, rutas, lógica de autenticación, esquema SQL, funciones Edge y worker cron. Se identificaron **35 requisitos funcionales (RF-001 a RF-035)** distribuidos en: autenticación/sesión, dashboard/rutas, gestión académica, comunicación/seguimiento, administración y requerimientos derivados de la migración (Gateway, RLS, worker, migraciones, Docker).

Ver detalle: [03-requerimientos-funcionales.md](./03-requerimientos-funcionales.md)

### 1.6. Requisitos No Funcionales (Resumen)

Se identificaron **32 requisitos no funcionales (RNF-001 a RNF-032)** agrupados en: arquitectura/diseño, seguridad informática, calidad/pruebas/metodología, desempeño/operación/observabilidad y usabilidad/documentación académica. Se destaca la aplicación estricta de **SDD + TDD**, preservación de **RLS**, gestión segura de secretos, backups con prueba de restore y evidencias para entrega académica.

Ver detalle: [04-requerimientos-no-funcionales.md](./04-requerimientos-no-funcionales.md)

### 1.7. Fuentes de Información

La información fue obtenida del proyecto original `agenda-virtual-liceo`, documentación técnica (`README.md`, `package.json`, `supabase-schema.sql`), migraciones históricas, código fuente (`App.tsx`, `auth-store.ts`, páginas), propuestas y backlog del proyecto destino `AgendaVirtual-LAG`, además de la ruta SENA y referencias **OWASP**.

Ver detalle: [05-fuentes-de-informacion.md](./05-fuentes-de-informacion.md)

### 1.8. Trazabilidad

Se estableció la matriz de trazabilidad entre requisitos, módulos, actores, fases de la ruta SENA y tareas del backlog `migrate-to-vps-hybrid`. Esto garantiza que cada requisito tenga una justificación, una fuente y un destino dentro del proceso de migración.

Ver detalle: [06-matriz-trazabilidad.md](./06-matriz-trazabilidad.md)

### 1.9. Conclusiones del Análisis

- **Sistema bien definido:** El proyecto original cuenta módulos operativos claramente identificables y un modelo de autorización robusto basado en RLS.
- **Riesgo crítico identificado:** La dependencia de `auth.uid()` / `auth.users` requiere validar el contrato con `supabase/postgres` + GoTrue en un **spike bloqueante** (Fase 0.2) antes de implementar el Gateway.
- **Migración viable con enfoque híbrido:** Preservar Postgres + RLS y agregar un Gateway fino es la estrategia correcta para mantener paridad funcional sin reescribir toda la lógica de autorización.
- **Cumplimiento metodológico:** Se integra la ruta SENA con SDD+TDD, con evidencias obligatorias para los puntos posteriores.
- **Listos para diseño:** Con el SRS mínimo documentado, se puede dar inicio al **punto 2: Diseño de la solución** (Fase 1 del backlog).

### 1.10. Recomendaciones

1. **Ejecutar Fase 0.2 primero:** Realizar el spike de `supabase/postgres` + GoTrue, cargar schema/policies y probar `auth.uid()` y una policy real. Documentar como ADR.
2. **Consolidar migraciones:** Ejecutar **0.3** para tener un único set versionado antes de cualquier despliegue reproducible.
3. **Formalizar amenazas:** Desarrollar el modelo **STRIDE liviano** (Fase 0.5) complementando el análisis con seguridad.
4. **Mantener trazabilidad:** Seguir actualizando la matriz al crear sub-cambios SDD por fase.
5. **Aplicar TDD desde el inicio:** No escribir implementación sin test que falle primero, registrando evidencia en `apply-progress.md`.

---

**Elaborado por:** Adrian  
**Aprobado para continuar:** Punto 1 completado - Listo para **Punto 2: Diseño de la solución**  
**Ubicación de evidencia:** `openspec/changes/migrate-to-vps-hybrid/01-analisis-levantamiento-requisitos/`  
**Seguimiento ODD:** `odd/tasks/analisis-levantamiento-requisitos.md`