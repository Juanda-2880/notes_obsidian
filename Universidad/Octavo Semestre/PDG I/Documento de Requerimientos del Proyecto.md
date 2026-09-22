
## 1. Introducción

### 1.1 Propósito

Este documento formaliza, en formato de **épicas** e **historias/requerimientos**, el desglose de tareas del proyecto **IAsLab ORCHID**, cuyo fin es dotar al laboratorio de Inteligencia Artificial (IAsLab) de una plataforma de clúster con Kubernetes para el despliegue, observabilidad, gobernanza de recursos y evaluación de modelos de IA (LLMs) en modo de inferencia concurrente y multiusuario.

### 1.2 Alcance del Proyecto

**Incluye (In Scope):**

- Despliegue de un clúster Kubernetes sobre las estaciones de trabajo de la sala 104M, con soporte de GPU (RTX 4090).
- Un módulo de observabilidad (métricas de hardware, logs y métricas de inferencia) basado en Prometheus, Loki y Grafana.
- Un sistema de gobernanza de recursos y cuotas por rol (Estudiante, Profesor, Administrador), integrado con el servicio institucional **SAAMFI** vía OAuth2/OIDC, incluyendo prioridad, expropiación (preemption) y reservas anticipadas.
- Orquestación de motores de inferencia (llama.cpp obligatorio, vLLM opcional/comparativo) mediante KubeRay/CRDs.
- Una interfaz web (frontend + backend/API Gateway) para desplegar, consumir y administrar modelos y cuotas.
- Evaluación de desempeño técnico (pruebas de carga) y de aceptación de usuario (SUS - System Usability Scale).
- Documentación y reporte final de resultados (informe PDG2).

**No incluye (Out of Scope):**

- Entrenamiento o _fine-tuning_ de modelos de IA (el proyecto usa modelos ya entrenados).
- Adquisición de hardware adicional fuera del disponible en la sala 104M.
- Soporte multi-clúster o multi-sede (federación de clústeres).
- Desarrollo de nuevos motores de inferencia desde cero (se usan motores existentes empaquetados: llama.cpp, vLLM).
- Migración o reemplazo del sistema SAAMFI institucional (solo integración/consumo vía OIDC).
- Soporte para sistemas operativos o hipervisores distintos a los definidos en la infraestructura base.

### 1.3 Stakeholders / Roles de Usuario

|Rol|Descripción|
|---|---|
|**Estudiante**|Usuario final que despliega y consume modelos dentro de su cuota asignada, con prioridad menor.|
|**Profesor**|Usuario con prioridad alta sobre recursos, puede reclamar/expropiar recursos y hacer reservas anticipadas.|
|**Administrador de Plataforma**|Rol propio del sistema; gestiona cuotas, roles y monitorea el clúster desde una interfaz sin consola de comandos.|
|**Equipo de desarrollo/PDG2**|Responsable de la implementación, pruebas de carga y documentación del proyecto.|

### 1.4 Supuestos y Restricciones

- La red de la sala 104M opera a 10 Gbit/s y no debe convertirse en cuello de botella durante las pruebas de carga.
- Las GPUs disponibles son RTX 4090 con 24 GB de VRAM; el empaquetado de modelos debe maximizar ese límite (formatos GGUF cuantizados).
- SAAMFI es el proveedor de identidad institucional y se asume disponibilidad de su servicio OAuth2/OIDC.
- Se dispone de capacidad de sobreaprovisionamiento controlado (10%-20% sobre la capacidad nominal).

### 1.5 Glosario

- **CRD:** Custom Resource Definition (Kubernetes).
- **RBAC:** Role-Based Access Control.
- **TTFT:** Time To First Token.
- **VRAM:** Video RAM (memoria de la GPU).
- **SUS:** System Usability Scale.
- **PVC/PV:** Persistent Volume Claim / Persistent Volume.
- **CNI:** Container Network Interface.

---

## 2. Épica 1 — Módulo de Observabilidad

**Objetivo de negocio:** Desarrollar un módulo de observabilidad para la captura y visualización de métricas de hardware y de carga de trabajo, orientado a la supervisión del estado de los nodos.

**Criterio de éxito de la épica:** Los administradores pueden ver, en tiempo real y por nodo/modelo, el estado de hardware y de inferencia, y reciben alertas automáticas ante condiciones de riesgo.

### 2.1 Feature: Infraestructura Base de Telemetría

|ID|Requerimiento|Prioridad|Criterios de Aceptación|
|---|---|---|---|
|RF-1.1.1|Desplegar Prometheus en el clúster de Kubernetes para la recolección de métricas.|Alta|Prometheus está desplegado y accesible dentro del clúster; recolecta al menos las métricas nativas de Kubernetes (kube-state-metrics/node-exporter).|
|RF-1.1.2|Desplegar e integrar Loki para la agregación centralizada de registros (logs) de los contenedores.|Alta|Los logs de todos los pods del clúster son centralizados y consultables desde Loki.|
|RF-1.1.3|Desplegar Grafana y conectarlo con Prometheus y Loki como fuentes de datos (Data Sources).|Alta|Grafana muestra correctamente datos provenientes de ambas fuentes en un mismo dashboard de prueba.|

### 2.2 Feature: Instrumentación de Hardware (GPU/Nodos)

|ID|Requerimiento|Prioridad|Criterios de Aceptación|
|---|---|---|---|
|RF-1.2.1|Instalar y configurar el NVIDIA DCGM Exporter en los nodos de trabajo (workers) que poseen GPU.|Alta|El exporter corre como DaemonSet/pod en cada nodo GPU y expone métricas vía endpoint Prometheus.|
|RF-1.2.2|Configurar la recolección de temperatura del núcleo de la GPU y memoria.|Alta|Prometheus almacena series temporales de temperatura de GPU y memoria por nodo.|
|RF-1.2.3|Configurar la recolección de utilización de memoria de video (VRAM).|Alta|Prometheus almacena el porcentaje/valor absoluto de uso de VRAM por GPU y nodo.|
|RF-1.2.4|Configurar la recolección de consumo de potencia/energía (W).|Media|Se dispone de series temporales de consumo eléctrico por GPU.|
|RF-1.2.5|Configurar la recolección de throughput del bus PCIe.|Media|Se dispone de series temporales del throughput PCIe por GPU.|

### 2.3 Feature: Instrumentación de Cargas de Trabajo (Inferencia)

|ID|Requerimiento|Prioridad|Criterios de Aceptación|
|---|---|---|---|
|RF-1.3.1|Configurar la exportación de métricas desde los motores de inferencia (ej. vLLM, llama.cpp) hacia Prometheus.|Alta|Cada motor de inferencia desplegado expone un endpoint `/metrics` scrapeado correctamente por Prometheus.|
|RF-1.3.2|Recolectar el tiempo hasta el primer token (TTFT).|Alta|Existe una métrica TTFT por despliegue/modelo consultable en Prometheus.|
|RF-1.3.3|Recolectar la latencia entre tokens.|Alta|Existe una métrica de latencia inter-token por despliegue/modelo.|
|RF-1.3.4|Recolectar el throughput agregado (tokens por segundo).|Alta|Existe una métrica de tokens/segundo agregada por despliegue.|
|RF-1.3.5|Recolectar el nivel de encolamiento/saturación de concurrencia.|Media|Existe una métrica que refleje peticiones en cola o nivel de saturación del motor.|

### 2.4 Feature: Visualización y Alertas

|ID|Requerimiento|Prioridad|Criterios de Aceptación|
|---|---|---|---|
|RF-1.4.1|Diseñar y construir el Panel de Hardware en Grafana por nodo específico.|Alta|Existe un dashboard filtrable por nodo que muestra temperatura, VRAM, potencia y PCIe.|
|RF-1.4.2|Diseñar y construir el Panel de Inferencia en Grafana por modelo/despliegue.|Alta|Existe un dashboard filtrable por modelo/despliegue que muestra TTFT, latencia, throughput y saturación.|
|RF-1.4.3|Configurar reglas de alerta automatizadas (ej. alerta preventiva si VRAM > 90%, riesgo FR-03.4 de congelamiento por picos eléctricos).|Alta|Se dispara una alerta (notificación) cuando el consumo de VRAM supera el 90% en cualquier GPU monitoreada.|

---

## 3. Épica 2 — Gobernanza de Recursos y Cuotas por Rol

**Objetivo de negocio:** Implementar un sistema de cuotas y restricciones, acoplado a SAAMFI, que reserve cupos con prioridad de profesores sobre estudiantes.

**Criterio de éxito de la épica:** El sistema autentica usuarios vía SAAMFI, asigna cuotas según rol, y garantiza prioridad de Profesores sobre Estudiantes en el acceso a GPU, incluyendo reservas y administración sin línea de comandos.

### 3.1 Feature: Integración de Identidad y Control de Acceso (RBAC)

|ID|Requerimiento|Prioridad|Criterios de Aceptación|
|---|---|---|---|
|RF-2.1.1|Integrar el servicio institucional SAAMFI mediante OAuth2/OIDC para la autenticación de usuarios.|Alta|Un usuario puede iniciar sesión en la plataforma usando sus credenciales institucionales SAAMFI.|
|RF-2.1.2|Desarrollar un componente mapeador que traduzca los atributos del token de SAAMFI a roles de la plataforma (Estudiante, Profesor).|Alta|Al iniciar sesión, el sistema asigna automáticamente el rol correcto según los atributos del token.|
|RF-2.1.3|Crear e implementar un rol de Administrador de Plataforma propio del sistema.|Alta|Existe un rol "Administrador" con permisos elevados, gestionable independientemente de SAAMFI.|

### 3.2 Feature: Sistema de Cuotas y Políticas en Kubernetes

|ID|Requerimiento|Prioridad|Criterios de Aceptación|
|---|---|---|---|
|RF-2.2.1|Configurar Namespaces dinámicos o aislados en Kubernetes para separar los despliegues de distintos usuarios/roles.|Alta|Cada usuario/rol opera en un namespace aislado; no hay colisión de recursos entre usuarios.|
|RF-2.2.2|Implementar ResourceQuotas y LimitRanges asociados a los roles (límites de CPU, RAM y GPU asignable).|Alta|Un usuario no puede exceder los límites de CPU/RAM/GPU definidos para su rol; el sistema rechaza o encola la solicitud excedente.|
|RF-2.2.3|Diseñar e implementar la lógica de sobreaprovisionamiento controlado (margen del 10% al 20% de capacidad nominal).|Media|El sistema permite exceder la capacidad nominal hasta el margen configurado sin degradar el clúster.|

### 3.3 Feature: Motor de Admisión, Prioridad y Reservas

|ID|Requerimiento|Prioridad|Criterios de Aceptación|
|---|---|---|---|
|RF-2.3.1|Implementar la capa de admisión y encolamiento (KubeRay o controladores personalizados) que retenga trabajos si no hay capacidad.|Alta|Una solicitud que exceda la capacidad disponible queda en cola en lugar de fallar o descartarse.|
|RF-2.3.2|Desarrollar la lógica de prioridad y expropiación (preemption): una solicitud "Profesor" puede reclamar recursos ocupados o encolarse con prioridad alta.|Alta|Ante contención de recursos, una solicitud de Profesor desplaza o se antepone a una de Estudiante, de forma verificable en pruebas.|
|RF-2.3.3|Desarrollar un sistema de reservas anticipadas, permitiendo agendar el uso de GPU por ventanas de tiempo.|Media|Un usuario puede reservar una GPU para una ventana horaria futura y el sistema respeta dicha reserva al llegar la hora.|
|RF-2.3.4|Construir un backend/API para la gestión de las cuotas.|Alta|Existe una API que expone operaciones CRUD sobre cuotas, consumible por el módulo administrativo.|

### 3.4 Feature: Módulo Administrativo (Interfaz)

|ID|Requerimiento|Prioridad|Criterios de Aceptación|
|---|---|---|---|
|RF-2.4.1|Desarrollar la interfaz administrativa para que los administradores ajusten límites, vean consumo global y gestionen roles sin usar la consola de comandos.|Alta|Un Administrador puede modificar cuotas y roles, y visualizar el consumo global del clúster, completamente desde la interfaz web.|

---

## 4. Épica 3 — Despliegue e Inferencia de Modelos

**Objetivo de negocio:** Implementar sobre el clúster el despliegue concurrente de modelos mediante motores de inferencia empaquetados.

**Criterio de éxito de la épica:** El clúster base está operativo con soporte GPU, y los usuarios pueden desplegar modelos de inferencia concurrentemente a través de una interfaz web y un API Gateway.

### 4.1 Feature: Puesta en Marcha del Clúster Base

|ID|Requerimiento|Prioridad|Criterios de Aceptación|
|---|---|---|---|
|RF-3.1.1|Instalar Kubernetes en las estaciones de trabajo de la sala 104M (nodo de control y nodos de trabajo).|Alta|El clúster está operativo con al menos un nodo de control y los nodos de trabajo esperados en estado `Ready`.|
|RF-3.1.2|Configurar la red del clúster (CNI).|Alta|Los pods de distintos nodos pueden comunicarse entre sí según la política de red definida.|
|RF-3.1.3|Desplegar NVIDIA GPU Operator para aprovisionar controladores, runtime y el complemento de dispositivo GPU de forma declarativa.|Alta|Los nodos con GPU exponen recursos `nvidia.com/gpu` disponibles para ser solicitados por pods.|
|RF-3.1.4|Configurar Node Selectors, taints y tolerancias para dirigir las cargas aceleradas solo a las máquinas con GPU.|Alta|Los pods que requieren GPU se programan únicamente en nodos con GPU; los pods sin tolerancia no se programan ahí.|

### 4.2 Feature: Orquestación de Cargas de Inteligencia Artificial

|ID|Requerimiento|Prioridad|Criterios de Aceptación|
|---|---|---|---|
|RF-3.2.1|Desplegar KubeRay en el clúster para habilitar la declaración de servicios de inferencia como recursos nativos (CRDs).|Alta|Es posible crear un servicio de inferencia mediante un manifiesto CRD gestionado por KubeRay.|
|RF-3.2.2|Configurar Volúmenes Persistentes (PV/PVC) para almacenar los pesos de los modelos y evitar descargas repetitivas (caché de modelos).|Alta|Los pesos de un modelo descargado persisten entre reinicios de pod y no se vuelven a descargar innecesariamente.|

### 4.3 Feature: Motores de Inferencia (Contenedores)

|ID|Requerimiento|Prioridad|Criterios de Aceptación|
|---|---|---|---|
|RF-3.3.1|Seleccionar y empaquetar en contenedores (Dockerfiles/Imágenes) los motores de inferencia.|Alta|Existen imágenes Docker funcionales para al menos un motor de inferencia, versionadas en un registro accesible.|
|RF-3.3.2|Configurar la imagen base para llama.cpp, enfocada en formatos GGUF cuantizados para maximizar los 24GB de VRAM de las RTX 4090.|Alta|Un modelo GGUF cuantizado se ejecuta en llama.cpp dentro del clúster utilizando eficientemente la VRAM disponible.|
|RF-3.3.3|(Opcional/Comparativo) Configurar una imagen base para vLLM para evaluar el enfoque de PagedAttention.|Baja|Existe una imagen vLLM funcional que permite comparar desempeño frente a llama.cpp (si se decide ejecutar esta tarea opcional).|

### 4.4 Feature: Interfaz Web de Despliegue de Modelos (API Gateway & Frontend)

|ID|Requerimiento|Prioridad|Criterios de Aceptación|
|---|---|---|---|
|RF-3.4.1|Construir un componente API Gateway que concentre las solicitudes de inferencia y aplique límites de tasa (Rate Limiting).|Alta|Todas las solicitudes de inferencia pasan por el Gateway y las que excedan el límite de tasa configurado son rechazadas o encoladas.|
|RF-3.4.2|Desarrollar el servicio backend que reciba la petición del usuario y la traduzca en un manifiesto YAML (CRD) para Kubernetes.|Alta|Una solicitud de despliegue desde el frontend genera un CRD válido aplicado automáticamente al clúster.|
|RF-3.4.3|Construir la interfaz web (Frontend) que permita seleccionar un modelo ya entrenado.|Alta|El usuario visualiza y selecciona, desde un catálogo, un modelo previamente entrenado disponible.|
|RF-3.4.4|Permitir elegir el motor de inferencia desde el frontend.|Alta|El usuario puede elegir entre los motores de inferencia habilitados (ej. llama.cpp, vLLM) antes de desplegar.|
|RF-3.4.5|Permitir seleccionar el nodo o dejarlo a disposición del orquestador.|Media|El usuario puede fijar un nodo específico o dejar la decisión al scheduler de Kubernetes.|
|RF-3.4.6|Permitir iniciar y detener el despliegue desde el frontend.|Alta|El usuario puede iniciar y detener un despliegue de modelo y el estado se refleja correctamente en la interfaz.|
|RF-3.4.7|Permitir obtener el endpoint (URL/API) del modelo en servicio para consumirlo.|Alta|Tras un despliegue exitoso, la interfaz muestra un endpoint funcional y consumible externamente.|

---

## 5. Épica 4 — Evaluación de Desempeño y Aceptación

**Objetivo de negocio:** Evaluar el desempeño mediante pruebas de carga y la aceptación mediante el System Usability Scale (SUS).

**Criterio de éxito de la épica:** Se cuenta con evidencia cuantitativa de desempeño técnico bajo carga y evidencia cualitativa/cuantitativa de usabilidad, documentadas en el informe final del proyecto (PDG2).

### 5.1 Feature: Pruebas de Carga y Desempeño Técnico

|ID|Requerimiento|Prioridad|Criterios de Aceptación|
|---|---|---|---|
|RF-4.1.1|Seleccionar/configurar una herramienta de motor de benchmarking automatizado (ej. Locust, K6).|Alta|La herramienta seleccionada está instalada y configurada para apuntar al endpoint de inferencia del sistema.|
|RF-4.1.2|Diseñar el script de prueba de carga simulando peticiones de inferencia (text generation) concurrentes.|Alta|El script genera carga concurrente configurable (número de usuarios/peticiones) contra el sistema.|
|RF-4.1.3|Recolectar datos técnicos apoyándose en el Módulo de Observabilidad, asegurando que la red de la sala (10 Gbit/s) no sea un cuello de botella silencioso.|Alta|Los resultados de la prueba de carga incluyen métricas de red que descartan explícitamente el ancho de banda como cuello de botella.|
|RF-4.1.4|Redactar el reporte de resultados comparando latencias y throughput bajo estrés.|Alta|Existe un documento/reporte con comparativas de latencia y throughput en distintos escenarios de carga.|

### 5.2 Feature: Pruebas de Usabilidad (System Usability Scale)

|ID|Requerimiento|Prioridad|Criterios de Aceptación|
|---|---|---|---|
|RF-4.2.1|Traducir/adaptar el cuestionario de 10 ítems del System Usability Scale (SUS).|Alta|Existe una versión en español, validada, del cuestionario SUS de 10 ítems.|
|RF-4.2.2|Definir la muestra de usuarios: Estudiantes, Profesores y Administradores del IAsLab.|Alta|Se documenta una muestra representativa de los tres roles, con tamaño y criterios de selección definidos.|
|RF-4.2.3|Diseñar las sesiones de prueba (tareas guiadas: "inicia sesión, despliega un modelo, revisa tu cuota").|Alta|Existe un guion de sesión de prueba con tareas concretas y medibles para cada participante.|
|RF-4.2.4|Aplicar el cuestionario SUS post-prueba.|Alta|Se recopilan respuestas del cuestionario SUS de todos los participantes de la muestra definida.|
|RF-4.2.5|Analizar los puntajes obtenidos comparándolos con las bandas de referencia calibradas publicadas en la literatura.|Alta|Se calcula el puntaje SUS promedio y se interpreta contra bandas de referencia (ej. escala de adjetivos/percentiles de Bangor et al.).|
|RF-4.2.6|Documentar los hallazgos de usabilidad en el informe final de PDG2.|Alta|El informe final incluye una sección de resultados de usabilidad con hallazgos, puntajes y recomendaciones.|

---

## 6. Requerimientos No Funcionales (RNF)

|ID|Requerimiento|Categoría|
|---|---|---|
|RNF-01|El sistema debe soportar múltiples usuarios concurrentes desplegando/consumiendo modelos sin degradación significativa del rendimiento dentro de los límites de cuota.|Rendimiento|
|RNF-02|El módulo de observabilidad debe reflejar métricas de hardware y de inferencia con una latencia de actualización que permita alertamiento preventivo oportuno.|Rendimiento / Confiabilidad|
|RNF-03|El sistema debe prevenir el congelamiento de nodos por picos de consumo eléctrico (VRAM > 90%) mediante alertas automáticas (riesgo FR-03.4).|Confiabilidad|
|RNF-04|El acceso a la plataforma debe estar protegido mediante autenticación federada (SAAMFI/OIDC); no se permite acceso anónimo a funciones de despliegue.|Seguridad|
|RNF-05|El sistema debe garantizar el aislamiento de recursos entre usuarios (namespaces, cuotas) para evitar que un usuario afecte el desempeño de otro.|Seguridad / Aislamiento|
|RNF-06|La interfaz administrativa y de usuario debe ser utilizable sin conocimientos de línea de comandos ni de Kubernetes.|Usabilidad|
|RNF-07|El sistema debe alcanzar un puntaje SUS considerado aceptable (según bandas de referencia de la literatura) en las pruebas de usabilidad.|Usabilidad / Aceptación|
|RNF-08|La red de la sala 104M (10 Gbit/s) no debe ser un cuello de botella para los flujos de inferencia bajo las cargas de prueba definidas.|Rendimiento / Infraestructura|
|RNF-09|Los pesos de los modelos deben persistir mediante almacenamiento persistente (PV/PVC), evitando descargas repetidas.|Eficiencia / Infraestructura|
|RNF-10|El sobreaprovisionamiento de recursos debe mantenerse dentro de un margen controlado (10%-20%) para no comprometer la estabilidad del clúster.|Confiabilidad|

---

## 7. Trazabilidad Épicas → Objetivos del Proyecto

|Épica|Objetivo original|Features asociadas|
|---|---|---|
|Épica 1|Objetivo 1: Módulo de Observabilidad|Infraestructura de Telemetría, Instrumentación HW, Instrumentación de Cargas, Visualización y Alertas|
|Épica 2|Objetivo 2: Gobernanza de Recursos y Cuotas por Rol|Identidad/RBAC, Cuotas y Políticas K8s, Admisión/Prioridad/Reservas, Módulo Administrativo|
|Épica 3|Objetivo 3: Despliegue e Inferencia de Modelos|Clúster Base, Orquestación de Cargas IA, Motores de Inferencia, Interfaz Web/API Gateway|
|Épica 4|Objetivo 4: Evaluación de Desempeño y Aceptación|Pruebas de Carga, Pruebas de Usabilidad SUS|

---

## 8. Priorización General (Resumen)

- **Prioridad Alta:** Infraestructura base (Kubernetes, GPU Operator, Prometheus/Loki/Grafana), autenticación SAAMFI, cuotas y expropiación por rol, despliegue de llama.cpp, API Gateway, frontend de despliegue, pruebas de carga y SUS.
- **Prioridad Media:** Métricas secundarias de hardware (potencia, PCIe), sobreaprovisionamiento controlado, reservas anticipadas, selección manual de nodo.
- **Prioridad Baja:** Integración comparativa de vLLM (marcada como opcional en el documento fuente).