# Precotex Proyecto Application.Tests

Prueba `Precotex.Proyecto.Application`. Referencia `Application` (y transitivamente `Domain`). Usa Moq para los contratos que el UseCase consume por inyección: `IRepository` (definido en Domain) e interfaces de orquestación (definidas en Application).

```mermaid
flowchart LR
    AT[Application.Tests] --> APP[Application]
    APP --> DOM[Domain]

    style APP fill:#40916c,stroke:#1b4332,color:#fff
    style DOM fill:#2d6a4f,stroke:#1b4332,color:#fff
    style AT fill:#95d5b2,stroke:#1b4332,color:#000
```

## Estructura

```mermaid
flowchart TD
    ROOT[🗂️ Precotex.Proyecto.Application.Tests]
    ROOT --> UC[📁 UseCases]
    UC --> UCINV[📁 Inventario]
    UC --> UCVEN[📁 Ventas]
    ROOT --> VAL[📁 Validators]
    VAL --> VALF[✅ Tests de FluentValidation]
    ROOT --> TH[📁 TestHelpers]
    TH --> THF[🧱 Mocks/Builders compartidos de DTOs y entidades]

    style ROOT fill:#1b4332,stroke:#1b4332,color:#fff
    style UC fill:#2d6a4f,stroke:#1b4332,color:#fff
    style UCINV fill:#74c69d,stroke:#1b4332,color:#000
    style UCVEN fill:#74c69d,stroke:#1b4332,color:#000
    style VAL fill:#ffb703,stroke:#1b4332,color:#000
    style VALF fill:#ffd166,stroke:#1b4332,color:#000
    style TH fill:#219ebc,stroke:#1b4332,color:#fff
    style THF fill:#8ecae6,stroke:#1b4332,color:#000
```

## Flujo: prueba de un caso de uso

```mermaid
flowchart LR
    A["🧪 Test (Arrange)"] -->|"Mock&lt;IRepository&gt;, Mock&lt;IServicioExterno&gt;"| B["⚙️ UseCase"]
    A -->|"Act: ejecutar con Request DTO"| B
    B -->|"Response DTO"| C["✅ Assert sobre el resultado"]
    B -->|"llamadas a los mocks"| D["✅ Verify (Moq)"]

    style A fill:#40916c,stroke:#1b4332,color:#fff
    style B fill:#2d6a4f,stroke:#1b4332,color:#fff
    style C fill:#74c69d,stroke:#1b4332,color:#000
    style D fill:#74c69d,stroke:#1b4332,color:#000
```

El test construye los mocks de las interfaces que el UseCase recibe por constructor (`Mock<IPedidoRepository>`, etc.), configura el comportamiento esperado (`Setup`), ejecuta el caso de uso con un Request DTO y verifica dos cosas: el Response DTO devuelto (`Assert`), y opcionalmente que el UseCase llamó a las dependencias correctas (`Verify`). Nunca instancia una clase concreta de Infraestructure — si tuviera que hacerlo, el UseCase estaría acoplado a una implementación concreta.

`Validators/` se prueba sin mocks: cada test alimenta el validator con un DTO válido/inválido y verifica el resultado (`IsValid`, lista de errores).

## Carpeta → contenido → SOLID

| Carpeta | Contiene | SOLID que valida |
|---|---|---|
| `UseCases/{Modulo}/` | Un archivo de test por UseCase, con mocks de `IRepository` (Domain) e interfaces de orquestación (Application) | DIP — si un UseCase no se puede mockear sin referenciar Infraestructure, la dependencia está invertida |
| `Validators/` | Un archivo de test por Validator, casos válidos e inválidos | SRP — cada Validator prueba un único DTO |
| `TestHelpers/` | Builders de DTOs/entidades y helpers de configuración de mocks reutilizables entre UseCases | SRP (de los propios tests) |

Ver la estrategia general de pruebas en [tests/README.md](../README.md).
