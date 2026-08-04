# Precotex Proyecto

Este repositorio es la plantilla oficial para los proyectos Backend de Precotex.
Todo nuevo proyecto debe partir desde esta estructura.

## Objetivo

Proporcionar una plantilla estándar para el desarrollo de proyectos Backend basada en Clean Architecture, promoviendo una separación clara de responsabilidades, mantenibilidad, escalabilidad y buenas prácticas de desarrollo.

## Arquitectura

El proyecto sigue el enfoque de **Clean Architecture**, organizado en capas (API, Application, Domain, Infrastructure) con una regla de dependencia estricta hacia el Domain.

Ver detalle completo en [docs/arquitectura.md](docs/arquitectura.md).

## Tecnologías utilizadas

El stack se organiza según la capa de Clean Architecture donde interviene cada tecnología, siguiendo el mismo flujo de dependencias del proyecto: la API recibe la solicitud, Application la orquesta, Domain aplica las reglas e Infrastructure persiste contra SQL Server. Testing y entrega son transversales a las cuatro capas.

```mermaid
flowchart LR
    API["🌐 Api\nASP.NET Core · Swagger"] --> APPL["⚙️ Application\nFluentValidation"]
    APPL --> DOM["🟩 Domain\nC# / .NET"]
    INFRA["🏗️ Infrastructure\nDapper"] --> DOM
    INFRA --> DB[("🛢️ SQL Server")]

    API -.-> TEST["🧪 xUnit · Moq"]
    APPL -.-> TEST
    DOM -.-> TEST
    INFRA -.-> TEST
    TEST --> DEL["🚀 Git · GitHub"]

    style API fill:#74c69d,stroke:#1b4332,color:#000
    style APPL fill:#40916c,stroke:#1b4332,color:#fff
    style DOM fill:#1b4332,stroke:#1b4332,color:#fff
    style INFRA fill:#2d6a4f,stroke:#1b4332,color:#fff
    style DB fill:#219ebc,stroke:#1b4332,color:#fff
    style TEST fill:#ffb703,stroke:#1b4332,color:#000
    style DEL fill:#9d0208,stroke:#1b4332,color:#fff
```

| Tecnología | Capa / Etapa | Uso en el proyecto |
|---|---|---|
| C# / .NET | Domain | Lenguaje y runtime base de las cuatro capas |
| ASP.NET Core Web API | Api | Controllers, middlewares, filters (`Precotex.Proyecto.Api`) |
| Swagger / Swashbuckle | Api | Documentación interactiva de la API (`SwaggerOptions.cs`, `AddSwaggerConfig()`) |
| FluentValidation | Application | Reglas de validación de entrada en `Application/Validators` |
| Dapper | Infrastructure | Acceso a datos (`DapperContext`, repositorios); nunca expone sus tipos fuera de esa capa |
| SQL Server | Base de datos | Motor de base de datos consumido a través de `Infrastructure` |
| xUnit / Moq | Testing | Pruebas unitarias por capa y dobles de prueba para dependencias externas |
| Git / GitHub | Entrega | Ramas `feature`/`fix`/`hotfix`, Pull Requests a `dev` y tags de versión en `main` |


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
