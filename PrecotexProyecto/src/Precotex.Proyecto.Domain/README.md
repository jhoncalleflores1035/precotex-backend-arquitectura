# Precotex Proyecto Domain

## Descripción

Capa central de la solución donde se implementan las reglas del negocio y los servicios que ejecutan los casos de uso definidos por Application.
Contiene entidades, contratos de persistencia y lógica de dominio.
No contiene detalles de infraestructura como Dapper, EF, SQL o acceso HTTP.

## Estructura

```mermaid
flowchart TD
    ROOT["🗂️ Precotex.Proyecto.Domain"]
    ROOT --> ENT["📁 Entities"]
    ROOT --> SERV["📁 Services"]
    ROOT --> INT["📁 Interfaces"]
    ROOT --> ENUM["📁 Enums"]

    ENT --> ENTF["📄 Cliente, Pedido, Producto"]
    SERV --> SERVF["📄 PedidoService, ProductoService"]
    INT --> INTREPO["📁 Repositories"]
    ENUM --> ENUMF["📄 EstadoPedido, TipoCliente"]

    INTREPO --> INTREPOINV["📁 Inventario"]
    INTREPO --> INTREPOVEN["📁 Ventas"]

    style ROOT fill:#1b4332,stroke:#1b4332,color:#fff
    style ENT fill:#2d6a4f,stroke:#1b4332,color:#fff
    style SERV fill:#40916c,stroke:#1b4332,color:#fff
    style INT fill:#e76f51,stroke:#1b4332,color:#fff
    style ENUM fill:#ffb703,stroke:#1b4332,color:#000
    style ENTF fill:#e9ecef,stroke:#6c757d,color:#000
    style SERVF fill:#e9ecef,stroke:#6c757d,color:#000
    style INTREPO fill:#f4a261,stroke:#1b4332,color:#000
    style ENUMF fill:#e9ecef,stroke:#6c757d,color:#000
    style INTREPOINV fill:#e9ecef,stroke:#6c757d,color:#000
    style INTREPOVEN fill:#e9ecef,stroke:#6c757d,color:#000
```

---

## Responsabilidades

| Carpeta | Responsabilidad |
|---|---|
| `Entities/` | Entidades con identidad y comportamiento propio del negocio |
| `Enums/` | Valores cerrados definidos por el negocio | 
| `Services/` | Implementación de servicios definidos por Application para ejecutar casos de uso |
| `Interfaces/Repositories/{Modulo}/` | Contratos de persistencia que Infrastructure implementa |

---

## Flujo

La `API` consume el contrato de servicio definido en `Application`.
`Domain` implementa ese contrato y coordina las entidades del dominio, aplicando las reglas necesarias.
Cuando requiere persistencia utiliza IRepository, cuyo contrato pertenece a `Domain` y cuya implementación corresponde a `Infrastructure`.

```mermaid
flowchart LR
    A["🌐 API"] --> B["🔌 Application Service Contract"]
    B --> C["🟩 Domain Service"]
    C --> D["🟩 Domain Entity"]
    C --> E["🔻 IRepository"]
    E -.implementado por.-> F["🏗️ Infrastructure Repository"]

    style A fill:#74c69d,stroke:#1b4332,color:#000
    style B fill:#8ecae6,stroke:#1b4332,color:#000
    style C fill:#1b4332,stroke:#1b4332,color:#fff
    style D fill:#40916c,stroke:#1b4332,color:#fff
    style E fill:#e76f51,stroke:#1b4332,color:#fff
    style F fill:#f4a261,stroke:#1b4332,color:#000
```

---

## Dependencias

```mermaid
flowchart LR
    APP["⚙️ Application"] --> DOM["🟩 Domain"]
    INFRA["🏗️ Infraestructure"] --> DOM["🟩 Domain"] 

    style DOM fill:#2d6a4f,stroke:#1b4332,color:#fff
    style APP fill:#74c69d,stroke:#1b4332,color:#000
    style INFRA fill:#e76f51,stroke:#1b4332,color:#fff
```

`Application` depende de `Domain` para utilizar entidades y contratos.
`Infrastructure` depende de `Domain` para implementar los contratos de persistencia.
`Domain` no conoce la implementación concreta de `Infrastructure`.

---

## Qué NO pertenece aquí

| No colocar | Corresponde a |
|---|---|
| Controllers y lógica HTTP | `Api` |
| DTOs de entrada/salida | `Application` |
| Validación de formato de entrada | `Application` |
| Dapper, EF, SQL | `Infraestructure` |
| Implementación de repositorios | `Infraestructure` |
| Configuración de conexión | `Infraestructure` |

---

## Referencias

Ver arquitectura general en [docs/arquitectura.md](../../docs/arquitectura.md).
