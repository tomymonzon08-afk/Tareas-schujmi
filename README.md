# Tareas — Errores detectados

Cada archivo `.md` describe un error encontrado en el código: **el problema**, **por qué ocurre / por qué importa** y **una solución propuesta**.

Las tareas están organizadas en 3 carpetas según el área:

- [`backend/`](./backend) — errores del servidor (Express / Prisma)
- [`frontend/`](./frontend) — errores de la app React / Vite
- [`conexion/`](./conexion) — errores en la integración frontend ↔ backend (rutas del API, CORS, env, manejo de respuestas)

Dentro de cada carpeta, los archivos están **numerados por importancia** (`01-` = más importante).

## Resumen por prioridad

### 🔴 Críticos (rompen el funcionamiento)
- [backend/01 — schema.prisma desincronizado con la migración](./backend/01-schema-prisma-desincronizado.md)
- [conexion/01 — configuración de entorno, prefijo `/v1` y CORS](./conexion/01-configuracion-env-cors-y-v1.md)

### 🟠 Importantes (lógica / UX)
- [frontend/01 — `useState` usado en lugar de `useEffect`](./frontend/01-usestate-en-lugar-de-useeffect.md)
- [frontend/02 — navegación entre páginas rota](./frontend/02-navegacion-rota.md)
- [conexion/02 — el manejo de 401 trata un login fallido como sesión expirada](./conexion/02-manejo-401-login.md)

### 🟡 Menores (code smells / mejoras)
- [frontend/03 — `ProtectedRoute` no valida el token](./frontend/03-protectedroute-no-valida.md)
- [frontend/04 — `logout` definido pero sin usar](./frontend/04-logout-sin-usar.md)
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
