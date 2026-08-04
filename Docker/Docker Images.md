
### ¿Qué son las Imágenes de Docker?

Las imágenes de Docker se pueden entender como el **ADN de nuestros contenedores**.

Al principio, todos los contenedores instanciados a partir de una misma imagen tendrán el mismo peso y contenido base. Sin embargo, si ingresamos a un contenedor en ejecución y modificamos o creamos archivos, ese contenedor en particular cambiará su tamaño. Esto sucede porque al crear un contenedor, Docker añade una **capa de lectura y escritura (Read/Write layer)** sobre la imagen original.

Las imágenes son **autónomas (self-contained)**; funcionan como una **plantilla de solo lectura (read-only template)** que encapsula absolutamente todo lo necesario para ejecutar una aplicación de forma aislada, sin depender del entorno del servidor anfitrión.

Son, en esencia, _snapshots_ (instantáneas) de la aplicación y su entorno de ejecución completo, congeladas en el tiempo y listas para "revivir" como un contenedor en cualquier momento y en cualquier lugar.

### Anatomía de una Imagen (Capas / Layers)

Como se ilustra en el archivo "Screenshot_20260804_161405.png", una imagen de Docker no es un solo bloque masivo, sino que se construye a través de una arquitectura de múltiples capas apiladas:

- **Base Image (Capa Base):** Es el cimiento de la imagen. Por lo general, es una distribución mínima de Linux diseñada para ser ligera (como Alpine Linux, que pesa apenas unos pocos megabytes) o un sistema operativo más completo (como Ubuntu).
    
- **Runtime Deps (Entorno de Ejecución):** Es el software base o motor específico requerido para que tu código funcione. Ejemplos de esto son el entorno de Node.js, la máquina virtual de Java (JRE) o el intérprete de Python.
    
- **Dependencies (Librerías y Dependencias):** Todo el código de terceros y módulos externos de los que depende la aplicación para funcionar (por ejemplo, los paquetes instalados mediante `npm install` o `pip install`).
    
- **Application Code (Código de la Aplicación):** El propio código fuente que has escrito o los binarios ya compilados de tu proyecto.
    
- **Configuration (Configuración):** Opciones, variables de entorno, puertos expuestos (`EXPOSE`) y directivas de inicio (`CMD` o `ENTRYPOINT`) que definen cómo debe comportarse la aplicación y su entorno al arrancar el contenedor.
    

### ¿De dónde provienen las Imágenes?

Las imágenes pueden obtenerse o generarse desde diferentes fuentes:

1. **Docker Hub:** El registro público oficial por defecto, donde encuentras imágenes oficiales de bases de datos, lenguajes y herramientas.
    
2. **Private Registries (Registros Privados):** En entornos empresariales y de nube, es común almacenar las imágenes de forma segura en servicios privados como AWS Elastic Container Registry (ECR), Google Artifact Registry o Azure Container Registry.
    
3. **Construcción Propia (Custom Build):** Cuando necesitas empaquetar tu propia aplicación, creas una imagen personalizada utilizando un archivo de instrucciones llamado **`Dockerfile`** y ejecutando el comando `docker build`.
    

### Información de Valor Extra (Conceptos Avanzados)

Para llevar tus apuntes al siguiente nivel, aquí tienes algunos conceptos técnicos fundamentales:

- **Union File System (UnionFS):** Es la tecnología mágica detrás de las capas de Docker. UnionFS permite que múltiples sistemas de archivos (las capas de solo lectura de tu imagen) se superpongan de forma transparente, pareciendo un único sistema de archivos desde la perspectiva del contenedor.
    
- **Reutilización y Caché (Layer Caching):** Si tienes varias imágenes diferentes que usan la misma capa base (ej. `ubuntu:22.04`), Docker solo descarga y almacena esa capa **una vez** en tu disco. Al hacer el _build_, Docker usa un sistema de caché; si no has cambiado una línea en tu `Dockerfile`, usará la capa previamente construida, ahorrando muchísimo tiempo en las integraciones continuas (CI/CD).
    
- **Inmutabilidad:** Las capas de una imagen nunca cambian. Si actualizas tu código de la aplicación y haces un nuevo `docker build`, Docker crea una nueva capa para el código, pero reutiliza todas las capas subyacentes intactas.
    
- **Seguridad:** Empezar siempre con imágenes base minimalistas (como Alpine) o versiones _distroless_ no solo reduce el tamaño de descarga y los costos de almacenamiento, sino que disminuye drásticamente la superficie de ataque, un principio vital en la ciberseguridad aplicada a contenedores.


![[Docker Images-1785878051823.webp]]



#### Container Registries

Estos registries se dan desde el momento en el que se desean almacenar y manejar las imagenes de docker en un entorno un poco mas interno o tambien publico para que otra gente las utilice y así, por lo que esta en local pero no es la mejor solucion 

Los beneficios de Container Registries es 
- Colaboración porque permite compartir las iamgenes con los compañeros de equipo, clientes y en si una comunidad
- Versioning se puede hacer el track de las diferentes versiones de las imagenes facilitando el rollback o las actualizaciones este se usa con tags y difeirentes versiones
-  Seguridad, los private registries da un entorno mas seguro con images sensibles
- Automate te image build and deployment as part of a CI CD Pipeline 

Los tipos de los contenedores registries estan los 

- 
Public Registries son los que estan abiertos a todo el mundo como podria ser Docker Hub

Private Registries, es usasdo para una propiedad fuerte o images sensibles y ofrecen accesso de control granular ya sea el de AWS, GCP, Azure, Quay, Harbor etc etc


![[Docker Images-1785879032088.webp]]