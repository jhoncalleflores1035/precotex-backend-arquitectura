# Precotex Proyecto Domain.Tests

Prueba `Precotex.Proyecto.Domain`. No referencia ninguna otra capa: las entidades no tienen dependencias externas, así que sus tests tampoco las necesitan.

```mermaid
flowchart LR
    DT[Domain.Tests] --> DOM[Domain]

    style DOM fill:#2d6a4f,stroke:#1b4332,color:#fff
    style DT fill:#95d5b2,stroke:#1b4332,color:#000
```

## Estructura

```mermaid
flowchart TD
    ROOT[🗂️ Precotex.Proyecto.Domain.Tests]
    ROOT --> ENT[📁 Entities]
    ENT --> ENTF[📄 ClienteTests, PedidoTests, ProductoTests]
    ROOT --> INT[📁 Interfaces]
    INT --> INTF[📄 Fakes de IReglaDescuento, IValidable]
    ROOT --> TH[📁 TestHelpers]
    TH --> THF[🧱 Builders/ObjectMother de entidades]

    style ROOT fill:#1b4332,stroke:#1b4332,color:#fff
    style ENT fill:#2d6a4f,stroke:#1b4332,color:#fff
    style ENTF fill:#74c69d,stroke:#1b4332,color:#000
    style INT fill:#e76f51,stroke:#1b4332,color:#fff
    style INTF fill:#f4a261,stroke:#1b4332,color:#000
    style TH fill:#219ebc,stroke:#1b4332,color:#fff
    style THF fill:#8ecae6,stroke:#1b4332,color:#000
```

## Flujo: prueba de una entidad

```mermaid
flowchart LR
    A["🧪 Test (Arrange)"] -->|"builder/constructor"| B["🟩 Entidad"]
    A -->|"Act"| C["método de negocio"]
    C --> B
    B -->|"nuevo estado / excepción"| D["✅ Assert"]

    style A fill:#40916c,stroke:#1b4332,color:#fff
    style B fill:#2d6a4f,stroke:#1b4332,color:#fff
    style C fill:#ffb703,stroke:#1b4332,color:#000
    style D fill:#74c69d,stroke:#1b4332,color:#000
```

Cada test instancia la entidad (directamente o vía un builder de `TestHelpers/`), invoca un método de negocio y verifica el nuevo estado o la excepción lanzada. No hay mocks: si una entidad los necesitara, dejaría de ser Domain puro.

Los `Interfaces/` de Domain (`IReglaDescuento`, `IValidable`) se prueban con **fakes** escritos a mano (clases pequeñas que implementan la interfaz con comportamiento fijo para el test), no con Moq — no hay nada que "verificar que fue llamado", solo comportamiento a probar.

## Carpeta → contenido → SOLID

| Carpeta | Contiene | SOLID que valida |
|---|---|---|
| `Entities/` | Un archivo de test por entidad, con un test por regla de negocio/invariante | SRP — si el test de una entidad crece demasiado, la entidad probablemente tiene más de una responsabilidad |
| `Interfaces/` | Fakes de las interfaces de reglas de negocio puras | ISP / DIP — una interfaz fácil de fakear es una interfaz pequeña y bien enfocada |
| `TestHelpers/` | Builders/Object Mother para construir entidades válidas sin repetir setup en cada test | SRP (de los propios tests) |

Ver la estrategia general de pruebas en [tests/README.md](../README.md).
