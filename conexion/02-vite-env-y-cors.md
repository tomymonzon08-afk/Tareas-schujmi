# 02 — Archivos VITE en carpeta equivocada + CORS sin el puerto 5173

**Categoría:** Conexión frontend ↔ backend
**Severidad:** 🔴 Crítico
**Archivos afectados:**
- `backend/.env.development`, `backend/.env.production`, `backend/.env.example` (variables `VITE_*` mal ubicadas)
- `frontend/` (faltan los `.env`)
- `frontend/src/services/api.js`
- `frontend/vite.config.js`
- `backend/src/app.js`

## Problema
Hay dos errores encadenados en la integración de desarrollo:

1. **Los archivos con variables `VITE_*` están en `backend/`**, pero Vite **solo lee los `.env` de `frontend/`**. Como en `frontend/` no hay ningún `.env`, `import.meta.env.VITE_API_BASE` queda `undefined` y se usa el fallback absoluto `http://localhost:3000/api/v1`.
2. Esa URL absoluta **saltea el proxy de Vite** (`vite.config.js` define un proxy `/api → http://localhost:3000`), por lo que el navegador hace una petición cross-origin desde `http://localhost:5173`.
3. En `app.js`, `allowedOrigins` incluye `4173` y `3000` pero **no `5173`**, que es el puerto real del dev server (`vite.config.js: server.port = 5173`). → **CORS bloquea login/registro en desarrollo.**

## Por qué ocurre / por qué importa
- La intención original (a juzgar por `VITE_API_BASE=/api`) era que el frontend usara rutas relativas y el **proxy** de Vite, evitando CORS. Pero al estar los `.env` en el lugar equivocado, eso no sucede.
- El resultado: en dev, las peticiones salen como absolutas a `:3000` y chocan con CORS porque `5173` no está permitido.

## Solución propuesta
1. **Mover los `.env` de frontend a `frontend/`**, dejando solo variables del frontend:
   ```
   # frontend/.env.development
   VITE_API_BASE=/api/v1
   ```
   (y dejar en `backend/` solo `DATABASE_URL`/`JWT_SECRET` — ver `backend/02`.)
2. Con `VITE_API_BASE=/api/v1` (relativo), las peticiones pasan por el **proxy** de Vite y no hay CORS en dev.
3. Igualmente, agregar `http://localhost:5173` (y `127.0.0.1:5173`) a `allowedOrigins` en `app.js` para cubrir el caso de peticiones directas.

> Relacionado: `conexion/01` (prefijo `/v1`) y `backend/02` (variables del backend faltantes).
</content>
