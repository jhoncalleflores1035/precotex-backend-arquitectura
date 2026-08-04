# Precotex Proyecto Infraestructure.Tests

## Descripción

Contiene pruebas automatizadas de la capa `Precotex.Proyecto.Infrastructure`.
Son pruebas de integración enfocadas en validar el acceso real a datos mediante Dapper.
Se ejecutan contra una base de datos de prueba (LocalDB o contenedor), validando consultas SQL, mapeo de entidades y comportamiento de los repositorios.
No utiliza mocks de persistencia, ya que el objetivo es verificar la integración real entre código, SQL y base de datos.

## Estructura

```mermaid
flowchart TD
    ROOT[🗂️ Precotex.Proyecto.Infraestructure.Tests]
    ROOT --> REPO[📁 Repositories]
    REPO --> REPOINV[📁 Inventario]
    REPO --> REPOVEN[📁 Ventas]
    ROOT --> PERS[📁 Persistence]
    PERS --> PERSF[🔌 DapperContextTests.cs]
    ROOT --> FIX[📁 TestHelpers]
    FIX --> FIXF[🧱 Fixture de conexión, scripts de seed/reset]

    style ROOT fill:#1b4332,stroke:#1b4332,color:#fff
    style REPO fill:#2d6a4f,stroke:#1b4332,color:#fff
    style REPOINV fill:#74c69d,stroke:#1b4332,color:#000
    style REPOVEN fill:#74c69d,stroke:#1b4332,color:#000
    style PERS fill:#e76f51,stroke:#1b4332,color:#fff
    style PERSF fill:#f4a261,stroke:#1b4332,color:#000
    style FIX fill:#219ebc,stroke:#1b4332,color:#fff
    style FIXF fill:#8ecae6,stroke:#1b4332,color:#000
```

---

## Responsabilidades

| Carpeta | Responsabilidad | 
|---|---|
| `Repositories/{Modulo}/` | Tests de integración de repositorios contra BD real |
| `Persistence/` | Tests de conexión y configuración de Dapper |
| `TestHelpers/` | Fixtures, seed y limpieza de datos de prueba |

---

## Flujo

Cada test siembra el estado necesario en la base de datos de test, ejecuta el método del repositorio y verifica que la entidad de `Domain` devuelta sea correcta. Al finalizar (o al iniciar) el test se revierte el cambio — por transacción con rollback o por reseteo del esquema — para que los tests no dependan del orden de ejecución ni entre sí.

```mermaid
flowchart LR
    A["🧪 Test (Arrange)"] -->|"seed de datos"| B[("🛢️ Base de datos de test")]
    A -->|"Act"| C["🏗️ DapperPedidoRepository"]
    C --> B
    C -->|"Entidad (Domain)"| D["✅ Assert"]
    A -->|"reset/rollback"| B

    style A fill:#40916c,stroke:#1b4332,color:#fff
    style B fill:#219ebc,stroke:#1b4332,color:#fff
    style C fill:#2d6a4f,stroke:#1b4332,color:#fff
    style D fill:#74c69d,stroke:#1b4332,color:#000
```

Si dos implementaciones distintas de `IPedidoRepository` existieran (por ejemplo, Dapper y en memoria para tests rápidos), ambas deberían pasar exactamente el mismo conjunto de tests de contrato — es la prueba práctica de que son sustituibles entre sí.

---

## Dependencias

`Infraestructure.Tests` referencia `Infraestructure` (y, transitivamente, `Domain`), y se ejecuta contra una base de datos de test real.

```mermaid
flowchart LR
    IT[Infraestructure.Tests] --> INFRA[Infraestructure]
    INFRA --> DOM[Domain]
    IT -.contra.-> BD[("🛢️ Base de datos de test")]

    style INFRA fill:#74c69d,stroke:#1b4332,color:#000
    style DOM fill:#2d6a4f,stroke:#1b4332,color:#fff
    style IT fill:#95d5b2,stroke:#1b4332,color:#000
    style BD fill:#219ebc,stroke:#1b4332,color:#fff
```

---

## Qué NO pertenece aquí

| No colocar | Corresponde a |
|---|---|
| Pruebas de reglas de negocio | `Domain.Tests` |
| Pruebas de UseCases/Validators | `Application.Tests` |
| Pruebas de Controllers, Middlewares, Filters | `Api.Tests` |
| Mocks de Dapper o del acceso a datos | no aporta valor aquí — usar base de datos real de test |
| Lógica condicional (`if`, loops) dentro de un test | usar `[Theory]`/`[InlineData]` |

---

## Referencias

Ver la estrategia general de pruebas en [tests/README.md](../README.md).
Ver la capa que prueba en [src/Precotex.Proyecto.Infraestructure/README.md](../../src/Precotex.Proyecto.Infraestructure/README.md).
