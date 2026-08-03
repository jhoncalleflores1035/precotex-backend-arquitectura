# Precotex Proyecto

## Objetivo

El objetivo principal de este proyecto es el desarrollo de una solución de software robusta, mantenible y escalable, construida bajo los principios de **Clean Architecture**. Precotex nace como una base técnica sólida que separa claramente las responsabilidades entre capas (Domain, Application, Infrastructure y API), facilitando la evolución del sistema, la incorporación de nuevos módulos de negocio y la aplicación de buenas prácticas de desarrollo como la inversión de dependencias, la validación centralizada y el acceso a datos desacoplado mediante Dapper.

## Arquitectura

El proyecto sigue el enfoque de **Clean Architecture**, organizado en capas (API, Application, Domain, Infrastructure) con una regla de dependencia estricta hacia el Domain.

Ver detalle completo en [docs/arquitectura.md](docs/arquitectura.md).

## Tecnologías utilizadas

| Tecnología | Uso |
|---|---|
| .NET / C# | Lenguaje y framework principal |
| ASP.NET Core Web API | Capa de presentación / API REST |
| Dapper | Micro ORM para acceso a datos en la capa de Infrastructure |
| SQL Server | Motor de base de datos |
| xUnit / Moq (o equivalente) | Pruebas unitarias |
| Git / GitHub | Control de versiones |

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
