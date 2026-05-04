# Prompts ABR — AI4Devs-backend-202602-Seniors

Registro de trabajo solicitado para el módulo LTI (backend + documentación), abril.

## Contexto del proyecto

- Monorepo con `frontend/` (React, Create React App) y `backend/` (Express + TypeScript + Prisma + PostgreSQL).
- Dominio de reclutamiento: candidatos, posiciones, aplicaciones, pasos de entrevista e entrevistas con puntuación.

## Prompt 1: Documentación README (español)

Generar documentación en markdown para el README que incluya:

1. Estructura de carpetas del repositorio.
2. Tecnologías usadas (frontend y backend).
3. Arquitectura de backend (capas: presentación, aplicación, dominio, Prisma) y de frontend (componentes, servicios, CRA).
4. Pasos completos para levantar el entorno: variables `.env`, Docker Compose para PostgreSQL, `npm install`, `prisma generate`, migraciones y semilla de datos, arranque backend y frontend.

## Prompt 2: Endpoint `GET /positions/:id/candidates`

- Listar todas las **aplicaciones** asociadas a un `positionId`.
- Por cada aplicación devolver:
  - Nombre completo del candidato (tabla `Candidate`: `firstName` + `lastName`).
  - Fase actual: `current_interview_step` basado en `Application.currentInterviewStep` → relación `InterviewStep` (nombre e índice de orden).
  - **Puntuación media** del candidato en esa aplicación: media de `Interview.score` de las entrevistas ligadas a esa `Application`.

## Prompt 3: Endpoint `PUT /candidates/:id/stage`

- Actualizar la etapa del proceso para un candidato concreto.
- Implementación alineada con el modelo: actualizar `Application.currentInterviewStep` identificando la fila por `candidateId` (parámetro de ruta) y `positionId` (cuerpo JSON), validando que `interviewStepId` pertenezca al flujo de la posición.

## Restricciones de entrega

- Texto de usuario final en **español**.
- Cambios de código de API en la carpeta `backend/`.
- Resumen técnico de rutas/controladores/tecnologías adicional en `backend/DOCUMENTACION.md`.
- Este fichero en `prompts/prompts-ABR.md`.
