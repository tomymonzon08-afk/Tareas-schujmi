# 05 — Favicon inexistente (`/vite.svg`)

**Categoría:** Frontend
**Severidad:** 🟡 Menor
**Archivos afectados:**
- `frontend/index.html`

## Problema
El `index.html` referencia un favicon que no parece existir en el repo:

```html
<link rel="icon" type="image/svg+xml" href="/vite.svg" />
```

No hay un `public/vite.svg` ni un `vite.svg` en el proyecto.

## Por qué ocurre / por qué importa
- Genera un 404 al cargar la página (inofensivo, pero ensucia la consola/red).
- Además, en producción el `base` es `/~ocho/`, por lo que la ruta absoluta `/vite.svg` apuntaría fuera del base.

## Solución propuesta
- Agregar un favicon real en `frontend/public/` (por ejemplo `favicon.svg`) y apuntar el `<link>` a él.
- O eliminar la línea si no se quiere favicon por ahora.
</content>
