# 03 — `ProtectedRoute` no valida el token

**Categoría:** Frontend
**Severidad:** 🟡 Menor
**Archivos afectados:**
- `frontend/src/components/ProtectedRoute.jsx`

## Problema
`ProtectedRoute` solo verifica que **exista** un token en `localStorage`:

```js
const token = localStorage.getItem('token');
if (!token) return <Navigate to="/" replace />;
return children;
```

No valida si el token es válido o si está vencido.

## Por qué ocurre / por qué importa
- Un token vencido o manipulado igual deja pasar al usuario a la pantalla protegida.
- El error recién se descubre cuando se hace el primer fetch y el backend responde 401 (lo cual además dispara el redirect agresivo descrito en `conexion/02`).
- Es una validación de seguridad superficial (aunque la validación real siempre debe ocurrir en el backend).

## Solución propuesta
- Como mínimo, decodificar el token en el cliente y chequear `exp` antes de renderizar (sabiendo que esto es solo UX; la autoridad es el backend).
- O hacer una verificación liviana contra el backend (por ejemplo `GET /usuario/perfil`) al montar las rutas protegidas, y limpiar la sesión si falla.
- Mantener la validación real de autorización en el backend (ya existe vía `authMiddleware.verifyToken`).
</content>
