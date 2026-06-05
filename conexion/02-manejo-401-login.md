# 03 — El manejo de 401 trata un login fallido como sesión expirada

**Categoría:** Conexión frontend ↔ backend
**Severidad:** 🟠 Importante
**Archivos afectados:**
- `frontend/src/services/api.js`
- `backend/src/services/auth.services.js`
- `backend/src/middlewares/errorHandler.js`

## Problema
En `api.js`, `handleResponse` reacciona a **cualquier** respuesta `401` borrando la sesión y redirigiendo:

```js
if (response.status === 401) {
  localStorage.removeItem('token');
  localStorage.removeItem('usuario');
  window.location.href = '/';
  throw new Error('Sesión expirada. Por favor, iniciá sesión nuevamente.');
}
```

Pero el backend también devuelve `401` para **credenciales incorrectas** en el login (`auth.services.js` → `errorHandler.js`: *"Credenciales incorrectas"*, status 401).

## Por qué ocurre / por qué importa
Si el usuario se equivoca de contraseña al iniciar sesión:
- En vez de ver *"Credenciales incorrectas"*, se lo redirige al inicio con el mensaje *"Sesión expirada"*.
- El `catch` de `Inicio.jsx` que mostraría el error correcto queda tapado por el redirect.

Es una mala experiencia y un mensaje engañoso.

## Solución propuesta
Distinguir el 401 de "sesión expirada" (token inválido en rutas protegidas) del 401 de "credenciales incorrectas" (login). Opciones:

- No aplicar el redirect global en las llamadas de login/registro (por ejemplo, un flag `skipAuthRedirect` en `authFetch`).
- O diferenciar por la presencia de token: si no había token guardado, no es una sesión expirada sino un error de credenciales, así que solo propagar el mensaje del backend.

Ejemplo:
```js
const hadToken = !!localStorage.getItem('token');
if (response.status === 401 && hadToken) {
  // sesión expirada → limpiar y redirigir
} else if (!response.ok) {
  throw new Error(data.message || 'Error en la petición');
}
```
</content>
