# SafePlace — Backend API

API REST construida con Node.js + Express para el sistema de monitoreo biométrico SafePlace. Gestiona autenticación, trabajadores, mediciones, alertas y dispositivos conectados.

## Stack

- **Runtime:** Node.js 20
- **Framework:** Express 4
- **Base de datos:** PostgreSQL 15
- **Auth:** JWT + bcrypt
- **ORM:** pg (driver nativo)

## Arquitectura

```
src/
├── routes/          # Endpoints REST
├── controllers/     # Manejo de req/res
├── services/        # Lógica de negocio y reglas de alertas
├── repositories/    # Queries SQL
├── models/          # Validaciones de entidades
├── middlewares/     # Auth JWT, RBAC, error handler
└── database/        # Conexión PostgreSQL
```

## Endpoints

| Método | Ruta | Auth | Descripción |
|--------|------|------|-------------|
| POST | `/api/v1/auth/register` | No | Registrar usuario |
| POST | `/api/v1/auth/login` | No | Login, devuelve JWT |
| GET | `/api/v1/workers` | JWT | Listar trabajadores |
| POST | `/api/v1/workers` | JWT | Crear trabajador |
| PUT | `/api/v1/workers/:id` | JWT | Editar trabajador |
| PATCH | `/api/v1/workers/:id/deactivate` | JWT | Desactivar trabajador |
| GET | `/api/v1/alerts` | JWT | Listar alertas |
| PATCH | `/api/v1/alerts/:id/status` | JWT | Actualizar estado de alerta |
| POST | `/api/v1/measurements` | JWT | Ingresar medición biométrica |
| GET | `/api/v1/measurements/:worker_id` | JWT | Historial de mediciones |
| GET | `/api/v1/devices` | JWT | Listar dispositivos |
| POST | `/api/v1/devices` | JWT | Registrar dispositivo |
| GET | `/health` | No | Health check |

## Correr en local con Docker

Requiere [Docker Desktop](https://www.docker.com/products/docker-desktop/) instalado.

```bash
# Clonar el repo
git clone https://github.com/mackhood/safeplace-backend.git
cd safeplace-backend

# Copiar variables de entorno
cp .env.example .env

# Levantar backend + PostgreSQL
docker compose up -d

# Ver logs
docker compose logs -f
```

El servidor queda disponible en `http://localhost:8000`.

Para detener:
```bash
docker compose down
```

Para detener y borrar la base de datos:
```bash
docker compose down -v
```

## Correr en local sin Docker

```bash
npm install
# Configurar .env con una PostgreSQL accesible
npm run dev
```

## Variables de entorno

Copiá `.env.example` a `.env` y completá los valores:

```env
PORT=8000
DATABASE_URL=postgresql://user:password@host/database?sslmode=require
JWT_SECRET=tu_secreto_jwt
JWT_EXPIRES_IN=8h
DEVICE_TOKEN_SECRET=tu_secreto_device
NODE_ENV=development
```

## Deploy en Render

El backend está deployado en [Render](https://render.com) con deploy automático en cada push a `main`.

**URL de producción:** `https://safeplace-backend.onrender.com`

**Configuración en Render:**

| Campo | Valor |
|-------|-------|
| Runtime | Node |
| Build Command | `npm install` |
| Start Command | `npm start` |

Variables de entorno configuradas directamente en el dashboard de Render.

> **Nota:** Con el plan gratuito de Render el servicio se duerme tras 15 minutos de inactividad. La primera request puede tardar ~30 segundos en responder.
