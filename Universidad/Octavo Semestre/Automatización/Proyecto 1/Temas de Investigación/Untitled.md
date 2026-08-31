# Diagrama de Secuencia — microservice-app-example

Repositorio analizado: `bortizf/microservice-app-example` (fork del clásico "PRFT DevOps Training" microservices demo).

## Arquitectura de referencia

| Componente | Tecnología | Puerto típico | Responsabilidad |
|---|---|---|---|
| **Frontend** | Vue.js | 8080 | UI, guarda el JWT en Vuex/localStorage |
| **Auth API** | Go (Echo) | 8000 | `POST /login` → valida credenciales contra Users API y emite JWT (HS256) |
| **Users API** | Java Spring Boot | 8083 | `GET /users/{username}` protegido por JWT (filtro de seguridad) |
| **TODOs API** | Node.js (Express) | 8082 | CRUD de tareas en memoria por usuario, protegido por JWT, publica eventos en Redis |
| **Redis** | Redis Pub/Sub | 6379 | Canal `log_channel` para eventos CREATE/DELETE |
| **Log Message Processor** | Python | — | Se suscribe a Redis y procesa/loggea los eventos (con soporte de tracing Zipkin) |
| **Zipkin** (opcional) | Tracing | 9411 | Trazabilidad distribuida entre Auth API, TODOs API y Log Processor |

Flujo de negocio cubierto en el diagrama:
1. **Login** (Frontend → Auth API → Users API → generación de JWT).
2. **Listar TODOs** (`GET /todos`, con validación de JWT).
3. **Crear TODO** (`POST /todos`) + publicación asíncrona a Redis + consumo por Log Processor + traza a Zipkin.
4. **Eliminar TODO** (`DELETE /todos/:taskId`) con el mismo patrón de logging asíncrono.
5. **Manejo de error de autenticación** (token inválido/expirado).

---

## Diagrama de secuencia (Mermaid)

```mermaid
sequenceDiagram
    autonumber
    actor U as Usuario
    participant FE as Frontend (Vue.js)
    participant AUTH as Auth API (Go)
    participant USERS as Users API (Spring Boot)
    participant TODOS as TODOs API (Node/Express)
    participant REDIS as Redis (Pub/Sub)
    participant LOG as Log Message Processor (Python)
    participant ZK as Zipkin (Tracing)

    rect rgb(235, 245, 255)
    Note over U,USERS: 1. LOGIN Y GENERACIÓN DE JWT
    U->>FE: Ingresa username + password
    activate FE
    FE->>AUTH: POST /login {username, password}
    activate AUTH
    AUTH->>AUTH: Genera JWT interno de servicio (scope=read)
    AUTH->>USERS: GET /users/{username}\nHeader: Authorization Bearer <jwt-servicio>
    activate USERS
    USERS->>USERS: Valida JWT y claim "username" (AccessUserFilter)
    USERS-->>AUTH: 200 OK {username, firstname, lastname, role}
    deactivate USERS
    AUTH->>AUTH: userKey = username_password
    alt Credenciales válidas (hash en AllowedUserHashes)
        AUTH->>AUTH: Firma JWT de usuario (HS256, exp=72h,\nclaims: username, firstname, lastname, role)
        AUTH-->>FE: 200 OK {accessToken}
        FE->>FE: Decodifica JWT, guarda accessToken\nen Vuex store + localStorage
        FE-->>U: Redirige a vista de TODOs
    else Credenciales inválidas
        AUTH-->>FE: 401 Unauthorized\n{"username or password is invalid"}
        FE-->>U: Muestra mensaje de error
    end
    deactivate AUTH
    deactivate FE
    end

    rect rgb(235, 255, 240)
    Note over U,TODOS: 2. LISTAR TAREAS (carga inicial de la vista)
    U->>FE: Abre vista "Todos"
    activate FE
    FE->>TODOS: GET /todos\nHeader: Authorization Bearer <accessToken>
    activate TODOS
    TODOS->>TODOS: Middleware express-jwt valida token
    alt Token válido
        TODOS->>TODOS: _getTodoData(username)\n(cache en memoria, seed inicial si no existe)
        TODOS-->>FE: 200 OK [ {id, content}, ... ]
        FE-->>U: Renderiza lista de tareas
    else Token inválido / expirado
        TODOS-->>FE: 401 {"message":"invalid token"}
        FE->>FE: auth.logout() → limpia store
        FE-->>U: Redirige a Login
    end
    deactivate TODOS
    deactivate FE
    end

    rect rgb(255, 245, 230)
    Note over U,ZK: 3. CREAR TAREA (con logging asíncrono + tracing)
    U->>FE: Escribe tarea y pulsa "Add todo"
    activate FE
    FE->>TODOS: POST /todos {content}\nHeader: Authorization Bearer <accessToken>
    activate TODOS
    TODOS->>TODOS: Middleware JWT valida token
    TODOS->>ZK: (zipkin middleware) inicia span "todos-api"
    TODOS->>TODOS: create(): agrega item a cache,\nincrementa lastInsertedID
    TODOS-->>FE: 200 OK {id, content}
    FE-->>U: Actualiza UI con la nueva tarea
    par Publicación asíncrona del evento (no bloquea la respuesta HTTP)
        TODOS->>REDIS: PUBLISH log_channel\n{opName:"CREATE", username, todoId, zipkinSpan}
        activate REDIS
        REDIS-->>LOG: Mensaje entregado (subscriber de log_channel)
        deactivate REDIS
        activate LOG
        LOG->>LOG: Parsea JSON del mensaje
        alt ZIPKIN_URL configurado y hay zipkinSpan
            LOG->>ZK: Reporta span "save_log"\n(trace_id, parent_span_id heredados)
        end
        LOG->>LOG: log_message(): espera 0–2000ms\naleatorio y hace print() a stdout
        deactivate LOG
    end
    deactivate TODOS
    deactivate FE
    end

    rect rgb(255, 235, 235)
    Note over U,LOG: 4. ELIMINAR TAREA (mismo patrón CREATE/DELETE)
    U->>FE: Pulsa "eliminar" sobre una tarea
    activate FE
    FE->>TODOS: DELETE /todos/{taskId}\nHeader: Authorization Bearer <accessToken>
    activate TODOS
    TODOS->>TODOS: Middleware JWT valida token
    TODOS->>TODOS: delete(): elimina item del cache
    TODOS-->>FE: 204 No Content
    FE-->>U: Quita la tarea de la lista (UI)
    TODOS->>REDIS: PUBLISH log_channel\n{opName:"DELETE", username, todoId, zipkinSpan}
    activate REDIS
    REDIS-->>LOG: Mensaje entregado
    deactivate REDIS
    activate LOG
    LOG->>LOG: log_message(): procesa y hace print()
    deactivate LOG
    deactivate TODOS
    deactivate FE
    end

    Note over U,ZK: Todas las llamadas HTTP entre servicios (Auth API, TODOs API,\nLog Processor) pueden instrumentarse con Zipkin cuando\nZIPKIN_URL está definido, propagando trace_id/span_id end-to-end.
```

---

## Notas de diseño y buenas prácticas aplicadas al diagrama

- **`autonumber`**: numera automáticamente cada interacción para poder referenciarlas en documentación o tickets.
- **`activate` / `deactivate`**: refleja el ciclo de vida real de cada llamada síncrona (request/response), evitando ambigüedad sobre qué proceso "está vivo" en cada momento.
- **`alt` / `else`**: modela explícitamente los caminos de éxito y error (credenciales inválidas, token expirado/ inválido), que es donde más fallan los diagramas "felices" típicos.
- **`par` (paralelo)**: la publicación en Redis y el consumo por el Log Processor ocurren **después** de responder al cliente HTTP — es un patrón *fire-and-forget* asíncrono, no una llamada bloqueante. Esto es fiel al código real (`_logOperation` se invoca sin `await`/bloqueo antes del `res.json`).
- **`rect` con colores**: agrupa el diagrama en las 4 fases funcionales (login, listar, crear, eliminar) para que sea legible incluso siendo "completo".
- **Propagación de trazabilidad (Zipkin)**: se muestra cómo el `zipkinSpan` viaja dentro del payload de Redis para que el Log Processor pueda reconstruir el trace distribuido — un detalle fácil de omitir pero presente explícitamente en el código (`todoController.js` → `log-message-processor/main.py`).
- **Seguridad JWT en dos capas**: Auth API usa un JWT *de servicio* (scope=read) para autenticarse contra Users API, y genera un JWT *de usuario final* (con claims username/firstname/lastname/role) para el resto del sistema. El diagrama distingue ambos tokens explícitamente para no confundirlos.

## Cómo usar este diagrama

- El bloque ```mermaid``` se puede pegar directamente en GitHub (los `.md` renderizan Mermaid nativamente), GitLab, Notion, Obsidian, o en [mermaid.live](https://mermaid.live) para exportarlo como PNG/SVG.
- Si necesitas una versión separada por flujo (solo login, o solo CRUD), puedo dividir este diagrama en varios archivos más pequeños.
