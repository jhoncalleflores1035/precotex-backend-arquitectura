# Precotex Proyecto Api

## Descripción

Punto de entrada de la aplicación.
Su responsabilidad es recibir las solicitudes, validar la información de entrada, delegar los casos de uso y devolver la respuesta al cliente.
No contiene lógica de negocio.

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
    style FIL fill:#219ebc,stroke:#1b4332,color:#fff
    style FILF fill:#8ecae6,stroke:#1b4332,color:#000
    style CFG fill:#e76f51,stroke:#1b4332,color:#fff
    style CFGF1 fill:#f4a261,stroke:#1b4332,color:#000
    style CFGF2 fill:#f4a261,stroke:#1b4332,color:#000
    style PRG fill:#9d0208,stroke:#1b4332,color:#fff
```

---

## Responsabilidades

| Carpeta | Contiene | SOLID |
|---|---|---|
| `Controllers/{Modulo}/` | Controllers por módulo de negocio | SRP / DIP |
| `Extensions/ServiceExtensions.cs` | Todos los `AddXxx()` del proyecto | SRP |
| `Middlewares/` | Excepciones | SRP |
| `Filters/` | Validación, auditoría por acción | SRP |
| `Configuration/` | POCOs para `IOptions<T>` | SRP |
| `Program.cs` | Composition root | — |

---

## Composition Root

La configuración principal de la aplicación se concentra en `Program.cs`, delegando el registro de servicios a métodos de extensión.

```csharp
builder.Services.AddApplicationServices();
builder.Services.AddInfrastructureServices(builder.Configuration);
builder.Services.AddSwaggerConfig();
builder.Services.AddAuthConfig(builder.Configuration);
builder.Services.AddCorsConfig();

app.UseExceptionHandlingMiddleware();
app.MapControllers();
```

---

## Flujo

El request recorre la pipeline de este proyecto y cruza hacia el resto de capas mediante un DTO.

Dentro de `Api` no hay lógica de negocio: solo enruta, valida forma y traduce.

```mermaid
flowchart LR
    subgraph API[" 📦 Precotex.Proyecto.Api "]
        direction TB
        A["🌐 Request"] --> B["🛡️ Middleware"]
        B --> C["✅ Filters"]
        C --> D["🎮 Controller"]
    end

    subgraph OTRAS[" 🌍 Otras capas "]
        direction TB
        E["⚙️ Application"] --> F["🟩 Domain"]
        F --> G["📐 Repository (Interface)"]
        G --> H["🏗️ Infrastructure"]
    end

    D -->|"Request DTO"| E

    style API fill:#d8f3dc,stroke:#1b4332,color:#1b4332
    style OTRAS fill:#e9ecef,stroke:#6c757d,color:#495057

    style A fill:#74c69d,stroke:#1b4332,color:#000
    style B fill:#ffd166,stroke:#1b4332,color:#000
    style C fill:#8ecae6,stroke:#1b4332,color:#000
    style D fill:#2d6a4f,stroke:#1b4332,color:#fff
    style E fill:#40916c,stroke:#1b4332,color:#fff
    style F fill:#1b4332,stroke:#1b4332,color:#fff
    style G fill:#f4a261,stroke:#1b4332,color:#000
    style H fill:#e76f51,stroke:#1b4332,color:#fff
```

---

## Dependencias

La capa API puede depender de:

- Application
- Domain (opcional para contratos compartidos)

No debe depender de implementaciones de Infrastructure.

---

## Qué NO pertenece aquí

| No colocar | Corresponde a |
|---|---|
| Reglas de negocio | `Domain` |
| Orquestación de casos de uso | `Application` |
| Acceso a datos | `Infrastructure` |
| Interfaces de repositorio | `Domain` |
| Validaciones de dominio | `Domain` |
| Mapeo directo de entidades | `Application` |

---

## Referencias

Ver arquitectura general en [docs/arquitectura.md](../../docs/arquitectura.md).
