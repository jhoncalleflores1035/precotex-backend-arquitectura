# Decisiones de arquitectura

Registro de decisiones técnicas relevantes que no sean obvias desde el código ni estén cubiertas en el README.

## Formato

```markdown
## <Fecha> — <Título de la decisión>

**Contexto:** qué problema o disyuntiva motivó la decisión.

**Decisión:** qué se decidió.

**Consecuencias:** qué implica a futuro (trade-offs aceptados).
```

## 2026-08-03 — Los contratos de repositorio (`IRepository`) se definen en Domain, no en Application

**Contexto:** El diseño inicial ubicaba todos los contratos que implementa Infrastructure (repositorios y servicios externos) en `Application/Interfaces`. Los repositorios, sin embargo, describen operaciones sobre las propias entidades de `Domain`, por lo que dejarlos en Application no reflejaba quién es el dueño real del contrato.

**Decisión:** Los contratos de persistencia (`IPedidoRepository`, `IProductoRepository`, etc.) se mueven a `Domain/Interfaces/Repositories/{Modulo}`. `Infrastructure` los implementa con una dependencia directa a `Domain`. `Application/Interfaces/{Modulo}` queda reservado solo para contratos propios de orquestación (servicios externos, notificaciones) que no son repositorios.

**Consecuencias:** `Infrastructure` ahora depende directamente de `Domain` además de `Application` (antes era solo transitiva). Los casos de uso (`UseCase`) no cambian: siguen consumiendo la interfaz inyectada sin conocer su implementación concreta, sin importar en qué capa se declaró el contrato.
