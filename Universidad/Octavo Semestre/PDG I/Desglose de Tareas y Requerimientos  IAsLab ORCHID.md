
## Objetivo 1: Módulo de Observabilidad
*Desarrollar un módulo de observabilidad para la captura y visualización de métricas de hardware y de carga de trabajo, orientado a la supervisión del estado de los nodos.*

### 1.1. Infraestructura Base de Telemetría
- [ ] Desplegar **Prometheus** en el clúster de Kubernetes para la recolección de métricas.
- [ ] Desplegar e integrar **Loki** para la agregación centralizada de registros (logs) de los contenedores.
- [ ] Desplegar **Grafana** y conectarlo con Prometheus y Loki como fuentes de datos (Data Sources).

### 1.2. Instrumentación de Hardware (GPU/Nodos)
- [ ] Instalar y configurar el **NVIDIA DCGM Exporter** en los nodos de trabajo (workers) que poseen GPU.
- [ ] Configurar la recolección de métricas críticas de hardware:
  - [ ] Temperatura del núcleo de la GPU y memoria.
  - [ ] Utilización de memoria de video (VRAM).
  - [ ] Consumo de potencia/energía (W).
  - [ ] Throughput del bus PCIe.

### 1.3. Instrumentación de Cargas de Trabajo (Inferencia)
- [ ] Configurar la exportación de métricas desde los motores de inferencia (ej. vLLM, llama.cpp) hacia Prometheus.
- [ ] Definir y recolectar métricas de inferencia:
  - [ ] Tiempo hasta el primer token (TTFT).
  - [ ] Latencia entre tokens.
  - [ ] Throughput agregado (tokens por segundo).
  - [ ] Nivel de encolamiento/saturación de concurrencia.

### 1.4. Visualización y Alertas
- [ ] Diseñar y construir el **Panel de Hardware** en Grafana por nodo específico.
- [ ] Diseñar y construir el **Panel de Inferencia** en Grafana por modelo/despliegue.
- [ ] Configurar reglas de alerta automatizadas en Prometheus/Grafana (Ej: Alerta preventiva si el consumo de VRAM supera el 90% para evitar el congelamiento del nodo por picos de consumo eléctrico, según riesgo FR-03.4).

---

## Objetivo 2: Gobernanza de Recursos y Cuotas por Rol
*Implementar un sistema de cuotas y restricciones, acoplado a SAAMFI, que reserve cupos con prioridad de profesores sobre estudiantes.*

### 2.1. Integración de Identidad y Control de Acceso (RBAC)
- [ ] Integrar el servicio institucional **SAAMFI** mediante OAuth2/OIDC para la autenticación de usuarios.
- [ ] Desarrollar un componente mapeador que traduzca los atributos del token de SAAMFI a roles de la plataforma (Estudiante, Profesor).
- [ ] Crear e implementar un rol de **Administrador de Plataforma** propio del sistema.

### 2.2. Sistema de Cuotas y Políticas en Kubernetes
- [ ] Configurar *Namespaces* dinámicos o aislados en Kubernetes para separar los despliegues de distintos usuarios/roles.
- [ ] Implementar *ResourceQuotas* y *LimitRanges* en Kubernetes asociados a los roles (límites de CPU, RAM y GPU asignable).
- [ ] Diseñar e implementar la lógica de **sobreaprovisionamiento controlado** (margen del 10% al 20% de capacidad nominal).

### 2.3. Motor de Admisión, Prioridad y Reservas
- [ ] Implementar la capa de admisión y encolamiento (utilizando capacidades de KubeRay o controladores personalizados) que retenga trabajos si no hay capacidad.
- [ ] Desarrollar la lógica de **prioridad y expropiación (preemption)**: garantizar que una solicitud con rol "Profesor" pueda reclamar recursos ocupados o encolarse con prioridad alta.
- [ ] Desarrollar un sistema de **reservas anticipadas**, permitiendo agendar el uso de GPU por ventanas de tiempo.

### 2.4. Módulo Administrativo (Interfaz)
- [ ] Construir un backend/API para la gestión de las cuotas.
- [ ] Desarrollar la interfaz administrativa para que los administradores del IAsLab puedan ajustar límites, ver consumo global y gestionar roles sin usar la consola de comandos.

---

## Objetivo 3: Despliegue e Inferencia de Modelos
*Implementar sobre el clúster el despliegue concurrente de modelos mediante motores de inferencia empaquetados.*

### 3.1. Puesta en Marcha del Clúster Base
- [ ] Instalar Kubernetes en las estaciones de trabajo de la sala 104M (configurar nodo de control y nodos de trabajo).
- [ ] Configurar la red del clúster (CNI).
- [ ] Desplegar **NVIDIA GPU Operator** para aprovisionar controladores, runtime y el complemento de dispositivo GPU de forma declarativa.
- [ ] Configurar *Node Selectors*, marcas (taints) y tolerancias para dirigir las cargas aceleradas solo a las máquinas con GPU.

### 3.2. Orquestación de Cargas de Inteligencia Artificial
- [ ] Desplegar **KubeRay** en el clúster para habilitar la declaración de servicios de inferencia como recursos nativos (CRDs).
- [ ] Configurar Volúmenes Persistentes (PV/PVC) para almacenar los pesos de los modelos y evitar descargas repetitivas (caché de modelos).

### 3.3. Motores de Inferencia (Contenedores)
- [ ] Seleccionar y empaquetar en contenedores (Dockerfiles/Imágenes) los motores de inferencia.
- [ ] Configurar la imagen base para **llama.cpp** (enfocado en formatos GGUF cuantizados para maximizar los 24GB de VRAM de las RTX 4090).
- [ ] *(Opcional/Comparativo)* Configurar una imagen base para **vLLM** si se requiere evaluar el enfoque de *PagedAttention*.

### 3.4. Interfaz Web de Despliegue de Modelos (API Gateway & Frontend)
- [ ] Construir un componente *API Gateway* que concentre las solicitudes de inferencia y aplique límites de tasa (Rate Limiting).
- [ ] Desarrollar el servicio backend que reciba la petición del usuario y traduzca esto en un manifiesto YAML (CRD) para Kubernetes.
- [ ] Construir la interfaz web de usuario (Frontend) que permita:
  - Seleccionar un modelo ya entrenado.
  - Elegir el motor de inferencia.
  - Seleccionar el nodo o dejarlo a disposición del orquestador.
  - Iniciar y detener el despliegue.
  - Obtener el *endpoint* (URL/API) del modelo en servicio para consumirlo.

---

## Objetivo 4: Evaluación de Desempeño y Aceptación
*Evaluar el desempeño mediante pruebas de carga  y la aceptación mediante el System Usability Scale (SUS).*

### 4.1. Pruebas de Carga y Desempeño Técnico
- [ ] Seleccionar/configurar una herramienta de motor de benchmarking automatizado (ej. Locust, K6).
- [ ] Diseñar el script de prueba de carga simulando peticiones de inferencia (text generation) concurrentes.
- [ ] Recolectar datos técnicos (apoyándose en el Módulo de Observabilidad) asegurando que la red de la sala (10 Gbit/s) no sea un cuello de botella silencioso.
- [ ] Redactar el reporte de resultados comparando latencias y throughput bajo estrés.

### 4.2. Pruebas de Usabilidad (System Usability Scale)
- [ ] Traducir/adaptar el cuestionario de 10 ítems del **System Usability Scale (SUS)**.
- [ ] Definir la muestra de usuarios: Estudiantes, Profesores y Administradores del IAsLab.
- [ ] Diseñar las sesiones de prueba (asignar tareas guiadas: "inicia sesión, despliega un modelo, revisa tu cuota").
- [ ] Aplicar el cuestionario SUS post-prueba.
- [ ] Analizar los puntajes obtenidos comparándolos con las bandas de referencia calibradas publicadas en la literatura.
- [ ] Documentar los hallazgos de usabilidad en el informe final de PDG2.