# 02 — Variables de entorno del backend faltantes

**Categoría:** Backend
**Severidad:** 🔴 Crítico
**Archivos afectados:**
- `backend/.env.development`
- `backend/.env.production`
- `backend/.env.example`
- `backend/src/server.js`
- `backend/src/utils/jwt.js`
- `backend/prisma/schema.prisma`

## Problema
Los archivos `.env` del backend contienen **variables de frontend** (`VITE_BASE_PATH`, `VITE_API_BASE`, `VITE_API_PROXY_TARGET`) y **les faltan** las dos variables que el backend realmente necesita:

- `DATABASE_URL` — requerida por Prisma (`schema.prisma`: `url = env("DATABASE_URL")`).
- `JWT_SECRET` — requerida por `utils/jwt.js`.

Además, `server.js:1` usa `import 'dotenv/config'`, que **solo carga el archivo `.env`** (no `.env.development` ni `.env.production`), y no existe ningún `.env`.

## Por qué ocurre / por qué importa
- En `utils/jwt.js:5-8`, si `JWT_SECRET` no está definida se ejecuta `process.exit(1)` → **el server se cierra al arrancar**.
- Prisma no puede conectarse sin `DATABASE_URL`.
- Aunque las variables correctas existieran en `.env.development`/`.env.production`, `dotenv/config` no las leería porque solo carga `.env`.

**Resultado:** el backend no levanta en ningún entorno.

## Solución propuesta
1. Mover las variables `VITE_*` a la carpeta `frontend/` (ver `conexion/02`), ya que no pertenecen al backend.
2. Definir en el backend las variables reales. Ejemplo de `backend/.env.example`:
   ```
   DATABASE_URL="file:./dev.db"
   JWT_SECRET="cambiame-por-un-secreto-largo-y-aleatorio"
   PORT=3000
   ```
3. Crear el `.env` real (no versionado; ya está en `.gitignore`) con esos valores.
4. Si se quieren entornos separados, cargar el archivo correcto explícitamente, por ejemplo:
   ```js
   import dotenv from 'dotenv'
   dotenv.config({ path: `.env.${process.env.NODE_ENV || 'development'}` })
   ```
   en lugar de `import 'dotenv/config'`.
</content>
