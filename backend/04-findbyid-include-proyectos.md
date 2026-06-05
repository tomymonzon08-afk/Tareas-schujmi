# 04 — `findById` incluye `proyectos` en la respuesta del perfil

**Categoría:** Backend
**Severidad:** 🟡 Menor
**Archivos afectados:**
- `backend/src/repositories/usuario.repository.js`
- `backend/src/controllers/usuario.controller.js`

## Problema
`usuarioRepository.findById` hace:

```js
return await prisma.usuario.findUnique({
  where: { id },
  include: { proyectos: true }
})
```

Ese `findById` se usa en `getPerfil`, por lo que la respuesta del endpoint `GET /usuario/perfil` incluye el array `proyectos` del usuario.

## Por qué ocurre / por qué importa
- El frontend (`ConfigurarPerfil`) no usa `proyectos`; es información extra que se envía sin necesidad.
- El modelo `Proyecto` parece un remanente de un boilerplate (no hay funcionalidad real de proyectos en la app, que es de fitness/rutinas).

## Solución propuesta
- Si `proyectos` no se necesita en el perfil, quitar el `include` o crear un método específico para el perfil sin relaciones.
- Evaluar si el modelo `Proyecto` debería existir o reemplazarse por las entidades reales del dominio (rutinas, ejercicios).
</content>
