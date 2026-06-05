# 01 — `schema.prisma` desincronizado con la migración

**Categoría:** Backend
**Severidad:** 🔴 Crítico
**Archivos afectados:**
- `backend/prisma/schema.prisma`
- `backend/prisma/migrations/20260531115259_add_perfil_fields/migration.sql`
- `backend/src/controllers/usuario.controller.js`
- `backend/src/repositories/usuario.repository.js`

## Problema
La migración `add_perfil_fields` agrega cuatro columnas a la tabla `Usuario`:

```sql
ALTER TABLE "Usuario" ADD COLUMN "edad" INTEGER;
ALTER TABLE "Usuario" ADD COLUMN "peso" REAL;
ALTER TABLE "Usuario" ADD COLUMN "altura" INTEGER;
ALTER TABLE "Usuario" ADD COLUMN "nivelActividad" TEXT;
```

Pero el modelo `Usuario` en `schema.prisma` **no declara esos campos**: solo tiene `id`, `email`, `password`, `nombre`, `rol`, `createdAt`, `updatedAt` y `proyectos`.

## Por qué ocurre / por qué importa
El Prisma Client se genera a partir de `schema.prisma`, no de la base de datos. Si el campo no está en el schema, el cliente no lo conoce. Como consecuencia:

- `usuarioRepository.update(id, { nombre, edad, peso, altura, nivelActividad })` (`usuario.controller.js:23`) lanzará un error de validación de Prisma del tipo *"Unknown argument `edad`"*.
- `getPerfil` nunca devolverá `edad/peso/altura/nivelActividad` aunque existan en la base.

**Resultado:** toda la pantalla `ConfigurarPerfil` (guardar y precargar el perfil) está rota de punta a punta.

## Solución propuesta
Agregar los campos al modelo en `schema.prisma` para que coincidan con la migración:

```prisma
model Usuario {
  id             Int      @id @default(autoincrement())
  email          String   @unique
  password       String
  nombre         String
  rol            String   @default("USER")
  edad           Int?
  peso           Float?
  altura         Int?
  nivelActividad String?
  createdAt      DateTime @default(now())
  updatedAt      DateTime @updatedAt
  proyectos      Proyecto[]
}
```

Luego regenerar el cliente con `npx prisma generate` y verificar que el estado de migraciones quede consistente (`npx prisma migrate status`). Tener en cuenta que `peso` es `REAL` en SQLite → `Float?` en Prisma, y `edad`/`altura` son `INTEGER` → `Int?`.
</content>
