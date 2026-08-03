# Precotex.Proyecto.Api

Punto de entrada de la solución. Depende de `Precotex.Proyecto.Application` → `Precotex.Proyecto.Domain`. Sin lógica de negocio: solo expone, valida y traduce.

## Estructura

```mermaid
flowchart TD
    ROOT[🗂️ Precotex.Proyecto.Api]
    ROOT --> CTRL[📁 Controllers]
    CTRL --> INV[📁 Inventario]
    CTRL --> VEN[📁 Ventas]
    ROOT --> EXT[📁 Extensions]
    EXT --> EXTF[⚙️ ServiceExtensions.cs]
    ROOT --> MID[📁 Middlewares]
    MID --> MIDF1[🛡️ ExceptionHandlingMiddleware.cs]
    MID --> MIDF2[🔗 CorrelationIdMiddleware.cs]
    ROOT --> FIL[📁 Filters]
    FIL --> FILF[✅ ValidationFilter.cs]
    ROOT --> CFG[📁 Configuration]
    CFG --> CFGF1[🔐 JwtOptions.cs]
    CFG --> CFGF2[📝 SwaggerOptions.cs]
    ROOT --> PRG[🚀 Program.cs]

    style ROOT fill:#1b4332,stroke:#1b4332,color:#fff
    style CTRL fill:#2d6a4f,stroke:#1b4332,color:#fff
    style INV fill:#40916c,stroke:#1b4332,color:#fff
    style VEN fill:#40916c,stroke:#1b4332,color:#fff
    style EXT fill:#74c69d,stroke:#1b4332,color:#000
    style EXTF fill:#95d5b2,stroke:#1b4332,color:#000
    style MID fill:#ffb703,stroke:#1b4332,color:#000
    style MIDF1 fill:#ffd166,stroke:#1b4332,color:#000
    style MIDF2 fill:#ffd166,stroke:#1b4332,color:#000
    style FIL fill:#219ebc,stroke:#1b4332,color:#fff
    style FILF fill:#8ecae6,stroke:#1b4332,color:#000
    style CFG fill:#e76f51,stroke:#1b4332,color:#fff
    style CFGF1 fill:#f4a261,stroke:#1b4332,color:#000
    style CFGF2 fill:#f4a261,stroke:#1b4332,color:#000
    style PRG fill:#9d0208,stroke:#1b4332,color:#fff
```

## Carpeta → contenido → SOLID

| Carpeta | Contiene | SOLID |
|---|---|---|
| `Controllers/{Modulo}/` | Controllers por módulo de negocio | SRP / DIP |
| `Extensions/ServiceExtensions.cs` | Todos los `AddXxx()` del proyecto | SRP |
| `Middlewares/` | Excepciones, correlación de requests | SRP |
| `Filters/` | Validación, auditoría por acción | SRP |
| `Configuration/` | POCOs para `IOptions<T>` | SRP |
| `Program.cs` | Composition root | — |

`Program.cs` queda así de simple:

```csharp
builder.Services.AddApplicationServices();
builder.Services.AddInfrastructureServices(builder.Configuration);
builder.Services.AddSwaggerConfig();
builder.Services.AddAuthConfig(builder.Configuration);
builder.Services.AddCorsConfig();

app.UseExceptionHandlingMiddleware();
app.MapControllers();
```

Ver arquitectura general en [docs/arquitectura.md](../../docs/arquitectura.md).
