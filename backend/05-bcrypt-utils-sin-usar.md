# 05 — Utilidades `bcrypt.js` sin usar

**Categoría:** Backend
**Severidad:** 🟡 Menor
**Archivos afectados:**
- `backend/src/utils/bcrypt.js`
- `backend/src/services/auth.services.js`

## Problema
`utils/bcrypt.js` exporta `hashPassword` y `comparePassword`, pero **nunca se importan**. En `auth.services.js` se llama a `bcrypt` directamente (`bcrypt.hash(...)`, `bcrypt.compare(...)`).

## Por qué ocurre / por qué importa
Es código muerto. No rompe nada, pero:
- Duplica responsabilidad (dos formas de hashear).
- Confunde sobre cuál es la capa "correcta" para manejar contraseñas.

## Solución propuesta
- Usar las utilidades en `auth.services.js` para centralizar el manejo de contraseñas:
  ```js
  import { hashPassword, comparePassword } from '../utils/bcrypt.js'
  ```
- O bien eliminar `utils/bcrypt.js` si se prefiere usar `bcrypt` directo. Lo importante es no mantener ambas.
</content>
