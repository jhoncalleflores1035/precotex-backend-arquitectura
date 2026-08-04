# Requerimientos e Incidencias

Registro de los requerimientos e incidencias del sistema. Cada entrada aquí debe estar vinculada a una rama del flujo descrito en [`flujo-git.md`](./flujo-git.md), de modo que se pueda rastrear qué requerimiento o incidencia originó cada rama, PR y tag.

## Tipos

| Tipo | Prefijo ID | Rama asociada | Referencia |
|---|---|---|---|
| Requerimiento | `REQ-XXX` | `feature/<nombre>` | Nueva funcionalidad o mejora |
| Incidencia | `INC-XXX` | `fix/<nombre>` (detectada en dev/QA) o `hotfix/<nombre>` (detectada en producción) | Bug o comportamiento no esperado |

La numeración es correlativa por tipo y nunca se reutiliza, aunque la incidencia o requerimiento se descarte (`REQ-001`, `REQ-002`, ... / `INC-001`, `INC-002`, ...).

## Formato — Requerimiento

```markdown
## REQ-XXX — <Título corto>

**Fecha de alta:** AAAA-MM-DD
**Estado:** Pendiente | En desarrollo | En QA | Completado | Descartado
**Solicitado por:** <nombre/área>
**Rama:** feature/<nombre>

**Descripción:** qué se solicita y por qué (contexto de negocio).

**Criterios de aceptación:**
- [ ] Criterio 1
- [ ] Criterio 2

**Notas:** aclaraciones, dependencias con otros REQ/INC, decisiones vinculadas en `decisiones-arquitectura.md` si aplica.
```

## Formato — Incidencia

```markdown
## INC-XXX — <Título corto>

**Fecha de alta:** AAAA-MM-DD
**Estado:** Abierta | En corrección | En QA | Resuelta | No es bug
**Severidad:** Crítica | Alta | Media | Baja
**Detectada en:** Desarrollo | QA | Producción
**Rama:** fix/<nombre> | hotfix/<nombre>

**Descripción:** comportamiento actual vs. esperado, pasos para reproducir.

**Causa raíz:** (se completa al resolver).

**Solución aplicada:** (se completa al resolver).

**Notas:** versión/tag donde se detectó (si aplica), REQ relacionado si la incidencia surge de un requerimiento reciente.
```

## Relación con el flujo de Git

1. Se da de alta el REQ o INC en este documento, con estado inicial (`Pendiente` / `Abierta`).
2. Se crea la rama correspondiente desde `dev` (o desde `main` si es `hotfix`), según la tabla de prefijos de `flujo-git.md`.
3. El nombre de la rama debe reflejar el mismo tema que el título del REQ/INC (ej. `REQ-014 — Registrar kardex de inventario` → `feature/inventario-kardex`).
4. Los commits y el Pull Request deben referenciar el ID (ej. `feat: agrega endpoint de kardex (REQ-014)`).
5. Al fusionar el PR, se actualiza el estado en este documento (`En QA`).
6. Cuando `dev` supera QA y se promueve a `main` con su tag, se marca el REQ/INC como `Completado` / `Resuelta`, indicando el tag de versión donde quedó incluido.

### Ejemplo

```
REQ-014 — Registrar kardex de inventario
  Estado: Pendiente → feature/inventario-kardex → PR a dev → En QA → Completado (v1.1.0)

INC-009 — Stock queda en negativo tras venta parcial
  Detectada en: QA
  Estado: Abierta → fix/validacion-stock-negativo → PR a dev → Resuelta (v1.1.0)

INC-012 — Falla conexión a base de datos en producción
  Detectada en: Producción
  Severidad: Crítica
  Estado: Abierta → hotfix/error-conexion-bd → PR a main → Resuelta (v1.1.1)
```
