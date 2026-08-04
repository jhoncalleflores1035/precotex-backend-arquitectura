# Precotex Proyecto Infraestructure.Tests

Prueba `Precotex.Proyecto.Infraestructure`. Son pruebas de integración: se ejecutan contra una base de datos real de test (LocalDB o contenedor), nunca contra mocks — mockear Dapper no probaría nada sobre el SQL real.

```mermaid
flowchart LR
    IT[Infraestructure.Tests] --> INFRA[Infraestructure]
    INFRA --> DOM[Domain]
    INFRA --> APP[Application]
    IT -.contra.-> BD[("🛢️ Base de datos de test")]

    style INFRA fill:#74c69d,stroke:#1b4332,color:#000
    style DOM fill:#2d6a4f,stroke:#1b4332,color:#fff
    style APP fill:#40916c,stroke:#1b4332,color:#fff
    style IT fill:#95d5b2,stroke:#1b4332,color:#000
    style BD fill:#219ebc,stroke:#1b4332,color:#fff
```

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

## Flujo: prueba de un repositorio Dapper

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

Cada test siembra el estado necesario en la base de datos de test, ejecuta el método del repositorio y verifica que la entidad de `Domain` devuelta sea correcta. Al finalizar (o al iniciar) el test se revierte el cambio — por transacción con rollback o por reseteo del esquema — para que los tests no dependan del orden de ejecución ni entre sí.

Si dos implementaciones distintas de `IPedidoRepository` existieran (por ejemplo, Dapper y en memoria para tests rápidos), ambas deberían pasar exactamente el mismo conjunto de tests de contrato — es la prueba práctica de que son sustituibles entre sí.

## Carpeta → contenido → SOLID

| Carpeta | Contiene | SOLID que valida |
|---|---|---|
| `Repositories/{Modulo}/` | Tests de integración por repositorio, contra base de datos real | LSP — cualquier implementación de la interfaz debe pasar el mismo contrato de test |
| `Persistence/` | Tests de `DapperContext`: apertura/cierre de conexión, manejo de errores de conexión | SRP |
| `TestHelpers/` | Fixture de conexión a la BD de test, scripts de seed y limpieza compartidos | SRP (de los propios tests) |

Ver la estrategia general de pruebas en [tests/README.md](../README.md).
