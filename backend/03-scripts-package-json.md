# 03 — Scripts de `package.json` y setup de build

**Categoría:** Backend
**Severidad:** 🟡 Menor
**Archivos afectados:**
- `backend/package.json`
- `backend/tsconfig.json`

## Problema
Los scripts del backend son:

```json
"start": "node dist/server.js",
"dev":   "node dist/server.js",
"build": "tsc"
```

- `dev` y `start` apuntan ambos a `dist/server.js`, así que hay que correr `build` antes y **no existe un modo desarrollo con recarga** (nodemon/watch).
- Todo el código fuente es `.js` con módulos ESM (`import`/`export`), pero se "compila" con `tsc` a CommonJS (`tsconfig.json`: `module: "commonjs"`, `allowJs: true`). Es una configuración inusual y frágil para un proyecto que en realidad es JavaScript puro.

## Por qué ocurre / por qué importa
No rompe la ejecución una vez compilado, pero:
- El ciclo de desarrollo es incómodo (build manual en cada cambio).
- Mezclar ESM en el fuente con salida CommonJS puede dar sorpresas de resolución de módulos.

## Solución propuesta
Elegir un enfoque y ser consistente. Opción simple (correr el fuente directamente como ESM):

```json
{
  "type": "module",
  "scripts": {
    "dev": "node --watch src/server.js",
    "start": "node src/server.js"
  }
}
```

Y eliminar `tsc`/`tsconfig.json` si no se usa TypeScript. Si se quiere conservar TypeScript, migrar los archivos a `.ts` de verdad y agregar un `dev` con `tsx`/`ts-node-dev`.
</content>
