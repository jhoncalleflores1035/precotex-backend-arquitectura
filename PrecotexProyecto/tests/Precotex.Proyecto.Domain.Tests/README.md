# Precotex Proyecto Domain.Tests

## Descripción

Contiene pruebas automatizadas de la capa `Precotex.Proyecto.Domain`.
Valida reglas de negocio, invariantes y comportamiento interno de las entidades sin dependencias externas.
No referencia ninguna otra capa de la solución: al ser un dominio puro, sus pruebas tampoco requieren infraestructura, persistencia ni servicios externos.

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

---

## Responsabilidades

| Carpeta | Responsabilidad |
|---|---|
| `Entities/` | Tests de reglas de negocio e invariantes de entidades |
| `Fakes/` | Implementaciones simples de interfaces propias del dominio |
| `TestHelpers/` | Builders/Object Mother para crear escenarios válidos |

---

## Flujo

Cada test instancia la entidad (directamente o vía un builder de `TestHelpers/`), invoca un método de negocio y verifica el nuevo estado o la excepción lanzada. No hay mocks: si una entidad los necesitara, dejaría de ser Domain puro.

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

Los `Interfaces/` de Domain (`IReglaDescuento`, `IValidable`) se prueban con **fakes** escritos a mano (clases pequeñas que implementan la interfaz con comportamiento fijo para el test), no con Moq — no hay nada que "verificar que fue llamado", solo comportamiento a probar.

---

## Dependencias

`Domain.Tests` no referencia ninguna otra capa ni proyecto de test: es el único proyecto de pruebas sin dependencias externas al código que prueba.

```mermaid
flowchart LR
    DT[Domain.Tests] --> DOM[Domain]

    style DOM fill:#2d6a4f,stroke:#1b4332,color:#fff
    style DT fill:#95d5b2,stroke:#1b4332,color:#000
```

---

## Qué NO pertenece aquí

| No colocar | Corresponde a |
|---|---|
| Mocks de `IRepository` u otras dependencias externas | `Application.Tests` (si una entidad los necesita, rompe la regla de Domain) |
| Pruebas de UseCases, Validators | `Application.Tests` |
| Pruebas de acceso a datos real | `Infraestructure.Tests` |
| Pruebas de Controllers, Middlewares, Filters | `Api.Tests` |
| Lógica condicional (`if`, loops) dentro de un test | usar `[Theory]`/`[InlineData]` |

---

## Referencias

Ver la estrategia general de pruebas en [tests/README.md](../README.md).
Ver la capa que prueba en [src/Precotex.Proyecto.Domain/README.md](../../src/Precotex.Proyecto.Domain/README.md).
