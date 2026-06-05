# 04 — `getHealth` ignora el `base` de producción

**Categoría:** Conexión frontend ↔ backend
**Severidad:** 🟡 Menor
**Archivos afectados:**
- `frontend/src/services/api.js`
- `frontend/vite.config.js`

## Problema
`getHealth` usa una ruta fija y absoluta respecto del dominio:

```js
export const getHealth = async () => {
  const response = await fetch('/api/health');
  return handleResponse(response);
};
```

Mientras que el resto de los endpoints usan `API_BASE_URL` (configurable). En producción el `base` de Vite es `/~ocho/` (`vite.config.js`), y el `VITE_API_BASE` de producción es `/~ocho/api`, pero `getHealth` apunta a `/api/health`, ignorando ese prefijo.

## Por qué ocurre / por qué importa
- En dev funciona (el proxy mapea `/api`), pero en producción `/api/health` apuntaría fuera del base `/~ocho/` y fallaría el chequeo de "Backend conectado".
- Es una inconsistencia: un endpoint cableado distinto del resto.

## Solución propuesta
Construir la URL de health a partir de la misma base que los demás endpoints, por ejemplo:

```js
// health vive bajo /api (no bajo /api/v1)
const API_ROOT = (import.meta.env.VITE_API_BASE ?? '/api/v1').replace(/\/v1$/, '');
export const getHealth = async () => {
  const response = await fetch(`${API_ROOT}/health`);
  return handleResponse(response);
};
```

O exponer una variable de entorno dedicada para la raíz del API y derivar de ahí tanto `/health` como `/v1/...`.
</content>
