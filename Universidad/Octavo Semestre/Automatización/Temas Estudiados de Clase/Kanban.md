#Kanban #Auto #PlatsII #Contexto #Universidad 

### ¿Qué es Kanban y qué problemas resuelve?

Kanban es un método visual para gestionar y optimizar el trabajo mediante el uso de tarjetas y tableros. Originalmente asociado a la manufactura _Lean_, hoy en día es uno de los marcos de trabajo Ágiles más populares.

**¿Qué problemas resuelve?**

1. **Visibilidad del flujo:** Permite visualizar el trabajo por fases, lo que ayuda a evitar la sobrecarga del equipo.
    
2. **Métricas claras:** Facilita la medición del tiempo en el que se completan las tareas, permitiendo saber qué tan efectivos somos (usando métricas como _Lead Time_ y _Cycle Time_).
    
3. **El síndrome de "mucho movimiento, poco avance":** Resuelve la frustración de sentir que se hace de todo, pero no se termina nada. Kanban cambia el enfoque de "empezar tareas" a "terminar tareas".
    

### Origen y Filosofía: Sistema Pull vs. Push

Kanban fue creado en la década de 1940 por Taiichi Ohno, un ingeniero industrial de Toyota, como parte del sistema _Just In Time_ (JIT). Su objetivo era controlar qué se produce, cuánto se produce y cuándo, ajustándose a la demanda real.

- **Sistema Push (Tradicional):** El trabajo se "empuja" al equipo sin importar si tienen capacidad o no (generando cuellos de botella).
    
- **Sistema Pull (Kanban):** El trabajo es "halado" por la siguiente etapa solo cuando esta tiene capacidad.
    

Kanban se entiende como una metodología ágil porque permite ajustar el trabajo en progreso con el equipo en base a las **capacidades reales de desarrollo y los límites de trabajo en curso (WIP - Work In Progress), garantizando una entrega continua de valor sin saturar a los desarrolladores.**

### El Tablero Kanban en el Software

En el desarrollo de software, Kanban es muy fácil de adoptar porque su artefacto principal es simplemente un tablero (virtualizado en herramientas como Jira, Trello, etc.). El tablero da un vistazo inmediato al estado del trabajo. Cada etapa del flujo de desarrollo es un "carril" o columna (por ejemplo: _To Do_, _In Progress_, _Code Review_, _QA_, _Done_). En estos carriles se mueven las tarjetas (tickets), las cuales deben contener toda la información técnica y de negocio necesaria para ejecutar la tarea.

### ¿Cómo implementar Kanban? (Paso a paso)

1. **Capacitar al equipo:** Asegurarse de que todos entienden los principios de flujo y visibilidad.
    
2. **Mapear el flujo de trabajo:** Definir los carriles del tablero según el ciclo de vida real del desarrollo en tu proyecto. Debe adecuarse a las necesidades técnicas y de negocio.
    
3. **Diseñar las tarjetas:** Definir qué información debe llevar un ticket (descripción, criterios de aceptación, dependencias). Se pueden usar colores, etiquetas (tags) o _swimlanes_ (carriles horizontales) para priorizar tareas urgentes (como _bugs_ críticos).
    
4. **Asignar roles de seguimiento:** Aunque Kanban no prescribe roles rígidos como Scrum, es útil que alguien verifique que el flujo se mantenga sano y no se violen las políticas.
    
5. **Medir y evitar cuellos de botella:** Utilizar el tablero para identificar dónde se estancan las tareas. Esto se mide con:
    
    - **Lead Time (LT):** Tiempo total desde que el cliente hace la petición hasta que se entrega en producción.
        
    - **Cycle Time (CT):** Tiempo real que el equipo pasa trabajando activamente en el ticket.
        
6. **Estandarizar:** Encontrar tiempos promedio de entrega para hacer estimaciones más precisas.
    
7. **Escalar:** Si el método funciona en un equipo o microservicio específico, extender su uso a otras áreas del proyecto.
    

### Reglas y Principios de Kanban adaptados al Desarrollo de Software

Aquí tienes la mejora y explicación detallada de las reglas, enfocadas directamente en el ciclo de vida del software:

**1. Calidad desde el origen (No envíes salidas defectuosas)**

- _En Software:_ Si un desarrollador sabe que su código falla o no pasa las pruebas unitarias, no debe pasarlo a la columna de _QA_ o _Testing_ solo para "deshacerse" de la tarea. Pasar errores a la siguiente fase sobrecarga a los _testers_ y rompe el flujo. Los _bugs_ deben corregirse en la fase donde se detectan antes de avanzar.
    

**2. Sistema Pull estricto (El proceso cliente pide lo justo y necesario)**

- _En Software:_ La columna de la derecha (ej. _QA_ u _Operaciones_) actúa como el "cliente" de la columna de la izquierda (ej. _Desarrollo_). El equipo de pruebas solo debe tomar (_pull_) nuevos tickets de desarrollo cuando tengan la capacidad para revisarlos. No se debe forzar a QA a recibir 20 tickets si solo pueden probar 5 al día.
    

**3. Límites WIP / Nivelar la producción (No generar más de lo que se puede procesar)**

- _En Software:_ Es obligatorio establecer límites de Trabajo en Progreso (WIP Limits) por columna. Por ejemplo, si en la columna _Code Review_ el límite es 3, y ya hay 3 tickets ahí, los desarrolladores **no pueden** empezar tareas nuevas. Deben detenerse y ayudar a revisar código para liberar espacio. Esto evita la acumulación de código no desplegado y promueve el trabajo en equipo.
    

**4. Visibilidad total (El tablero es la única fuente de la verdad)**

- _En Software:_ "Si no está en el tablero, no existe". Cualquier tarea, _bug_, refactorización de código, ajuste de infraestructura (como modificar un _Docker Compose_ o un _cluster_ de Kubernetes) o reunión técnica debe estar reflejada en una tarjeta. Esto evita el trabajo oculto, las interrupciones informales y permite saber exactamente en qué se invierte el tiempo.
    

**5. Gestión del Flujo (Analizar el elemento de trabajo y optimizar)**

- _En Software:_ Consiste en observar constantemente cómo se mueven los tickets. Si notas que los tickets pasan rápido por _Desarrollo_ pero se acumulan durante días en _Despliegue_, acabas de encontrar un cuello de botella. La solución técnica aquí no es programar más rápido, sino automatizar los despliegues (ej. integrando un pipeline CI/CD) para resolver el atasco.