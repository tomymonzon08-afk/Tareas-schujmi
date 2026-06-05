# 01 — `useState` usado en lugar de `useEffect`

**Categoría:** Frontend
**Severidad:** 🟠 Importante
**Archivos afectados:**
- `frontend/src/page/Inicio.jsx`

## Problema
El health check del backend se dispara así (`Inicio.jsx:15`):

```js
useState(() => {
  getHealth()
    .then((data) => setBackendStatus(data.status))
    .catch((err) => setBackendError(err.message));
}, []);
```

Se usa `useState` cuando debería usarse `useEffect`.

## Por qué ocurre / por qué importa
- `useState(fn)` interpreta `fn` como **inicializador perezoso** del estado y **ignora el segundo argumento** (`[]`), que es propio de `useEffect`.
- El efecto secundario (la llamada al API) se ejecuta dentro del cálculo de estado, lo cual es un antipatrón: React puede invocar el inicializador de forma inesperada y en `StrictMode` se renderiza dos veces.
- Funciona "de casualidad" hoy, pero es frágil y conceptualmente incorrecto.

## Solución propuesta
Reemplazar por `useEffect` (e importarlo):

```js
import { useState, useEffect } from 'react';

useEffect(() => {
  getHealth()
    .then((data) => setBackendStatus(data.status))
    .catch((err) => setBackendError(err.message));
}, []);
```
</content>
