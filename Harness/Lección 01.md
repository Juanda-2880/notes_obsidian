#IA #Agentes #Harness

Muchas veces consideramos que tenemos experiencia en ambito de la IA, entonces le entregamos a un agente un proyecto completo y lo que termina sucediendo es que termina añadiendo features que no son necesarias, relleno y no es nada de lo que se pidio.

Pero todo esto no es culpa del modelo, puede ser de que no se le este sacando el sufiente valor a un modelo de IA.


#### El mismo Caballo, Destinos Distintos 

Anthropic comparó el mismo prompt y el mismo modelo, pero era uno con Harness y otro sin Harness

El que no tenía Harness se demoró 20 minutos, 9 dolares; la aplicacion abria pero las entidadesno respondian a la entrada. El segundo con un Harness completo con tres agentes con un planner + generator + evaluatos tomo 6 horas y 200 dolares; se podia mover una entidad pero la fiisca tenia asperezas y una parade que bloqueaba el avance

No cambiaron el modelo, cambiaron el equipo de monta 

### Dónde se atascan realmente los agentes?

Lo mas comun: nunca definiste la tarea con claridad. Dices tipo "Añade una función de busuqeda" y la interpretacion del agente puede ser completamente distinta a la de la tuya: buscar que? texto completo o datos estructurados? etc etc, si no se especifica el agente adivina. Si acierta es suerte; si falla, corregirlo cuesta mas que haber sido especifico desde el principio. Es como entrar en un restaurante y decirle al chef "quiero pescado" si llega hervido, al vapor o en una olla picante queda al azar.

Incluso cuando se especifica la tarea, el proyecto tiene convenciones arquitectonicas implicitas que el agente no conoce. Tu equipo estandarizó SQLAlchemy 2.0 pero el agente escribe código 1.X por defecto. Todos los endpoints deben usar OAuth 2.0, pero esa regla solo vive en tu cabeza y en un mensaje de Slack de hace tres mesas. El agente no puede verlo. No es que no quiera cumplirla, es que directamente no sabe que existe.

El entorno tambien es una trampa. Entornor de desarrolllo incompleto, dependencias faltantes, versiones incorrectas de herramientas. El agente qeuma una ventana de contexto valiosa de fallos de Pip Install y discrepancias de version de Node en vez de resolver la tarea real. Es como contratar a un carpintero experto pero olvidar darle martillo, clavos y una mesa nivelada: por muy capaz que sea, no puede trabajar.

Todavia mas comun: no hay forma de verificar. No hay tests, no hay linnt, o los comandos de verificacion nunca se comunican al agente. El agente escribe codigo, lo mira y decide que esta bien y dice "Terminado"  Es como pedur a un estudiante que entrege deberes sin clave de respuestas. Cree que termino pero al corregir aparecen  muchos errores, pero cuando los agentes sienten que el contexto se agota entran en Context Anxiety.

Las tareas largas que cruzan sesiona son aun peores: los descubrimientos de la sesion anterior se pierden y cada sesion nueva debe volver a explorar la estructura del proyecto y entender la organizacion del codigo. Los angetes sin estado persistente ven como la tasa de fallos sube brucamente en tareas de mas de 30 minutos.

### Cuando algo Falla, Arregla primero el Harness

Si el mismo modelo funciona en tareas similares y bien estructuradas, asume que es un problema de harness. Es como un coche que se detiene: no sospechas inmediatamente del motor; primero miras si tiene gasolina 

Pasos Concretos:

Atribuye cada fallo a una capa especifica. No digas simplemente "El modelo es malo" Pregunta La tarea es poco clara? faltaba contexto? habia metodos de verificacion? Mapea cada fallo a una de las cinco capas: Especificacion de tarea, provision de contexto, entorno de ejecución, feedback de verificación y gestion de estado. Estas son capas diagnosticas practicas, no los seis terminos del glosario anterior. 

Escribir el definition of done explicita para cada tarea. No decir "Añade Busqueda". es mejor decir 


Completion criteria:
- New endpoint GET /api/search?q=xxx
- Supports pagination, default 20 items
- Results include highlighted snippets
- All new code passes pytest
- Type checking passes (mypy --strict)


Crea un archivo Agents.md ponlo en la raiz del repositorio para decirle al agente el stack tecnico, las convenciones arquitectonicas y los comandos de verificacion. Es el primer paso del Harness Engineering y uno de los de mayor retorno. Un solo AGENTS.md puede ser mas efectivo que actualizara un modelo mas caro 

Contruye un bucle diagnostico. No trates los dallos como "el modelo volvió a equivocarse". Tratalos como señales de que tu harness tiene un Defecto. En cada fallo, identifaca la capa, corriglea y evita repetir ese mode de fallo. Tras unas rondas, tu harness se fortalece y el rendimiento del agente se estabiliza. Es como reparar Carreteras: Cada bache que tapas hace más suave el siguiente tramo

Cuantifica las Mejoras. Mantén un registro sencillo: Si cada tarea tuvo éxito o falló, y qué capa causó el fallo. Después de unas rondas verás cuál capa es el cuello de Botella.

