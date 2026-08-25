#Ray #PDG #Contexto 

**¿Qué es Ray?** 

Ray es una herramienta _open-source_ (de código abierto) diseñada para el escalamiento de aplicaciones de Inteligencia Artificial (IA) y Python, con un fuerte enfoque en _Machine Learning_ (ML) y _Deep Learning_. Proporciona la capa de cálculo necesaria para el procesamiento en paralelo, permitiendo a los desarrolladores escalar cargas de trabajo sin necesidad de ser expertos en sistemas distribuidos.

En el contexto del **IAsLab**, Ray minimiza la complejidad de ejecutar flujos de trabajo de _Machine Learning_ distribuidos de extremo a extremo (MLOps), apoyándose en dos componentes principales:

1. **Bibliotecas escalables (Ray AI Libraries):** Optimizadas para tareas comunes como el preprocesamiento de datos, entrenamiento distribuido, ajuste de hiperparámetros (_tuning_), aprendizaje por refuerzo y, fundamental para el PDG, el **despliegue/servicio de modelos (Ray Serve)**.
    
2. **Primitivas de computación distribuida pitécnica (Ray Core):** Para paralelizar y escalar aplicaciones de Python con mínimos cambios en el código.
    

Ray se integra nativamente con herramientas e infraestructuras existentes, lo cual es ideal para implementarlo sobre los clústeres bare-metal del IAsLab o en plataformas de orquestación como **Kubernetes** (a través de KubeRay).

**Beneficios por Perfil de Usuario en IAsLab:**

- **Para los Investigadores y Estudiantes (Data Scientists / ML Practitioners):** Ray les permite escalar sus trabajos sin fricción. Podrán paralelizar y distribuir fácilmente sus cargas de trabajo (entrenamiento e inferencia) a través de múltiples nodos y GPUs, resolviendo el problema actual de interrupción en el flujo de trabajo al pasar de experimentación a producción.
    
- **Para los Ingenieros de la Plataforma MLOps (Tu rol en el PDG):**
    
    - Proporciona abstracciones de computación robustas para crear el **motor orquestador de inferencia agnóstico** que requiere el laboratorio.
        
    - Reduce la fricción entre el desarrollo y la producción; el mismo código de Python puede escalar desde el entorno local de un estudiante hasta el clúster completo del IAsLab.
        
- **Para la Gestión de Infraestructura Distribuida:** Ray maneja automáticamente procesos críticos:
    
    - **Orquestación:** Gestión de los distintos componentes del clúster.
        
    - **Programación (Scheduling):** Coordinación de cuándo y dónde se ejecutan las tareas, lo cual es clave para evitar la monopolización de GPUs.
        
    - **Tolerancia a fallos:** Asegura que las tareas se completen independientemente de caídas en los nodos.
        
    - **Auto-escalado:** Ajuste dinámico de recursos según la demanda.

![[¿Qué es Ray_-1787674478565.webp]]

**El Framework Unificado de Cómputo de Ray (3 Capas):**

1. **Ray AI Libraries:** Toolkit unificado para escalar aplicaciones de ML (Data, Train, Tune, Serve, RLlib).
    
2. **Ray Core:** Biblioteca de propósito general para computación distribuida en Python.
    
3. **Ray Clusters:** Conjunto de nodos _worker_ conectados a un nodo _head_. Soportan auto-escalado dinámico.
    

**Las 5 Bibliotecas Nativas de Ray:**

1. **Data:** Carga y transformación de datos escalable e independiente del framework.
    
2. **Train:** Entrenamiento de modelos multi-nodo y multi-núcleo con tolerancia a fallos.
    
3. **Tune:** Ajuste de hiperparámetros escalable para optimizar modelos.
    
4. **Serve (¡El más útil para el PDG!):** Servicio de modelos programable y escalable para el despliegue e inferencia en línea (soporta _microbatching_ para mejorar el rendimiento).
    
5. **RLlib:** Cargas de trabajo escalables para aprendizaje por refuerzo.
    

### Análisis de Utilidad para el IAsLab (Qué es más útil)

Para los objetivos específicos de tu PDG I, **Ray Serve** y **Ray Clusters** son, sin duda, los componentes más críticos.

1. **Ray Serve (Prioridad Alta):** El problema central que identificaste es que IAsLab no tiene mecanismos para el despliegue distribuido de modelos (especialmente LLMs). Ray Serve está diseñado exactamente para esto. Permite componer múltiples modelos (por ejemplo, procesamiento de lenguaje, visión, etc.) y exponerlos a través de APIs (como FastAPI) fácilmente. Soporta el enrutamiento de peticiones a GPUs específicas, lo que encaja perfectamente con tu objetivo de crear un "motor orquestador de inferencia".
    
2. **Ray Clusters (Prioridad Alta):** Para resolver la monopolización de las GPUs, necesitas entender cómo Ray administra los recursos. Al conectar los nodos del laboratorio a un _Ray Cluster_, Ray toma control de la asignación de CPUs y GPUs.
    
3. **Ray AI Libraries (Train & Data) (Prioridad Media):** Serán útiles en una segunda fase si decides que la plataforma también debe orquestar el entrenamiento distribuido de manera nativa, pero por ahora tu enfoque es la inferencia y el despliegue.