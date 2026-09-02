# Cloud Design Patterns - Docket Microservices

  

Este documento describe detalladamente los patrones de diseño en la nube (Cloud Design Patterns) adoptados en la arquitectura de microservicios de **Docket**. Se expone su estado actual, las implementaciones realizadas para garantizar resiliencia, seguridad y eficiencia, y el problema puntual que solucionan dentro del ecosistema.

  

---

  

## 1. Patrón Circuit Breaker (Cortocircuito)

  

### ¿Qué problema soluciona?

En una arquitectura distribuida, la comunicación síncrona entre servicios es un punto único de falla (SPOF). Si un servicio dependiente experimenta alta latencia o caídas, el servicio llamante puede agotar sus propios recursos (hilos, conexiones, memoria) esperando una respuesta, desencadenando una falla en cascada que afecta a todo el sistema.

  

### ¿Qué se usó y cómo se implementó?

Se implementó el patrón **Circuit Breaker** (con un mecanismo de **Timeout**) en el microservicio `auth-api` (Go), que actúa como cliente síncrono del `users-api` (Java).

  

* **Implementación:** Se construyó una estructura `CircuitBreaker` nativa que envuelve al cliente HTTP (`HTTPDoer`).

* **Comportamiento (Fail-Fast):**

* **Estado Cerrado (Closed):** Las peticiones fluyen normalmente.

* **Estado Abierto (Open):** Si el 100% de 3 peticiones consecutivas fallan o exceden el Timeout (2s), el circuito se abre. Las siguientes peticiones fallan inmediatamente (`ErrCircuitOpen`) sin realizar tráfico de red, permitiendo que `users-api` se recupere.

* **Estado Semi-Abierto (Half-Open):** Tras un periodo de enfriamiento (ej. 10s), el circuito deja pasar una petición para probar la salud del servicio dependiente.

  

---

  

## 2. Patrón Asynchronous Messaging (Publisher-Subscriber)

  

### ¿Qué problema soluciona?

El acoplamiento fuerte en tareas secundarias (como la auditoría o la consolidación de logs) ralentiza las transacciones críticas para el usuario y genera dependencia directa entre el servicio de negocio y el servicio de análisis.

  

### ¿Qué se usó y cómo se implementó?

Se implementó un bus de mensajería usando **Redis (Pub/Sub)**, conectando al `todos-api` (Publisher) y al `log-message-processor` (Subscriber).

  

* **Cambios y Mejoras Aplicadas:**

* **Manejo de Errores (todos-api):** Se incluyó un bloque de control `try-catch` y un *callback* en la operación de publicación para asegurar que si Redis no está disponible, la transacción principal de creación/eliminación de ToDos no colapse.

* **Resiliencia en Conexión (log-message-processor):** Se añadió un ciclo iterativo de reintentos (`retry logic`) en el procesador Python para reconectar de forma automática si Redis tarda en iniciar o sufre cortes temporales.

  

---

  

## 3. Patrón Caching

  

### ¿Qué problema soluciona?

La lectura frecuente a la base de datos o a servicios externos para obtener datos inmutables o de baja rotación genera un costo de latencia alto e ineficiencia en el uso de recursos. Sin embargo, un caché mal implementado puede consumir toda la RAM disponible (Fuga de Memoria).

  

### ¿Qué se usó y cómo se implementó?

Se implementó caché en memoria temporal dentro del microservicio `todos-api` mediante la librería `memory-cache`.

  

* **Cambios y Mejoras Aplicadas (TTL):**

* Se refactorizó el almacenamiento añadiendo un **Time-to-Live (TTL)**. De este modo, los ToDos inactivos almacenados en la memoria del Node.js expirarán automáticamente (ej. 1 hora), previniendo problemas de sobrecarga de memoria (`Memory Leaks`).

  

---

  

## 4. Patrón Distributed Tracing (Trazabilidad Distribuida)

  

### ¿Qué problema soluciona?

En sistemas con múltiples microservicios interactuando, diagnosticar un cuello de botella o rastrear el ciclo de vida completo de una solicitud del usuario es prácticamente imposible sin un ID de correlación central.

  

### ¿Qué se usó y cómo se implementó?

Se usó **Zipkin** en todos los componentes del sistema (`todos-api`, `auth-api`, `users-api` y `log-message-processor`) para unificar la observabilidad del proyecto.

  

* **Cambios y Mejoras Aplicadas:**

* **Protección de Hilos:** Se detectó que el procesador de logs enviaba las trazas a Zipkin de manera indefinida sin control de tiempo. Se agregó un **Timeout estricto de 5 segundos** al cliente HTTP (`requests.post`) en el `log-message-processor` para evitar agotamiento de hilos si el servidor de Zipkin cae.

  

---

  

## 5. Patrón Database per Service

  

### ¿Qué problema soluciona?

Evita el acoplamiento a nivel de datos. Si varios microservicios acceden a la misma base de datos, cualquier cambio en el esquema impacta a múltiples equipos/servicios, y la base de datos se vuelve el punto único de falla y cuello de botella de escalabilidad.

  

### ¿Qué se usó y cómo se implementó?

El microservicio `users-api` en Java administra sus propios datos mediante **Spring Data JPA** acoplado a una base de datos relacional (H2). Ningún otro servicio tiene acceso directo a estas tablas, debiendo solicitar la información vía API REST.

  

* **Cambios y Mejoras (Seguridad):**

* Se corrigió una vulnerabilidad crítica donde el *Secreto JWT* (una configuración vital relacionada al acceso de la data y tokens) estaba expuesta ("hardcoded") en el código fuente. Se refactorizó para depender primariamente de variables de entorno, manteniendo el estándar de infraestructuras *Twelve-Factor App*.

  

---

*Documentación generada aplicando los estándares de Cloud Native y las mejores prácticas de código seguro y resiliente.*