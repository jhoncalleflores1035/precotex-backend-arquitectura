# Precotex Proyecto Infraestructure

## Descripción

Capa encargada de implementar los detalles técnicos externos de la aplicación.
Depende de `Precotex.Proyecto.Domain` para implementar contratos de persistencia (`IRepository`) y encapsular el acceso a datos mediante Dapper.
Contiene implementaciones concretas de infraestructura, sin incluir reglas de negocio.

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

---

## Responsabilidades

| Carpeta | Responsabilidad |
|---|---|
| `Repositories/{Modulo}/` | Implementación concreta de contratos de persistencia definidos en Domain |
| `Persistence/` | Configuración de acceso a datos (`DapperContext`) |
| `ExternalServices/` | Integraciones con servicios externos (correo, storage, etc.) |

---

## Flujo

`Infrastructure Repository` implementa los contratos `IRepository` definidos por `Domain`.
Utiliza Dapper y los componentes de persistencia para acceder a datos, transformando los resultados obtenidos en entidades del dominio.
Los detalles técnicos de acceso a datos permanecen encapsulados dentro de esta capa.

```mermaid
flowchart LR
    A["🔌 IRepository (Domain)"] -. implementado por .- B["🏗️ Repository"]
    B --> C["🔌 DapperContext"]
    C --> D[("🛢️ Base de datos")]
    B --> E["🟩 Domain Entity"]

    style A fill:#e76f51,stroke:#1b4332,color:#fff
    style B fill:#2d6a4f,stroke:#1b4332,color:#fff
    style C fill:#f4a261,stroke:#1b4332,color:#000
    style D fill:#219ebc,stroke:#1b4332,color:#fff
    style E fill:#2d6a4f,stroke:#1b4332,color:#fff
```

---

## Dependencias

Infrastructure depende de:

- Domain

Implementa contratos de persistencia definidos por Domain.

No es referenciada directamente por API ni Application; sus implementaciones son registradas mediante inyección de dependencias.

---

## Qué NO pertenece aquí

| No colocar | Corresponde a |
|---|---|
| DTOs de API | `Application` |
| Contratos de negocio (IService) | `Application` |
| Reglas de negocio | `Domain` |
| Contratos de persistencia (IRepository) | `Domain` |

---

## Referencias

Ver arquitectura general en [docs/arquitectura.md](../../docs/arquitectura.md).
