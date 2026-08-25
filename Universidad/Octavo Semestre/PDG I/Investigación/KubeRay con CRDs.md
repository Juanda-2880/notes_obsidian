#Ray #PDG #Kubernetes

**¿Qué es KubeRay?** KubeRay es un potente operador de Kubernetes de código abierto que simplifica el despliegue y la gestión de aplicaciones Ray. Funciona extendiendo la API de Kubernetes mediante _Custom Resource Definitions_ (CRDs), lo que permite administrar clústeres y cargas de trabajo de Ray de forma nativa.

KubeRay ofrece 4 CRDs principales:

1. **RayCluster:** KubeRay gestiona todo el ciclo de vida de este clúster, incluyendo su creación, eliminación, auto-escalado dinámico y tolerancia a fallos. Es la infraestructura base sobre la que corre cualquier carga de trabajo.
    
2. **RayService:** Está compuesto por dos partes: un _RayCluster_ subyacente y los gráficos de despliegue de _Ray Serve_. Su principal ventaja es que ofrece alta disponibilidad (HA) y actualizaciones sin tiempo de inactividad (_zero-downtime upgrades_).
    
3. **RayJob:** Automatiza la creación de un _RayCluster_, envía un trabajo (job) cuando el clúster está listo y puede configurarse para eliminar automáticamente el clúster una vez que el trabajo finaliza.
    
4. **RayCronJob:** Se utiliza para ejecutar _RayJobs_ en un horario recurrente basado en una expresión cron. Es ideal para cargas de trabajo periódicas, como procesamiento por lotes o tareas programadas.
    

### Análisis de Utilidad para el IAsLab (Qué CRD elegir)

Para tu PDG, la elección del CRD correcto depende directamente del bloque de la arquitectura que estés construyendo:

- **Para el bloque "Deploy View / Deploy Service" (Prioridad Máxima):** Debes utilizar **RayService**. Dado que el objetivo es integrar un motor orquestador de inferencia para modelos ya entrenados (especialmente LLMs), `RayService` es la herramienta definitiva. Garantiza que los modelos estén siempre disponibles para recibir peticiones web, manejando el enrutamiento y permitiendo actualizar la versión del modelo sin apagar el servicio.
    
- **Para el desarrollo y prototipado:** Debes utilizar **RayCluster**. Si la latencia es crítica (es decir, no puedes esperar a que se levante un clúster nuevo cada vez que un investigador hace una prueba), un `RayCluster` persistente que se reutilice múltiples veces es la mejor opción.
    
- **Para entrenamientos efímeros o liberación de recursos (Fair-Share):** Aunque el texto original menciona `RayJob` para "ahorrar costos en la nube pública", el IAsLab funciona con infraestructura 100% local (on-premise). Sin embargo, **RayJob** sigue siendo muy útil para tu sistema de gobernanza de cuotas acoplado a SAAMFI. Si un estudiante solo necesita procesar un lote de datos, `RayJob` levanta los contenedores, hace el trabajo y los destruye automáticamente, liberando inmediatamente las GPUs para evitar la monopolización.
    

### Recomendaciones Estratégicas y Técnicas

1. **El rol del AssetManager:** Tu `AssetManager Service` actuará como el puente entre el sistema web y Kubernetes. Cuando un usuario solicite desplegar un modelo desde la interfaz, el `AssetManager` validará los permisos en SAAMFI y, si todo está correcto, generará un archivo YAML (o usará la API de Kubernetes en Python/Go) para crear un objeto **RayService** en el clúster.
    
2. **Manejo de Actualizaciones:** El IAsLab necesitará estabilidad. Como Ray no soporta actualizaciones continuas (_rolling upgrades_) de forma nativa en un `RayCluster` simple, el uso de `RayService` te salva de este problema para los modelos en producción, ya que KubeRay orquesta el cambio de tráfico sin que el usuario final note la caída.
    
3. **Telemetría Integrada:** KubeRay expone métricas de manera nativa. Esto te facilitará enormemente el objetivo de potenciar la observabilidad. Puedes configurar Prometheus para que raspe (scrape) las métricas de los CRDs (`RayCluster`, `RayService`) y alimentar los dashboards interactivos en Grafana.