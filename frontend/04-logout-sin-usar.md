# 04 — `logout` definido pero sin usar

**Categoría:** Frontend
**Severidad:** 🟡 Menor
**Archivos afectados:**
- `frontend/src/services/api.js`
- (todas las páginas con header/sidebar)

## Problema
En `api.js` existe la función `logout`:

```js
export const logout = () => {
  localStorage.removeItem('token');
  localStorage.removeItem('usuario');
  window.location.href = '/';
};
```

Pero **nunca se importa ni se usa**, y no hay ningún botón de "cerrar sesión" conectado en la interfaz (los íconos de `settings`/perfil en los headers no hacen nada).

## Por qué ocurre / por qué importa
- El usuario no tiene forma de cerrar sesión desde la app.
- Es código que existe pero no aporta funcionalidad real hasta conectarlo.

## Solución propuesta
- Conectar `logout` a un botón real (por ejemplo en el menú de perfil del header o en "Configuración").
- Considerar usar `useNavigate` en lugar de `window.location.href` para mantener la SPA (ver `frontend/02`).
</content>
