# 07 — `exposedHeaders: ['Authorization']` innecesario

**Categoría:** Backend
**Severidad:** 🟡 Menor
**Archivos afectados:**
- `backend/src/app.js`

## Problema
La configuración de CORS expone el header `Authorization` en las respuestas:

```js
exposedHeaders: ['Authorization']
```

Pero el backend **nunca devuelve el token en un header**: lo manda en el body de la respuesta (`{ token, usuario }` desde `auth.services.js`).

## Por qué ocurre / por qué importa
No causa errores, pero es configuración sin efecto que puede confundir: sugiere que el cliente debería leer el token de un header de respuesta, cuando en realidad lo lee del body (`api.js` → `response.data.token`).

## Solución propuesta
- Quitar `exposedHeaders: ['Authorization']` si el token seguirá yendo en el body.
- O, si se decide mandar el token por header, hacerlo consistente en backend y frontend.
</content>
