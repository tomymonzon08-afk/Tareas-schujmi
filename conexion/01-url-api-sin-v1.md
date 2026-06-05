# 01 — La URL del API no coincide con las rutas del backend (`/v1`)

**Categoría:** Conexión frontend ↔ backend
**Severidad:** 🔴 Crítico
**Archivos afectados:**
- `frontend/src/services/api.js`
- `backend/src/routes/index.js`
- `backend/src/routes/v1/index.js`
- `backend/.env.*` (donde está mal puesto `VITE_API_BASE`)

## Problema
El backend monta las rutas bajo `/api/v1/...`:

```
app.use('/api', routes)        // index.js
router.use('/v1', v1Routes)    // routes/index.js
router.use('/auth', authRoutes)// routes/v1/index.js
```

Es decir, login está en `POST /api/v1/auth/login`.

Pero en el frontend (`api.js:4`):

```js
const API_BASE_URL = import.meta.env.VITE_API_BASE ?? 'http://localhost:3000/api/v1';
```

El fallback **sí** incluye `/v1`, pero el valor de `VITE_API_BASE` que aparece en los `.env` es `/api` (**sin `/v1`**). Con ese valor, las llamadas irían a `/api/auth/login` → **404**.

## Por qué ocurre / por qué importa
Hay una inconsistencia entre el fallback del código (`/api/v1`) y el valor configurado en los `.env` (`/api`). Según cuál gane, el login y el registro se rompen con 404. (Notar que `getHealth` sí funciona porque `/api/health` está montado directamente bajo `/api`.)

## Solución propuesta
Unificar el prefijo. Recomendado: que `VITE_API_BASE` incluya `/v1`:

```
VITE_API_BASE=/api/v1
```

Y que el fallback del código coincida:

```js
const API_BASE_URL = import.meta.env.VITE_API_BASE ?? '/api/v1';
```

> Relacionado: `conexion/02` (los `.env` con `VITE_*` están en la carpeta equivocada, así que hoy ni siquiera se leen) y `conexion/04` (`getHealth` usa una ruta fija).
</content>
