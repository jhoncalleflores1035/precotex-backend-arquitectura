# Precotex Proyecto Domain

Capa más interna de la arquitectura. **No depende de ninguna otra capa** (ni de Application, ni de Infrastructure, ni de API, ni de librerías externas como Dapper o EF).

```mermaid
flowchart LR
    APP[Application] --> DOM[Domain]
    INFRA[Infrastructure] -.no depende directo.-> DOM

    style DOM fill:#2d6a4f,stroke:#1b4332,color:#fff
```

> Regla simple para saber si algo va aquí: **si necesitas un `using` hacia Application, Infrastructure o un paquete de acceso a datos, esa clase no pertenece a Domain.**

## Estructura

```mermaid
flowchart TD
    ROOT[🗂️ Precotex.Proyecto.Domain]
    ROOT --> ENT[📁 Entities]
    ENT --> ENTF[📄 Cliente, Pedido, Producto]
    ROOT --> ENUM[📁 Enums]
    ENUM --> ENUMF[📄 EstadoPedido, TipoCliente]
    ROOT --> INT[📁 Interfaces]
    INT --> INTF[📄 IReglaDescuento, IValidable]

    style ROOT fill:#1b4332,stroke:#1b4332,color:#fff
    style ENT fill:#2d6a4f,stroke:#1b4332,color:#fff
    style ENTF fill:#74c69d,stroke:#1b4332,color:#000
    style ENUM fill:#ffb703,stroke:#1b4332,color:#000
    style ENUMF fill:#ffd166,stroke:#1b4332,color:#000
    style INT fill:#e76f51,stroke:#1b4332,color:#fff
    style INTF fill:#f4a261,stroke:#1b4332,color:#000
```

## Flujo: ejecución de un caso de uso

Domain es el último eslabón de la cadena API → Application → Domain. No conoce el `UseCase` que lo invoca, ni el Request/Response DTO, ni Dapper/EF: solo expone métodos de negocio y protege sus propias invariantes.

```mermaid
flowchart LR
    A["⚙️ UseCase (Application)"] -->|"invoca método de negocio"| B["🟩 Entidad (Domain)"]
    B --> C{"✅ Regla de negocio"}
    C -->|inválido| X["⚠️ Exception"]
    C -->|válido| D["🔄 Nuevo estado interno"]
    D --> B
    B -->|"Entidad actualizada"| A

    style A fill:#40916c,stroke:#1b4332,color:#fff
    style B fill:#2d6a4f,stroke:#1b4332,color:#fff
    style C fill:#ffb703,stroke:#1b4332,color:#000
    style X fill:#9d0208,stroke:#1b4332,color:#fff
    style D fill:#74c69d,stroke:#1b4332,color:#000
```

El `UseCase` le entrega a la entidad los datos ya validados en forma primitiva; la entidad decide si el cambio de estado es válido según sus propias reglas. Si no lo es, la excepción que lanza remonta hasta `Application` y de ahí hasta el `ExceptionHandlingMiddleware` de `Api`. Domain nunca valida el DTO de entrada ni sabe quién lo llamó: solo conoce sus propias reglas de negocio.

## Carpeta → contenido → SOLID

| Carpeta | Contiene | SOLID |
|---|---|---|
| `Entities/` | Objetos con identidad (`Id`) y reglas propias de negocio | SRP / OCP / LSP |
| `Enums/` | Catálogos cerrados de valores del negocio | — |
| `Interfaces/` | Contratos que pertenecen al negocio, no a infraestructura | ISP / DIP |

Por ahora **no** se usan Value Objects, Agregados, Excepciones de dominio ni Domain Events en este proyecto. Si en el futuro se necesitan, se documentan aquí antes de introducirlos.

Ver detalle general de la arquitectura en [docs/arquitectura.md](../../docs/arquitectura.md).
