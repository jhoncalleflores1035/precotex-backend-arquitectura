# Precotex Proyecto Api.Tests

Prueba `Precotex.Proyecto.Api`. La mayoría son pruebas unitarias de Controllers/Middlewares/Filters con los UseCases mockeados; opcionalmente, un subconjunto pequeño de pruebas de integración end-to-end con `WebApplicationFactory<Program>`.

```mermaid
flowchart LR
    APIT[Api.Tests] --> API[Api]
    API --> APP[Application]
    APP --> DOM[Domain]

    style API fill:#74c69d,stroke:#1b4332,color:#000
    style APP fill:#40916c,stroke:#1b4332,color:#fff
    style DOM fill:#2d6a4f,stroke:#1b4332,color:#fff
    style APIT fill:#95d5b2,stroke:#1b4332,color:#000
```

## Estructura

```mermaid
flowchart TD
    ROOT[🗂️ Precotex.Proyecto.Api.Tests]
    ROOT --> CTRL[📁 Controllers]
    CTRL --> INV[📁 Inventario]
    CTRL --> VEN[📁 Ventas]
    ROOT --> MID[📁 Middlewares]
    MID --> MIDF[🛡️ ExceptionHandlingMiddlewareTests.cs]
    ROOT --> FIL[📁 Filters]
    FIL --> FILF[✅ ValidationFilterTests.cs]
    ROOT --> INTEG[📁 Integration]
    INTEG --> INTEGF[🌐 Pruebas end-to-end con WebApplicationFactory]

    style ROOT fill:#1b4332,stroke:#1b4332,color:#fff
    style CTRL fill:#2d6a4f,stroke:#1b4332,color:#fff
    style INV fill:#40916c,stroke:#1b4332,color:#fff
    style VEN fill:#40916c,stroke:#1b4332,color:#fff
    style MID fill:#ffb703,stroke:#1b4332,color:#000
    style MIDF fill:#ffd166,stroke:#1b4332,color:#000
    style FIL fill:#219ebc,stroke:#1b4332,color:#fff
    style FILF fill:#8ecae6,stroke:#1b4332,color:#000
    style INTEG fill:#9d0208,stroke:#1b4332,color:#fff
    style INTEGF fill:#e76f51,stroke:#1b4332,color:#fff
```

## Flujo: prueba de un controller

```mermaid
flowchart LR
    A["🧪 Test (Arrange)"] -->|"Mock&lt;IUseCase&gt;"| B["🎮 Controller"]
    A -->|"Act: llamar la acción"| B
    B -->|"IActionResult"| C["✅ Assert sobre status code y body"]

    style A fill:#40916c,stroke:#1b4332,color:#fff
    style B fill:#2d6a4f,stroke:#1b4332,color:#fff
    style C fill:#74c69d,stroke:#1b4332,color:#000
```

El test de Controller mockea el UseCase que la acción invoca, llama al método del controller directamente (sin levantar el pipeline HTTP) y verifica el `IActionResult` (código de estado, tipo, contenido). No prepara reglas de negocio: si tuviera que hacerlo, el Controller estaría conteniendo lógica que le corresponde a `Application`.

`Middlewares/` y `Filters/` se prueban de forma aislada, sin Controller real: se invoca el middleware/filter directamente con un `HttpContext` simulado y se verifica su efecto (código de estado, cuerpo de la respuesta, si el pipeline continúa o corta).

`Integration/` es el único lugar de esta carpeta con pruebas de extremo a extremo (`WebApplicationFactory<Program>`, pipeline HTTP real, base de datos de test): deliberadamente pocas, para casos críticos de negocio de punta a punta.

## Carpeta → contenido → SOLID

| Carpeta | Contiene | SOLID que valida |
|---|---|---|
| `Controllers/{Modulo}/` | Un archivo de test por controller, con el/los UseCase mockeados | SRP / DIP — un controller sin lógica de negocio es fácil (y rápido) de testear con un solo mock |
| `Middlewares/` | Tests aislados del pipeline de manejo de excepciones | SRP |
| `Filters/` | Tests aislados de filtros de validación/auditoría | SRP |
| `Integration/` | Pruebas end-to-end acotadas a flujos críticos | — |

Ver la estrategia general de pruebas en [tests/README.md](../README.md).
