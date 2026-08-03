# Precotex.Proyecto.Api

Punto de entrada de la solución. Depende de `Precotex.Proyecto.Application` → `Precotex.Proyecto.Domain`. Sin lógica de negocio: solo expone, valida y traduce.

## Estructura

```
Precotex.Proyecto.Api/
├── Controllers/
│   ├── Inventario/
│   ├── Ventas/
│   ├── Compras/
│   └── Produccion/
├── Extensions/
│   └── ServiceExtensions.cs
├── Middlewares/
│   ├── ExceptionHandlingMiddleware.cs
│   └── CorrelationIdMiddleware.cs
├── Filters/
│   └── ValidationFilter.cs
├── Configuration/
│   ├── JwtOptions.cs
│   └── SwaggerOptions.cs
├── appsettings.json
└── Program.cs
```

## Carpeta → contenido → principio SOLID

| Carpeta | Contiene | Regla | SOLID |
|---|---|---|---|
| `Controllers/{Modulo}/` | Un controller por recurso, agrupado por módulo de negocio | Solo recibe request → llama servicio de `Application` → devuelve respuesta. Nunca lógica de negocio ni entidades de `Domain` expuestas | SRP / DIP |
| `Extensions/ServiceExtensions.cs` | Todos los `AddXxx()` del proyecto: `AddApplicationServices`, `AddInfrastructureServices`, `AddSwaggerConfig`, `AddAuthConfig`, `AddCorsConfig` | Un método estático por bloque de configuración, todos en el mismo archivo (proyecto modular, sin submódulos) | SRP |
| `Middlewares/` | Manejo global de excepciones, correlación de requests | Un middleware = una responsabilidad | SRP |
| `Filters/` | Validación de `ModelState`, filtros de auditoría | Cross-cutting a nivel de acción, no de todo el pipeline | SRP |
| `Configuration/` | POCOs para `IOptions<T>` (`JwtOptions`, `SwaggerOptions`, ...) | Un archivo por sección de `appsettings.json`, binding centralizado | SRP |
| `Program.cs` | Composition root | Solo invoca los métodos de `Extensions/`, cero configuración inline | — |

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
