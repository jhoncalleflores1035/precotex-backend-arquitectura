# Precotex.Proyecto.Domain

Capa más interna de la arquitectura. **No depende de ninguna otra capa** (ni de Application, ni de Infrastructure, ni de API, ni de librerías externas como Dapper o EF).

Ver detalle general de la arquitectura en [docs/arquitectura.md](../../docs/arquitectura.md).

```mermaid
flowchart LR
    APP[Application] --> DOM[Domain]
    INFRA[Infrastructure] -.no depende directo.-> DOM

    style DOM fill:#2d6a4f,stroke:#1b4332,color:#fff
```

> Regla simple para saber si algo va aquí: **si necesitas un `using` hacia Application, Infrastructure o un paquete de acceso a datos, esa clase no pertenece a Domain.**

## Contenido

| Carpeta | Qué va aquí | Ejemplo |
|---|---|---|
| `Entities/` | Objetos con identidad (`Id`) y reglas propias | `Cliente`, `Pedido`, `Producto` |
| `Enums/` | Catálogos cerrados de valores del negocio | `EstadoPedido`, `TipoCliente` |

Nada más por ahora: **no** se usan Value Objects, Agregados, Excepciones de dominio ni Domain Events en este proyecto. Si en el futuro se necesitan, se documentan aquí antes de introducirlos.

## Flujo: ciclo de vida de una entidad

La entidad es responsable de controlar sus propias transiciones de estado. Solo los caminos definidos por la propia clase son válidos; cualquier otro salto no existe en el modelo.

```mermaid
stateDiagram-v2
    [*] --> Pendiente : Crear()
    Pendiente --> Confirmado : Confirmar()
    Pendiente --> Cancelado : Cancelar()
    Confirmado --> [*]
    Cancelado --> [*]
```

Confirmado y Cancelado no tienen salida entre sí: la propia entidad impide, por diseño, pasar de `Confirmado` a `Cancelado` directamente. Esa restricción vive en el método de la entidad, no en quien la llama.

## Principios SOLID básicos, como flujos

### S — Single Responsibility

La entidad solo conoce sus reglas de negocio; guardar/consultar datos es responsabilidad de otra capa.

```mermaid
flowchart LR
    Pedido["Pedido (Domain)\nreglas de negocio"] -->|expone estado ya validado| Infra["Infrastructure\nguarda en BD"]
```

### O — Open/Closed

Una variante nueva del negocio se agrega como un camino adicional, sin tocar los caminos ya existentes.

```mermaid
flowchart TD
    Tipo{Tipo de cliente} -->|VIP| DVip["Descuento 10%"]
    Tipo -->|Regular| DReg["Descuento 0%"]
    Tipo -.->|nuevo tipo futuro| DNuevo["se agrega aparte,\nsin modificar los anteriores"]
```

### L — Liskov Substitution

Un tipo derivado debe poder reemplazar al tipo base en el mismo flujo, sin romperlo.

```mermaid
flowchart LR
    Caller[Quien usa la entidad] --> Cliente[Cliente]
    ClienteVip[ClienteVip] -.sustituye sin romper el flujo.-> Cliente
```

### I — Interface Segregation

Si el dominio define algún contrato, cada uno cubre un solo propósito en vez de uno solo con todo mezclado.

```mermaid
flowchart LR
    IValidable --> Entidad
    ICalculable --> Entidad
```

### D — Dependency Inversion

Ya representado en el primer diagrama: Domain no depende de nadie, y todas las demás capas dependen de él. Domain es el punto de partida de la inversión de dependencias.

## Checklist antes de un PR sobre Domain

- [ ] ¿La clase nueva solo depende de tipos del propio proyecto Domain (o de `System.*`)?
- [ ] ¿Las reglas de negocio están validadas dentro de la entidad, no en el controller/caso de uso?
- [ ] ¿El estado se modifica con métodos de negocio (`Confirmar()`, `Cancelar()`) y no con setters públicos sueltos?
- [ ] ¿Evité agregar Value Objects, Agregados, Excepciones de dominio o Events sin acordarlo antes?
