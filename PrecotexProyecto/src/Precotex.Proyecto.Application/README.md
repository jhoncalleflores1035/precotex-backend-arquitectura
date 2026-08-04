# Precotex Proyecto Application

## Descripción

Capa encargada de orquestar los casos de uso del sistema.
Define contratos de servicios consumidos por la API, gestiona DTOs de entrada/salida y coordina operaciones con Domain mediante sus contratos.
No contiene lógica de infraestructura ni reglas propias del dominio.

## Estructura

```mermaid
flowchart TD
    ROOT[🗂️ Precotex.Proyecto.Application]
    ROOT --> INT[📁 Interfaces]
    INT --> INTINV[📁 Inventario]
    INT --> INTVEN[📁 Ventas]
    ROOT --> DTO[📁 DTOs]
    DTO --> DTOREQ[📄 Request]
    DTO --> DTORES[📄 Response]
    ROOT --> VAL[📁 Validators]
    VAL --> VALF[✅ FluentValidation]

    style ROOT fill:#1b4332,stroke:#1b4332,color:#fff
    style INT fill:#e76f51,stroke:#1b4332,color:#fff
    style INTINV fill:#f4a261,stroke:#1b4332,color:#000
    style INTVEN fill:#f4a261,stroke:#1b4332,color:#000
    style DTO fill:#219ebc,stroke:#1b4332,color:#fff
    style DTOREQ fill:#8ecae6,stroke:#1b4332,color:#000
    style DTORES fill:#8ecae6,stroke:#1b4332,color:#000
    style VAL fill:#ffb703,stroke:#1b4332,color:#000
    style VALF fill:#ffd166,stroke:#1b4332,color:#000
```

---

## Responsabilidades

| Carpeta | Responsabilidad |
|---|---|
| `Interfaces/{Modulo}/` | Contratos de servicios de aplicación que exponen los casos de uso consumidos por la API. |
| `DTOs/` | Modelos de entrada y salida entre API y Application. |
| `Validators/` | Validación de datos de entrada mediante FluentValidation. |

---

## Flujo

La API invoca al `Application Service` (el `UseCase`). Este valida el request con el `Validator`, coordina operaciones con `Domain` y utiliza contratos definidos por este, cuya implementación corresponde a `Infrastructure`. y arma el `Response DTO` que retorna a la API.

```mermaid
flowchart LR
    A["🌐 API"] --> B["⚙️ Application Service"]
    B --> V["✅ Validator"]
    B --> D["🟩 Domain"]
    D --> I["🏗️ Infrastructure"]
    B --> R["📦 Response DTO"]
    R --> A

    style A fill:#74c69d,stroke:#1b4332,color:#000
    style B fill:#2d6a4f,stroke:#1b4332,color:#fff
    style V fill:#ffb703,stroke:#1b4332,color:#000
    style D fill:#1b4332,stroke:#1b4332,color:#fff
    style I fill:#e76f51,stroke:#1b4332,color:#fff
    style R fill:#8ecae6,stroke:#1b4332,color:#000
```

---

## Casos de Uso

Los servicios de Application representan las acciones disponibles para la API.

Cada servicio:
- recibe información mediante DTOs
- valida la entrada
- coordina operaciones con Domain
- devuelve resultados al consumidor

---

## Dependencias

La capa Application puede depender de:

- Domain

No conoce:

- API
- Infrastructure
- Persistencia
- Detalles externos

---

## Qué NO pertenece aquí

| No colocar | Corresponde a |
|---|---|
| Reglas e invariantes de negocio | `Domain` |
| Dapper, SQL, `HttpClient` concreto | `Infrastructure` |
| `HttpContext`, `IActionResult` | `Api` |
| Definición de `IRepository` | `Domain` |
| Código HTTP, formato de respuesta | `Api` |

---

## Referencias

Ver arquitectura general en [docs/arquitectura.md](../../docs/arquitectura.md).
