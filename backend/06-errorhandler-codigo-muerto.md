# 06 — Código muerto en `errorHandler`

**Categoría:** Backend
**Severidad:** 🟡 Menor
**Archivos afectados:**
- `backend/src/middlewares/errorHandler.js`

## Problema
`errorHandler` maneja el error `"Ejercicio no encontrado"`:

```js
if (err.message === 'Ejercicio no encontrado') {
  return res.status(404).json({ ... })
}
```

Pero **no existe ninguna funcionalidad de ejercicios en el backend** (no hay controller, service ni modelo de ejercicios). Ese error nunca se lanza.

## Por qué ocurre / por qué importa
Probablemente quedó de un copiado de otro proyecto o de una feature planeada. No rompe nada, pero ensucia el manejador y sugiere funcionalidad que no existe.

## Solución propuesta
- Eliminar el bloque si no se va a implementar ejercicios en el backend.
- O, si está planeado, dejar un comentario `// TODO` indicando que la feature aún no existe, para que no parezca activa.
</content>
