#Containers #Docker #Contexto 

## 1. El Problema a Resolver: ¿Por qué necesitamos Docker?

Para entender los beneficios que aporta Docker hoy en día, es fundamental plantearnos la siguiente pregunta: *¿Qué necesitamos para desplegar una aplicación tradicional, por ejemplo, en NodeJS?*

En una máquina limpia, el proceso suele ser:
* Descargar e instalar el entorno de ejecución (NodeJS).
* Descargar el código fuente de la aplicación.
* Instalar las dependencias específicas de la app (ej. mediante `npm install`).
* Ejecutar la aplicación

Aunque simplificado, esto funciona para una app sencilla. Sin embargo, el problema, a menudo conocido como la **"matriz del infierno" (Matrix of Hell)**, surge cuando escalamos:

Si movemos esto a otros lenguajes de programación (Python, Java, Ruby, Go), el proceso cambia drásticamente. Cada lenguaje requiere configuraciones, variables de entorno y gestores de paquetes distintos antes de poder siquiera empezar a codificar. Además, si necesitamos trabajar con múltiples versiones de Node en la misma máquina, o hacer convivir aplicaciones en Java y Python que requieren distintas dependencias a nivel de sistema operativo, se generan conflictos. ¿Cómo logramos que todo esto funcione de forma íntegra y sin pisarse entre sí?


![[Introducción a los Contenedores-1785382915252.webp]]

## 2. ¿Cómo nos ayudan los Contenedores?

Los contenedores resuelven este problema porque **encapsulan todas las dependencias, bibliotecas y configuraciones** necesarias para correr una aplicación de cualquier lenguaje en un solo paquete. Al hacerlo, exponen una interfaz externa limpia, estable y unificada.

Independientemente de si la aplicación está escrita en Java, Python o Go, desde afuera todos los contenedores se operan exactamente igual. Esto permite que el software se ejecute mucho mejor y de manera predecible en cualquier entorno.

### Beneficios de usar Contenedores (Explicación Ampliada)
* **Simplificación del Setup:** Evita el clásico problema de "funciona en mi máquina". Al encapsular el entorno, cualquier desarrollador puede levantar el proyecto con un solo comando sin instalar dependencias globales complejas.
* **Portabilidad:** Al ser independientes del sistema operativo base (siempre que el kernel sea compatible), un contenedor creado en un portátil con Arch Linux funcionará de forma idéntica en un servidor de producción en la nube.
* **Entornos Consistentes:** Los entornos de Desarrollo, Pruebas (Testing) y Producción son exactamente los mismos, reduciendo drásticamente los errores de despliegue.
* **Aislamiento (Isolation):** Aunque comparten el mismo núcleo (kernel) del sistema operativo, los contenedores están aislados a nivel de procesos y recursos (usando tecnologías nativas de Linux como *namespaces* y *cgroups*). Un fallo en un contenedor no tumba a los demás.
* **Eficiencia y Contenedores Ligeros (Lean):** Al no requerir arrancar un sistema operativo completo para cada aplicación, inician en milisegundos y pesan megabytes, en lugar de gigabytes.
* **Mejor Control de Recursos:** Se puede limitar exactamente cuánta memoria RAM o CPU máxima puede consumir cada contenedor de manera independiente.
* **Aplicaciones Fácilmente Escalables:** Facilitan la arquitectura de microservicios. Si el tráfico aumenta, puedes simplemente desplegar 10 réplicas más del mismo contenedor de forma casi instantánea.


![[Introducción a los Contenedores-1785383310158.webp]]

## 3. Contenedores vs. Máquinas Virtuales (VMs)

Para entender los contenedores, es útil compararlos con la virtualización tradicional.

### Máquinas Virtuales (VMs)
En la virtualización tradicional, comenzamos con una capa de hardware (Hardware Layer) que provee CPU, RAM, etc., ya sea un PC local o un Data Center. Sobre esto corre un Sistema Operativo Base (Host OS) y un **Hipervisor (Hypervisor)**.

> **Ejemplo en el mundo Cloud:** Si despliegas una instancia EC2 en la nube de AWS, estás alquilando una Máquina Virtual. El hardware físico en el centro de datos de AWS ejecuta un hipervisor (como AWS Nitro System), el cual permite dividir los recursos físicos para correr tu instancia. Tu instancia EC2 tendrá su propio *Guest Operating System* (ej. Amazon Linux, Ubuntu o Windows) instalado desde cero.

El hipervisor es la pieza clave que traduce las instrucciones de las máquinas virtuales al hardware real. El detalle más pesado aquí es que cada VM requiere su propio **Guest OS** entero, lo que consume una gran cantidad de recursos solo para mantener el sistema encendido antes de correr la aplicación.

![[Introducción a los Contenedores-1785383420805.webp]]

### Contenedores
El inicio es similar: se necesita hardware (CPU, memoria) y un sistema operativo base (Host OS, típicamente Linux). El gran cambio es que, en lugar de un hipervisor, tenemos un **Container Engine** (como Docker Engine).

Este motor es responsable de gestionar los contenedores. En lugar de instalar un sistema operativo invitado completo, se empaqueta solo el código y sus dependencias. Al compartir el núcleo (kernel) del Host OS, se logran contenedores extremadamente rápidos y ligeros. Se sacrifica un nivel muy profundo de aislamiento (ya que el kernel es compartido, a diferencia de las VMs donde está completamente separado), pero se gana masivamente en densidad y eficiencia.

![[Introducción a los Contenedores-1785384494977.webp]]

### Tabla Comparativa

![[Introducción a los Contenedores-1785384658811.webp]]


## 4. Arquitectura y Componentes de Docker

El ecosistema de Docker se basa en una arquitectura Cliente-Servidor. Sus principales componentes son:

* **Docker Client:** Es la forma en la que interactuamos con Docker. Principalmente utilizamos la interfaz de línea de comandos (`Docker CLI`). El cliente no ejecuta los contenedores directamente, sino que hace llamadas a la API (API Calls) para comunicarse con el Host y darle instrucciones.

![[Introducción a los Contenedores-1785384843117.webp]]

* **Docker Host:** Es el entorno donde ocurre toda la acción pesada. 
    * **REST API:** Recibe las peticiones del Docker Client.
    * **Docker Daemon (`dockerd`):** Es el núcleo (core) del Docker Engine. Escucha las peticiones de la API y gestiona los objetos de Docker (Imágenes, Contenedores, Redes, Volúmenes).
    * **Image Cache:** Almacenamiento local donde se guardan las imágenes descargadas o creadas localmente.
    * **Contenedores:** Las instancias en ejecución (o detenidas) generadas a partir de las imágenes.

![[Introducción a los Contenedores-1785385017140.webp]]

* **Images Registry:** Es el repositorio donde se almacenan y distribuyen las imágenes. El más común es **Docker Hub**, un registro público y privado por defecto. Sin embargo, en entornos empresariales, es común usar alternativas como AWS Elastic Container Registry (ECR).

![[Introducción a los Contenedores-1785385257523.webp]]


## 5. Ejemplos de Flujos de Trabajo en Docker

### Flujo 1: Correr un Contenedor (`docker run`)
1. Se ingresa el comando `docker run <imagen>` en el CLI.
2. El CLI envía la petición (request) al Docker Host a través de la REST API.
3. El Docker Host verifica si la imagen solicitada está presente en el Image Cache local.
4. Si no está, el Docker Daemon se conecta al Image Registry (requiere internet) y la descarga (Pull).
5. Una vez que la imagen está en el host, inicializa y arranca un nuevo contenedor basado en ella.

### Flujo 2: Construir una Imagen (`docker build`)
1. Se ejecuta el comando `docker build` en el CLI.
2. El CLI manda el request a la REST API del Host, adjuntando el archivo de configuración (`Dockerfile`) y el "Contexto" (los archivos locales necesarios).
3. El Docker Host procesa paso a paso el Dockerfile. Es muy probable que primero deba ir al Image Registry para descargar la imagen base mencionada en la instrucción `FROM`.
4. El Docker Host compila las capas, le asigna una etiqueta (tag) a la nueva imagen y la almacena de forma local en el caché.

### Flujo 3: Subir una Imagen (`docker push`)
1. Se ejecuta el comando `docker push` en el CLI.
2. Se le instruye al Docker Daemon que suba la imagen construida al Image Registry.
3. **Importante:** Para hacer un *push*, siempre se debe estar autenticado en el Registry, independientemente de si el repositorio de destino es público o privado.