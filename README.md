# Tareas — Errores detectados

Cada archivo `.md` describe un error encontrado en el código: **el problema**, **por qué ocurre / por qué importa** y **una solución propuesta**.

Las tareas están organizadas en 3 carpetas según el área:

- [`backend/`](./backend) — errores del servidor (Express / Prisma)
- [`frontend/`](./frontend) — errores de la app React / Vite
- [`conexion/`](./conexion) — errores en la integración frontend ↔ backend (rutas del API, CORS, env, manejo de respuestas)

Dentro de cada carpeta, los archivos están **numerados por importancia** (`01-` = más importante).

## Resumen por prioridad


### 🟡 Menores (code smells / mejoras)
- [frontend/05 — favicon inexistente (`/vite.svg`)](./frontend/05-favicon-inexistente.md)
- [frontend/06 — funcionalidades incompletas](./frontend/06-funcionalidades-incompletas.md)
- [conexion/03 — `getHealth` ignora el `base` de producción](./conexion/03-gethealth-base-produccion.md)
- [backend/03 — scripts de `package.json` / setup de build](./backend/03-scripts-package-json.md)
- [backend/04 — `findById` incluye `proyectos` en el perfil](./backend/04-findbyid-include-proyectos.md)
- [backend/05 — utils `bcrypt.js` sin usar](./backend/05-bcrypt-utils-sin-usar.md)
- [backend/06 — código muerto en `errorHandler`](./backend/06-errorhandler-codigo-muerto.md)
- [backend/07 — `exposedHeaders: Authorization` innecesario](./backend/07-exposedheaders-innecesario.md)
</content>
</invoke>
