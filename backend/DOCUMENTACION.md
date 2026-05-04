# Documentación técnica del backend (LTI)

Este documento resume rutas, controladores, servicios y tecnologías del servidor Node/Express ubicado en esta carpeta.

## Tecnologías

| Área | Tecnología |
|------|------------|
| Lenguaje | TypeScript |
| Framework HTTP | Express 4 |
| ORM / acceso a datos | Prisma 5 + PostgreSQL |
| Variables de entorno | dotenv |
| CORS | cors (origen permitido: `http://localhost:3000`) |
| Subida de ficheros | multer (ruta `POST /upload`) |
| Validación candidatos | Lógica en `application/validator.ts` + modelos de dominio |

## Estructura relevante (`backend/src`)

```
src/
├── index.ts                 # Arranque Express, middleware Prisma, montaje de rutas
├── application/
│   ├── services/
│   │   ├── candidateService.ts
│   │   ├── candidateStageService.ts   # Actualización de etapa (Application.currentInterviewStep)
│   │   ├── fileUploadService.ts
│   │   └── positionCandidateService.ts # Listado candidatos por posición + media de scores
│   └── validator.ts
├── domain/models/           # Modelos de dominio (Prisma encapsulado en clases)
├── presentation/controllers/
│   ├── candidateController.ts  # POST/GET candidatos, PUT etapa
│   └── positionController.ts   # GET candidatos por posición
└── routes/
    ├── candidateRoutes.ts
    └── positionRoutes.ts
```

## Rutas HTTP

| Método | Ruta | Descripción |
|--------|------|-------------|
| `POST` | `/candidates` | Alta de candidato (cuerpo JSON + relaciones opcionales) |
| `GET` | `/candidates/:id` | Detalle de candidato por id |
| `PUT` | `/candidates/:id/stage` | Actualiza la etapa actual de la **aplicación** candidato–posición (ver cuerpo) |
| `GET` | `/positions/:id/candidates` | Candidatos con aplicación activa para la posición `id` |
| `POST` | `/upload` | Subida de fichero |
| `GET` | `/` | Comprobación de servicio |

### `GET /positions/:id/candidates`

- **Controlador:** `presentation/controllers/positionController.ts` → `getPositionCandidates`
- **Servicio:** `application/services/positionCandidateService.ts` → `getCandidatesByPositionId`
- **Respuesta:** JSON con `position_id` y array `candidatos`. Cada elemento incluye `nombre_completo`, `current_interview_step` (id, name, order_index desde `InterviewStep`), `average_score` (media aritmética de `Interview.score` de esa aplicación; `null` si no hay puntuaciones).

### `PUT /candidates/:id/stage`

- **Controlador:** `presentation/controllers/candidateController.ts` → `putCandidateStage`
- **Servicio:** `application/services/candidateStageService.ts` → `updateApplicationInterviewStage`
- **Cuerpo JSON obligatorio:**
  - `positionId` (número): posición sobre la que se actualiza la aplicación.
  - `interviewStepId` (número): nueva etapa; debe pertenecer al `InterviewFlow` de esa posición.
- **Errores:** 404 si no hay aplicación para ese par candidato–posición; 400 si la etapa no es válida para el flujo de la posición.

## Compilación y arranque

```bash
npm install
npx prisma generate
npm run build
npm start
```

Desarrollo con recarga: `npm run dev` (si `ts-node-dev` está configurado en el proyecto).

La URL por defecto del servidor en código es el **puerto 3010**.
