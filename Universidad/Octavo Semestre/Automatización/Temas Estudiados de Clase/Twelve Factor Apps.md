#Universidad #Auto #PlatsII 

### ¿Qué es una "Twelve-Factor App"?

En la era moderna, el software se entrega comúnmente como un servicio (SaaS - _Software as a Service_). La metodología de los 12 factores es un conjunto de reglas de arquitectura y buenas prácticas diseñadas para construir aplicaciones web que sean:

- **Fáciles de configurar:** Usan formatos declarativos para automatizar la configuración, minimizando el tiempo para que un nuevo desarrollador se una al proyecto.
    
- **Portables:** Tienen un contrato limpio con el sistema operativo subyacente (funcionan igual en cualquier máquina).
    
- **Listas para la Nube (Cloud-Ready):** Prescinden de la administración tradicional de servidores, haciéndolas perfectas para plataformas modernas.
    
- **Ágiles:** Minimizan la diferencia entre el entorno de Desarrollo y Producción, habilitando el despliegue continuo (CI/CD).
    
- **Altamente Escalables:** Pueden escalar sin hacer cambios significativos en la arquitectura o en las herramientas.
    

Esta metodología es independiente del lenguaje de programación y de los servicios de respaldo (bases de datos, cachés, colas) que se utilicen.

### Los 12 Factores Explicados

#### 1. Código Base (_Codebase_)

**"Un solo código base rastreado en control de versiones, múltiples despliegues."** Nunca debes tener repositorios separados para "Desarrollo" y "Producción". Existe un solo repositorio en Git para la aplicación. Lo que cambia es el _despliegue_ (instancias en ejecución de ese código). Si hay múltiples repositorios, no es una aplicación, es un sistema distribuido.

#### 2. Dependencias (_Dependencies_)

**"Declara y aísla explícitamente las dependencias."** Una aplicación nunca debe asumir que ciertas herramientas o librerías del sistema ya están instaladas (como `curl` o un compilador específico). Toda dependencia debe declararse en un manifiesto (ej. `package.json` en Node, `requirements.txt` en Python).

- _Práctica DevOps:_ **Docker** es la herramienta definitiva para esto, ya que aísla por completo el entorno y asegura que todas las dependencias viajen con la aplicación.
    

#### 3. Configuración (_Config_)

**"Guarda la configuración en el entorno (Variables de Entorno)."** Cualquier cosa que varíe entre despliegues (credenciales de base de datos, tokens de APIs, URLs externas) debe estar fuera del código fuente. Nunca se _hardcodean_ contraseñas. Se deben inyectar mediante variables de entorno (ej. `.env`). Si puedes hacer tu repositorio público mañana sin comprometer credenciales, estás cumpliendo este factor.

#### 4. Servicios de Respaldo (_Backing Services_)

**"Trata los servicios de respaldo como recursos adjuntos (conectables)."** La aplicación no debe hacer distinción entre un servicio local y uno de terceros. Por ejemplo, cambiar de una base de datos MySQL local a un servicio gestionado como Amazon RDS debería requerir únicamente modificar la URL de conexión en las variables de entorno, sin tocar una sola línea de código.

#### 5. Construir, Liberar, Ejecutar (_Build, release, run_)

**"Separa estrictamente las etapas de construcción y ejecución."**

- **Build:** Convierte el código en un paquete ejecutable (compilación).
    
- **Release:** Combina el paquete con la configuración del entorno.
    
- **Run:** Ejecuta la aplicación en la nube.
    
- _Práctica DevOps:_ Esto se alinea perfectamente con la creación de pipelines de Integración Continua (CI) y el uso de **Docker Multistage**, donde separas la compilación de la imagen final de ejecución.
    

#### 6. Procesos (_Processes_)

**"Ejecuta la aplicación como uno o más procesos sin estado (_Stateless_)."** El contenedor o proceso de la app no debe almacenar datos persistentes en su disco local ni en memoria (como sesiones de usuario o archivos subidos). Si el contenedor se apaga o se reinicia, esos datos se pierden. Todo dato que deba persistir debe almacenarse en un servicio de respaldo con estado (una base de datos o un caché como Redis).

#### 7. Asignación de Puertos (_Port binding_)

**"Exporta servicios mediante la asignación de puertos."** La aplicación debe ser completamente autocontenida. No debe depender de que un servidor web global (como un Tomcat o Apache en el servidor físico) la inyecte. La aplicación debe levantar su propio servidor (ej. Express en Node) y exponerse a sí misma escuchando directamente en un puerto (ej. `8080`).

#### 8. Concurrencia (_Concurrency_)

**"Escala horizontalmente a través del modelo de procesos."** En lugar de hacer que un solo proceso sea gigantesco para manejar más tráfico (escalado vertical), la aplicación debe estar diseñada para poder clonarse a sí misma. Así, para manejar más carga, simplemente levantas más instancias o contenedores paralelos (escalado horizontal).

#### 9. Desechabilidad (_Disposability_)

**"Maximiza la robustez con inicios rápidos y apagados elegantes (_Graceful shutdown_)."** Los procesos deben iniciar instantáneamente para reaccionar a picos de tráfico. Al mismo tiempo, deben poder apagarse de manera segura: si reciben una señal de apagado, deben terminar de procesar la petición actual, cerrar las conexiones a la base de datos limpiamente y morir sin corromper datos.

#### 10. Paridad entre Desarrollo y Producción (_Dev/prod parity_)

**"Mantén los entornos de desarrollo, pruebas y producción lo más similares posible."** Evita excusas como "en mi máquina sí funcionaba". Si usas PostgreSQL en producción, no uses SQLite para pruebas locales por "comodidad". Contenedorizar los entornos garantiza que el desarrollador trabaje con las mismas versiones de software que correrán en la nube.

#### 11. Bitácoras (_Logs_)

**"Trata los logs como flujos de eventos."** La aplicación no debe preocuparse por cómo enrutar o almacenar sus propios logs (no debe escribir en un archivo `app.log` dentro de su disco). Simplemente debe imprimir sus eventos en la salida estándar (`stdout`/`stderr`). En un entorno moderno, herramientas de infraestructura capturan ese flujo y lo envían a sistemas centralizados de análisis (como CloudWatch o ELK Stack).

#### 12. Procesos Administrativos (_Admin processes_)

**"Ejecuta tareas de administración como procesos de un solo uso."** Tareas como migrar una base de datos o ejecutar scripts de limpieza no deben hacerse manualmente desde consolas externas. Deben ejecutarse como procesos efímeros dentro del mismo entorno y usando el mismo código base y configuración que la aplicación principal.