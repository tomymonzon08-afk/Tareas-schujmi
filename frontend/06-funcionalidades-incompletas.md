# 06 — Funcionalidades incompletas

**Categoría:** Frontend
**Severidad:** 🟡 Menor
**Archivos afectados:**
- `frontend/src/page/CalendarioSemanal.jsx`
- `frontend/src/page/MisRutinas.jsx`
- `frontend/src/page/BibliotecaEjercicios.jsx`

## Problema
Varias features están a medio implementar (no son bugs en sí, pero dejan la app sin lógica real):

- **Calendario:** el drag & drop tiene handlers que solo cambian clases visuales; al soltar no se mueve nada (`handleDrop` tiene el comentario *"La lógica de movimiento iría aquí"*). Además todos los datos (rutinas, fechas, calorías) están hardcodeados.
- **Mis Rutinas:** el buscador está `disabled` (TODO: "implementar búsqueda cuando haya datos dinámicos"), y las tarjetas de rutinas son un placeholder vacío (`{/* ...sin cambios */}`).
- **Biblioteca de Ejercicios:** los ejercicios están hardcodeados en el JSX; el buscador y los filtros por grupo muscular no filtran nada; "Agregar a Rutina" no hace nada.

## Por qué ocurre / por qué importa
La UI existe pero no está conectada a datos ni a acciones reales. El usuario ve contenido estático/falso. Importa para definir el alcance real del MVP.

## Solución propuesta
- Definir y crear los endpoints del backend que faltan (rutinas, ejercicios) y consumirlos desde el frontend.
- Implementar la lógica de drag & drop con estado real (y persistirlo).
- Conectar buscadores/filtros al estado y a los datos.
- Mientras tanto, marcar claramente lo que es mock para no confundirlo con funcionalidad terminada.
</content>
