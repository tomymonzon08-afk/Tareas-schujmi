# 01 — Configuración de entorno, prefijo `/v1` y CORS (frontend ↔ backend)

**Categoría:** Conexión frontend ↔ backend
**Severidad:** 🔴 Crítico
**Archivos afectados:**
- `backend/.env.development`, `backend/.env.production`, `backend/.env.example`
- `frontend/` (faltan los `.env`)
- `frontend/src/services/api.js`
- `frontend/vite.config.js`
- `backend/src/app.js`
- `backend/src/routes/index.js`
- `backend/src/routes/v1/index.js`
- `backend/src/server.js`
- `backend/src/utils/jwt.js`
- `backend/prisma/schema.prisma`

> Esta tarea fusiona tres problemas que en realidad describen **un único enredo de configuración** desde distintos ángulos. El paso central —mover los `.env` `VITE_*` de `backend/` a `frontend/`— aparecía repetido en los tres.

---

## Problema A — Variables de entorno mal ubicadas y faltantes

Los archivos `.env` del backend contienen **variables de frontend** (`VITE_BASE_PATH`, `VITE_API_BASE`, `VITE_API_PROXY_TARGET`) y **les faltan** las dos variables que el backend realmente necesita:

- `DATABASE_URL` — requerida por Prisma (`schema.prisma`: `url = env("DATABASE_URL")`).
- `JWT_SECRET` — requerida por `utils/jwt.js`.

Además, `server.js:1` usa `import 'dotenv/config'`, que **solo carga el archivo `.env`** (no `.env.development` ni `.env.production`), y no existe ningún `.env`.

### Por qué importa
- En `utils/jwt.js:5-8`, si `JWT_SECRET` no está definida se ejecuta `process.exit(1)` → **el server se cierra al arrancar**.
- Prisma no puede conectarse sin `DATABASE_URL`.
- Aunque las variables correctas existieran en `.env.development`/`.env.production`, `dotenv/config` no las leería porque solo carga `.env`.

**Resultado:** el backend no levanta en ningún entorno.

---

## Problema B — Los `.env` `VITE_*` están en la carpeta equivocada

1. **Los archivos con variables `VITE_*` están en `backend/`**, pero Vite **solo lee los `.env` de `frontend/`**. Como en `frontend/` no hay ningún `.env`, `import.meta.env.VITE_API_BASE` queda `undefined` y se usa el fallback absoluto `http://localhost:3000/api/v1`.
2. Esa URL absoluta **saltea el proxy de Vite** (`vite.config.js` define un proxy `/api → http://localhost:3000`), por lo que el navegador hace una petición cross-origin desde `http://localhost:5173`.
3. En `app.js`, `allowedOrigins` incluye `4173` y `3000` pero **no `5173`**, que es el puerto real del dev server (`vite.config.js: server.port = 5173`). → **CORS bloquea login/registro en desarrollo.**

### Por qué importa
- La intención original (a juzgar por `VITE_API_BASE=/api`) era que el frontend usara rutas relativas y el **proxy** de Vite, evitando CORS. Pero al estar los `.env` en el lugar equivocado, eso no sucede.
- El resultado: en dev, las peticiones salen como absolutas a `:3000` y chocan con CORS porque `5173` no está permitido.

---

## Problema C — La URL del API no coincide con las rutas (`/v1`)

El backend monta las rutas bajo `/api/v1/...`:

```
app.use('/api', routes)         // index.js
router.use('/v1', v1Routes)     // routes/index.js
router.use('/auth', authRoutes) // routes/v1/index.js
```

Es decir, login está en `POST /api/v1/auth/login`.

Pero en el frontend (`api.js:4`):

```js
const API_BASE_URL = import.meta.env.VITE_API_BASE ?? 'http://localhost:3000/api/v1';
```

El fallback **sí** incluye `/v1`, pero el valor de `VITE_API_BASE` que aparece en los `.env` es `/api` (**sin `/v1`**). Con ese valor, las llamadas irían a `/api/auth/login` → **404**.

### Por qué importa
Hay una inconsistencia entre el fallback del código (`/api/v1`) y el valor configurado en los `.env` (`/api`). Según cuál gane, el login y el registro se rompen con 404. (Notar que `getHealth` sí funciona porque `/api/health` está montado directamente bajo `/api`.)

---

## Solución propuesta (un solo cambio coordinado)

1. **Mover los `.env` de frontend a `frontend/`**, dejando solo variables del frontend, con el prefijo `/v1` incluido:
   ```
   # frontend/.env.development
   VITE_API_BASE=/api/v1
   ```
   Con un valor **relativo**, las peticiones pasan por el **proxy** de Vite y no hay CORS en dev.

2. **Dejar en `backend/` solo las variables reales del backend.** Ejemplo de `backend/.env.example`:
   ```
   DATABASE_URL="file:./dev.db"
   JWT_SECRET="cambiame-por-un-secreto-largo-y-aleatorio"
   PORT=3000
   ```
   Crear el `.env` real (no versionado; ya está en `.gitignore`) con esos valores. Si se quieren entornos separados, cargar el archivo correcto explícitamente en `server.js`:
   ```js
   import dotenv from 'dotenv'
   dotenv.config({ path: `.env.${process.env.NODE_ENV || 'development'}` })
   ```
   en lugar de `import 'dotenv/config'`.

3. **Unificar el prefijo `/v1`.** Que el fallback del código coincida con el valor configurado:
   ```js
   const API_BASE_URL = import.meta.env.VITE_API_BASE ?? '/api/v1';
   ```

4. **Agregar `http://localhost:5173` (y `127.0.0.1:5173`) a `allowedOrigins`** en `app.js`, para cubrir el caso de peticiones directas además del proxy.

> Relacionado: `conexion/02` (manejo de 401) y `conexion/03` (`getHealth` usa una ruta fija que ignora el prefijo).
