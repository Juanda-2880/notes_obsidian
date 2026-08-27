#Universidad #Auto #Diagrama

Los diagramas de secuencia sirven para describir intercambios de mensaje dentro del proyecto. Puede colocar mensajes en un diagrama de secuencia como parte del desarrollo del sistema de Software. 

El diagrama de secuencia muestra escenarios de intercambios de mensajes entre roles desempeñados por objetos. Esta funcionalidad se puede utilizar de varias maneras, incluyendo escenarios de análisis y diseño, rastreos de ejecución, comportamiento esperado en casos de pruebas, etc.

Los diagramas de secuencia le ayudan a comprender las interacciones y relaciones entre objetos mostrando los mensajes que se envían entre sí a lo largo del tiempo. Además, son la herramienta clave para ver la ejecucción animada. Cuando ejecuta un programa animado, su dinámica del sistema se muestra como interacciones entre los objetos y la temporización relativa de los sucesos. 

----

### Diseño de Diagrama de Secuencia

Un diagrama de secuencia tiene dos secciones:

- Panel de nombres, que es un control para identificar líneas de instancia cuando los nombres de rol no son visible 
- El panel de mensajes muestra los mensajes pasados entre líneas de instancia en el diagrama

![[Diagrama de Secuencia-1787839847981.webp]]

- Panel Nombre
Contiene el nombre de cada línea de instancia o rol de clasificador. En un diagrama de secuencia, un rol de clasificador representa una instancia de un clasificador. Describe un rol específico que desempaña la instancia de clasificador para realizar una tarea determinada

- Panel Mensaje
Contiene los elementos que componen la interacción. En el panel de objetos, los bordes del sistema y las instancias se muestran como línea de instancia, que son líneas verticales con un recuadro que contiene el nombre del rol 

-----

### Panel Nombre

El panel nombre contiene el nombre de cada lía de instancia o rol de clasificador. En un diagrama de secuencia, un rol de Claificador representa una instancia de un clasificador. Describe un rol específico que desempeña la instancia de clasificador para realizar una tarea determinada. Un rol de clasificador se muestra como una línea de instancia con una cabecera de texto (nombre) con un recuadro dibujado a su alrededor. Un rol de clasificador puede realizar un clasificador (clase o actor) del modelo estático.

Los nombres que son demasiado largos para caber en el panel continúan más allá del divisor, bajando por detrás del panel. 

Hay tres menaras de describir el nombre 


Classifier Role Name: Classifier Name
                   : Classifier Name
Classifier Role Name


En los dos primeros casos, si el nombre del clasificador no eciste en el metamodelo, Rhapsody le preguntará si desea añadir un nuevo clasificador al proyecto. El tercer caso indica al producto que desea utilizar un rol clasificador *Unspecified*, lo que significa que el rol clasificador no es una realización de un clasificador o actor existente.

Si cambia el nombre de un rol de clasificador por un nombre de rol que existe en el modelo, el rol de clasificador se realiza automáticamente en dicho clasificador.

----
### Panel Mensaje

Contiene los elementos que componen la interacción. En el panel de Objetos , los bordes del sistema y las instancias se muestran como línea de instancia, que son líneas verticales con un recuadro que contiene el nombre del rol. Los mensaje como, por ejemplo, sucesos, operaciones y tiempo de espera se muestran generalmente como flechas horizontales y inclinadas. 
Los mensajes aparecen en secuencia a medida que el tiempo avanza por el diagrama. La distancia vertical entre puntos en el tiempo indica sólo la secuencia en el tiempo y no cualquier escala de tiempo 