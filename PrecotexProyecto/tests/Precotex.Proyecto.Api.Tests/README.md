# Precotex Proyecto Api.Tests

## Descripción

Contiene pruebas automatizadas de la capa `Api`. 
Principalmente pruebas unitarias de Controllers, Middlewares y Filters con dependencias mockeadas, además de un conjunto reducido de pruebas de integración end-to-end con `WebApplicationFactory<Program>`.

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

---

## Responsabilidades

| Carpeta | Responsabilidad |
|---|---|
| `Controllers/{Modulo}/` | Tests unitarios de Controllers con UseCases mockeados | 
| `Middlewares/` | Tests aislados del pipeline HTTP | 
| `Filters/` | Tests de validaciones transversales | 
| `Integration/` | Flujos completos con pipeline real | 

---

## Flujo

El test de Controller mockea el UseCase que la acción invoca, llama al método del controller directamente (sin levantar el pipeline HTTP) y verifica el `IActionResult` (código de estado, tipo, contenido). No prepara reglas de negocio: si tuviera que hacerlo, el Controller estaría conteniendo lógica que le corresponde a `Application`.

```mermaid
flowchart LR
    A["🧪 Test (Arrange)"] -->|"Mock&lt;IUseCase&gt;"| B["🎮 Controller"]
    A -->|"Act: llamar la acción"| B
    B -->|"IActionResult"| C["✅ Assert sobre status code y body"]

    style A fill:#40916c,stroke:#1b4332,color:#fff
    style B fill:#2d6a4f,stroke:#1b4332,color:#fff
    style C fill:#74c69d,stroke:#1b4332,color:#000
```

`Middlewares/` y `Filters/` se prueban de forma aislada, sin Controller real: se invoca el middleware/filter directamente con un `HttpContext` simulado y se verifica su efecto (código de estado, cuerpo de la respuesta, si el pipeline continúa o corta).

`Integration/` es el único lugar de esta carpeta con pruebas de extremo a extremo (`WebApplicationFactory<Program>`, pipeline HTTP real, base de datos de test): deliberadamente pocas, para casos críticos de negocio de punta a punta.

---

## Dependencias

`Api.Tests` referencia únicamente `Api` (y, transitivamente, `Application` y `Domain`). Nunca referencia `Infraestructure.Tests` ni ningún otro proyecto de test.

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

---

## Qué NO pertenece aquí

| No colocar | Corresponde a |
|---|---|
| Pruebas de reglas de negocio | `Domain.Tests` |
| Pruebas de UseCases/Validators | `Application.Tests` |
| Pruebas de repositorios/acceso a datos real | `Infraestructure.Tests` |
| Datos de negocio complejos para levantar un Controller | señal de que el Controller tiene lógica que corresponde a `Application` |

---

## Referencias

Ver la estrategia general de pruebas en [tests/README.md](../README.md).
Ver la capa que prueba en [src/Precotex.Proyecto.Api/README.md](../../src/Precotex.Proyecto.Api/README.md).
