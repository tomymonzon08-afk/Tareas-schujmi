# 02 — Navegación entre páginas rota

**Categoría:** Frontend
**Severidad:** 🟠 Importante
**Archivos afectados:**
- `frontend/src/page/MisRutinas.jsx`
- `frontend/src/page/BibliotecaEjercicios.jsx`
- `frontend/src/page/CalendarioSemanal.jsx`
- (referencia) `frontend/src/App.jsx`

## Problema
Todos los links del sidebar, la barra superior y la navegación móvil usan `href="#"` y no usan el `<Link>` / `useNavigate` de `react-router-dom`. Ejemplo:

```jsx
<a className="..." href="#">
  <span className="material-symbols-outlined">menu_book</span>
  <span className="font-label-md">Biblioteca de Ejercicios</span>
</a>
```

No hay forma real de navegar entre las páginas protegidas (`/biblioteca`, `/calendario`, `/mis-rutinas`, `/configurar-perfil`).

## Por qué ocurre / por qué importa
- `href="#"` solo cambia el hash de la URL; no enruta a otra página y suele hacer scroll al inicio.
- La única "navegación" existente es vía `window.location.href` después del login/registro (`Inicio.jsx`, `ConfigurarPerfil.jsx`), lo que fuerza recargas completas y rompe la experiencia SPA.

## Solución propuesta
Reemplazar los `<a href="#">` de navegación por `<Link>` de react-router:

```jsx
import { Link } from 'react-router-dom';

<Link to="/biblioteca" className="...">
  <span className="material-symbols-outlined">menu_book</span>
  <span className="font-label-md">Biblioteca de Ejercicios</span>
</Link>
```

Y considerar reemplazar los `window.location.href = '/...'` por `useNavigate()` para mantener la navegación dentro de la SPA. Idealmente extraer el sidebar/header a un componente compartido para no duplicar la navegación en cada página.
</content>
