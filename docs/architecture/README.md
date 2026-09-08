# RaceFlow — Documentación de Arquitectura (C4)

Diagramas de arquitectura del sistema usando el [modelo C4](https://c4model.com/),
renderizados con [C4-PlantUML](https://github.com/plantuml-stdlib/C4-PlantUML) a
partir del modelo fuente en [`workspace.dsl`](workspace.dsl). El modelo se creó
originalmente con [Structurizr Lite](https://structurizr.com/help/lite), pero esa
herramienta fue descontinuada por sus autores; `workspace.dsl` se conserva como
referencia legible del modelo, mientras que los `.puml` en `export/` son la
fuente real de los diagramas.

> Para el mapeo de los estilos de comunicación distribuida (sockets, HTTP, RMI, gRPC,
> microservicios, API Gateway) contra la arquitectura real de RaceFlow, ver
> [EVOLUCION_ARQUITECTONICA.md](EVOLUCION_ARQUITECTONICA.md).

## Diagramas exportados

### Nivel 1 — Contexto del sistema

> El Atleta interactúa con la plataforma RaceFlow a través de HTTPS y WebSocket.
> RaceFlow consume el Servicio de Mapas (OpenStreetMap) y la API de Geolocalización del navegador.

![Contexto](export/structurizr-Contexto.png)

### Nivel 2 — Contenedores

> Detalla los contenedores desplegables: la SPA React, el API Gateway (Spring Cloud Gateway),
> los 5 microservicios Spring Boot, Redis, RabbitMQ y las 4 bases de datos PostgreSQL.

![Contenedores](export/structurizr-Contenedores.png)

### Nivel 3 — Componentes del Realtime Service

> Zoom interno del servicio más crítico: `WebSocketAuthInterceptor` → `RoomWebSocketHandler`
> → `RoomManager` (resuelve nombre vía `GrpcAuthClient`) y → `RankingService` → `RankingStrategy`
> (Strategy), con el ranking cacheado en Redis.

![Componentes Realtime](export/structurizr-Componentes_Realtime.png)

---

## Editar y regenerar los diagramas

Structurizr Lite (la herramienta original de este proyecto) fue descontinuada
por sus autores y ya no se levanta. Los diagramas ahora se editan directamente
en los archivos `.puml` de `export/`, usando la sintaxis de
[C4-PlantUML](https://github.com/plantuml-stdlib/C4-PlantUML).

**Requisito:** Docker Desktop instalado.

```bash
docker run -d --name plantuml-server -p 8082:8080 plantuml/plantuml-server
docker cp export/structurizr-Contexto.puml plantuml-server:/tmp/contexto.puml
docker exec plantuml-server java -jar /usr/local/plantuml.jar -tpng /tmp/contexto.puml
docker cp plantuml-server:/tmp/Contexto.png export/structurizr-Contexto.png
```

Repite el mismo patrón para `structurizr-Contenedores.puml` y
`structurizr-Componentes_Realtime.puml`. Si cambias el modelo, actualiza tanto
`workspace.dsl` (referencia legible) como los `.puml` correspondientes — hoy
son independientes, no se generan el uno del otro.

## Vistas disponibles

| Vista | ID | Descripción |
|---|---|---|
| **System Context** | `Contexto` | Actores externos y relación de alto nivel con RaceFlow |
| **Containers** | `Contenedores` | SPA + Gateway + 5 microservicios + Redis + RabbitMQ + 4 DBs |
| **Component** | `Componentes_Realtime` | Componentes internos del Realtime/Ranking Service |

## Estructura

```
docs/architecture/
├── workspace.dsl       ← modelo C4 en DSL de Structurizr (referencia legible)
├── workspace.json      ← estado legado de Structurizr Lite (ya no se usa)
├── README.md           ← este archivo
└── export/
    ├── structurizr-Contexto.png
    ├── structurizr-Contexto.puml
    ├── structurizr-Contenedores.png
    ├── structurizr-Contenedores.puml
    ├── structurizr-Componentes_Realtime.png
    └── structurizr-Componentes_Realtime.puml
```

## Referencia rápida del DSL

| Elemento | Descripción |
|---|---|
| `softwareSystem` | Sistema completo o sistema externo |
| `container` | Proceso/aplicación desplegable dentro del sistema |
| `component` | Unidad de código dentro de un contenedor |
| `person` | Actor humano que interactúa con el sistema |
| `autoLayout lr` | Disposición automática izquierda → derecha |
| tag `"Database"` | Renderiza el shape como cilindro |
| tag `"Cache"` | Cilindro rojo (Redis) |
| tag `"Broker"` | Pipe (RabbitMQ) |