# Precotex Proyecto

## Objetivo

El objetivo principal de este proyecto es el desarrollo de una solución de software robusta, mantenible y escalable, construida bajo los principios de **Clean Architecture**. Precotex nace como una base técnica sólida que separa claramente las responsabilidades entre capas (Domain, Application, Infrastructure y API), facilitando la evolución del sistema, la incorporación de nuevos módulos de negocio y la aplicación de buenas prácticas de desarrollo como la inversión de dependencias, la validación centralizada y el acceso a datos desacoplado mediante Dapper.

## Arquitectura

El proyecto sigue el enfoque de **Clean Architecture**, organizado en capas (API, Application, Domain, Infrastructure) con una regla de dependencia estricta hacia el Domain.

Ver detalle completo en [docs/arquitectura.md](docs/arquitectura.md).

## Tecnologías utilizadas

El stack se organiza según el momento del ciclo de trabajo en el que interviene: desarrollo del código, testing, build/ejecución y entrega de versiones.

```mermaid
flowchart LR
    DEV["💻 Desarrollo\n.NET · ASP.NET Core\nDapper · SQL Server\nFluentValidation · Swagger · JWT"]
    TEST["🧪 Testing\nxUnit · Moq"]
    BUILD["⚙️ Build y ejecución\ndotnet CLI"]
    DEL["🚀 Entrega\nGit · GitHub"]

    DEV --> TEST --> BUILD --> DEL

    style DEV fill:#1b4332,stroke:#1b4332,color:#fff
    style TEST fill:#2d6a4f,stroke:#1b4332,color:#fff
    style BUILD fill:#40916c,stroke:#1b4332,color:#fff
    style DEL fill:#74c69d,stroke:#1b4332,color:#000
```

| Tecnología | Etapa | Uso en el proyecto |
|---|---|---|
| .NET / C# | Desarrollo | Lenguaje y runtime base de las cuatro capas |
| ASP.NET Core Web API | Desarrollo | Capa de presentación (`Precotex.Proyecto.Api`): controllers, middlewares, filters |
| Dapper | Desarrollo | Acceso a datos en `Infrastructure` (`DapperContext`, repositorios); nunca expone sus tipos fuera de esa capa |
| SQL Server | Desarrollo | Motor de base de datos consumido a través de `Infrastructure` |
| FluentValidation | Desarrollo | Reglas de validación de entrada en `Application/Validators` |
| Swagger / Swashbuckle | Desarrollo | Documentación interactiva de la API (`SwaggerOptions.cs`, `AddSwaggerConfig()`) |
| JWT | Desarrollo | Autenticación de la API (`JwtOptions.cs`, `AddAuthConfig()`) |
| xUnit / Moq | Testing | Pruebas unitarias por capa y dobles de prueba para dependencias externas |
| dotnet CLI | Build y ejecución | `restore`, `build` y `run` del proyecto (ver [Cómo ejecutar el proyecto](#cómo-ejecutar-el-proyecto)) |
| Git / GitHub | Entrega | Ramas `feature`/`fix`/`hotfix`, Pull Requests a `dev` y tags de versión en `main` |

> La solución todavía no incluye un proyecto de pruebas (`.csproj`) creado: xUnit y Moq quedan fijados aquí como el estándar del proyecto y se incorporarán al crear dicho proyecto.

Ver detalle completo del flujo de entrega en [docs/flujo-git.md](docs/flujo-git.md).

## Flujo de trabajo con Git

El repositorio utiliza un flujo basado en dos ramas principales (`main`, `dev`) y un proceso de QA como puerta de calidad antes de llegar a producción, con ramas `feature/`, `fix/` y `hotfix/`.

Ver detalle completo en [docs/flujo-git.md](docs/flujo-git.md).

## Requisitos previos

- .NET SDK (versión utilizada por el proyecto)
- SQL Server (local o remoto)
- Visual Studio 2022 / VS Code

## Cómo ejecutar el proyecto

```bash
git clone <url-del-repositorio>
cd Precotex.Proyecto
dotnet restore
dotnet build
dotnet run --project src/Precotex.API
```

Configurar la cadena de conexión a SQL Server en `appsettings.json` antes de ejecutar.
