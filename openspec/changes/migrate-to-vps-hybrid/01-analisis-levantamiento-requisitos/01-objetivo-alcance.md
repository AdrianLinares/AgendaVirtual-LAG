# 01. Objetivo y Alcance

## 1.1. Objetivo General

Analizar y levantar los requisitos del sistema *Agenda Virtual - Liceo Ángel de la Guarda*, para migrar su arquitectura desde un modelo *serverless* (Supabase + Cloudflare Pages/Worker) hacia una implementación *self-hosted* en un VPS, bajo la arquitectura híbrida propuesta: **Frontend (React + Vite)**, **API Gateway (Node.js + TypeScript)**, **PostgreSQL (`supabase/postgres`)** con **Row Level Security (RLS)** y **orquestación con Docker Compose**.

Este levantamiento se realiza en el marco de la **etapa productiva (pasantía)** para el **Tecnólogo en Análisis y Desarrollo de Software** del **SENA**.

## 1.2. Objetivos Específicos

- Identificar los actores, roles y permisos del sistema actual.
- Levantar los **Requisitos Funcionales (RF)** y **Requisitos No Funcionales (RNF)** a partir del proyecto original `agenda-virtual-liceo`.
- Identificar módulos, reglas de negocio y flujos existentes para garantizar la **paridad funcional**.
- Determinar las restricciones técnicas derivadas de la migración (contrato RLS, autenticación, notificaciones por correo, migraciones de BD).
- Proveer evidencia documental para el primer punto de la ruta SENA: *"Análisis y levantamiento de requisitos"*.

## 1.3. Alcance

**Dentro del alcance:**

- Migración de los módulos operativos existentes: Autenticación, Boletines, Notas, Asistencia, Anuncios, Mensajes, Permisos/Excusas, Seguimiento, Horarios, Citaciones, Calendario y Administración de Usuarios.
- Preservación del modelo de autorización basado en **RLS** de PostgreSQL.
- Sustitución del cliente `@supabase/supabase-js` por un cliente HTTP hacia el **Gateway** propio.
- Migración de la función Edge `send-message-emails` (Deno) hacia un **job Node.js** en contenedor `worker`.
- Despliegue mediante **Docker Compose** (Postgres, Gateway, Frontend, Nginx, Worker).
- Aplicación de principios de **calidad, seguridad informática y trazabilidad** (SDD + TDD).

**Fuera del alcance:**

- Desarrollo de funcionalidades nuevas no presentes en el sistema original.
- Soporte multi-tenant.
- Alta disponibilidad / escalado horizontal.
- Aplicación móvil nativa.
- Reescritura completa de las políticas RLS (se conservarán, con auditoría y validación).
