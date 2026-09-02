# Revisión de Cloud Design Patterns - Docket Microservices

  

De acuerdo con la historia de usuario, a continuación se presenta el análisis de los patrones de diseño en la nube (Cloud Design Patterns) actualmente presentes en el proyecto, así como la propuesta para implementar un patrón de resiliencia.

  

## 1. Patrones de Diseño Existentes (Documentación)

  

Se ha analizado la arquitectura de la aplicación y se han identificado los siguientes patrones en al menos 3 de los microservicios:

  

### A. `todos-api` (Node.js)

- **Publisher-Subscriber (Asynchronous Messaging):** Utiliza Redis para publicar eventos (mensajes) de auditoría o registro de operaciones en un canal (`log_channel`) cuando se crea o elimina un "todo".

- **Distributed Tracing (Trazabilidad Distribuida):** Integra `zipkin` para enviar trazas de las peticiones, permitiendo observar el flujo de las transacciones a lo largo de la aplicación.

- **Caching:** Utiliza caché en memoria (`memory-cache`) para almacenar los datos de los ToDos.

  

### B. `log-message-processor` (Python)

- **Event-Driven Architecture / Pub-Sub:** Actúa como suscriptor en el ecosistema, escuchando asíncronamente el canal de Redis para procesar los mensajes publicados por el `todos-api`. Esto desacopla el procesamiento de logs de la lógica de negocio principal.

- **Distributed Tracing:** Continúa la traza (span) iniciada por otros servicios utilizando `py_zipkin`, manteniendo la correlación (`trace_id` y `span_id`) que llega a través del mensaje de Redis.

  

### C. `auth-api` (Go)

- **Service-to-Service Communication (Synchronous):** Este microservicio se comunica sincrónicamente vía HTTP REST con el `users-api` para validar credenciales y obtener la información del usuario antes de generar el token JWT.

- **Distributed Tracing:** Implementa middleware para propagar las trazas hacia Zipkin.

  

### D. `users-api` (Java / Spring Boot)

- **Database per Service:** Mantiene su propia base de datos (H2 mediante Spring Data JPA) para gestionar los usuarios, garantizando el bajo acoplamiento de datos.

  

---

  

## 2. Propuesta de Patrón de Resiliencia

  

El equipo debe elegir un punto crítico de falla. En esta arquitectura, **el punto de falla más relevante** es la comunicación síncrona entre el `auth-api` y el `users-api`.

Si el servicio de usuarios experimenta lentitud o caídas, el servicio de autenticación sufrirá agotamiento de recursos (hilos/conexiones bloqueadas esperando respuesta), lo que tirará abajo todo el inicio de sesión del sistema.

  

### Patrón Elegido: Circuit Breaker (Cortocircuito) + Timeout

**Dónde implementarlo:** En el cliente HTTP dentro del microservicio `auth-api` (Go) al realizar la llamada a `users-api`.

  

**Justificación:**

- **Circuit Breaker:** Evitará que el `auth-api` siga enviando peticiones a un `users-api` que ya está caído, devolviendo un error rápido (Fail-fast) a los clientes y permitiendo que el servicio de usuarios se recupere.

- **Timeout:** Evitará que las peticiones se queden colgadas indefinidamente si el `users-api` se vuelve lento (latencia alta).

  

**¿Cómo se implementaría? (Diseño)**

1. En `auth-api` (`user.go` o `main.go`), se envolvería la llamada HTTP al `UserAPIAddress` utilizando una librería de Circuit Breaker (por ejemplo, `sony/gobreaker`).

2. Se configurarían umbrales: si el 50% de las peticiones en los últimos 10 segundos fallan o exceden un Timeout (ej. 2 segundos), el circuito se "Abre" (Open).

3. Mientras esté Abierto, el `auth-api` retornará inmediatamente un error 503 (Service Unavailable) sin intentar llamar a la red.

4. Tras un tiempo de enfriamiento, pasará a "Semi-Abierto" (Half-Open) para probar si el `users-api` ya se recuperó.

  

### 3. Prueba de Validación del Patrón (Estrategia)

  

Para cumplir con el tercer criterio de aceptación, la prueba se plantearía de la siguiente manera:

  

- **Tipo de prueba:** Test de Integración / Unitario en `auth-api`.

- **Escenario:** Simular una falla en el servicio dependiente (`users-api`).

- **Ejecución:**

1. Levantar un servidor HTTP Mock en Go (usando `httptest.Server`) que actúe como `users-api`.

2. Configurar el Mock para que devuelva errores HTTP 500 o para que aplique un `time.Sleep` superior al Timeout configurado.

3. Ejecutar un bucle de N peticiones de login contra el `auth-api`.

4. **Aserciones:**

- Comprobar que las primeras peticiones fallan por Timeout/500.

- Comprobar que, tras cruzar el umbral del Circuit Breaker, las peticiones subsiguientes devuelven un error inmediato (Fail-fast) *sin* que la petición llegue al servidor Mock (validando que el circuito está abierto).

- (Opcional) Esperar el tiempo de enfriamiento y comprobar que el sistema intenta acceder de nuevo al Mock.