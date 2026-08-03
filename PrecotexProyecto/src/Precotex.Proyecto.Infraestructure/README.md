# Precotex Proyecto Infraestructure

Depende de `Precotex.Proyecto.Domain` (implementa sus contratos de persistencia, `IRepository`) y de `Precotex.Proyecto.Application` (implementa sus contratos de orquestación). Implementa lo que Domain y Application definen como interfaz: **cómo** se resuelve el acceso a datos y los servicios externos, nunca **qué** se necesita.

## Estructura

```mermaid
flowchart TD
    ROOT[🗂️ Precotex.Proyecto.Infraestructure]
    ROOT --> REPO[📁 Repositories]
    REPO --> REPOINV[📁 Inventario]
    REPO --> REPOVEN[📁 Ventas]
    ROOT --> PERS[📁 Persistence]
    PERS --> PERSF[🔌 DapperContext.cs]
    ROOT --> EXT[📁 ExternalServices]
    EXT --> EXTF[🌐 Integraciones externas]

    style ROOT fill:#1b4332,stroke:#1b4332,color:#fff
    style REPO fill:#2d6a4f,stroke:#1b4332,color:#fff
    style REPOINV fill:#74c69d,stroke:#1b4332,color:#000
    style REPOVEN fill:#74c69d,stroke:#1b4332,color:#000
    style PERS fill:#e76f51,stroke:#1b4332,color:#fff
    style PERSF fill:#f4a261,stroke:#1b4332,color:#000
    style EXT fill:#219ebc,stroke:#1b4332,color:#fff
    style EXTF fill:#8ecae6,stroke:#1b4332,color:#000
```

## Flujo: implementación de un repositorio con Dapper

```mermaid
flowchart LR
    A["🔌 IRepository (Domain)"] -.implementa.-> B["🏗️ DapperRepository"]
    B --> C["🔌 DapperContext"]
    C --> D[("🛢️ Base de datos")]
    B -->|"mapea filas a"| E["🟩 Entidad (Domain)"]

    style A fill:#e76f51,stroke:#1b4332,color:#fff
    style B fill:#2d6a4f,stroke:#1b4332,color:#fff
    style C fill:#f4a261,stroke:#1b4332,color:#000
    style D fill:#219ebc,stroke:#1b4332,color:#fff
    style E fill:#2d6a4f,stroke:#1b4332,color:#fff
```

`DapperRepository` solo conoce el `IRepository` que implementa y la entidad de `Domain` que devuelve. Nunca expone tipos propios de Dapper (`IDbConnection`, `DynamicParameters`, etc.) fuera de esta capa.

## Carpeta → contenido → SOLID

| Carpeta | Contiene | SOLID |
|---|---|---|
| `Repositories/{Modulo}/` | Implementación concreta (Dapper) de las interfaces definidas en Domain, organizadas por módulo de negocio | DIP / LSP |
| `Persistence/` | Conexión y contexto de acceso a datos (`DapperContext`) | SRP |
| `ExternalServices/` | Integraciones con servicios externos (correo, storage, etc.) | SRP / ISP |

Ver detalle general de la arquitectura en [docs/arquitectura.md](../../docs/arquitectura.md).
