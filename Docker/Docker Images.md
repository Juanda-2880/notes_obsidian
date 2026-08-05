#Docker #Images 
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


### ¿Qué son los Container Registries?

Un **Container Registry** (Registro de Contenedores) es un repositorio centralizado diseñado específicamente para almacenar, gestionar y distribuir imágenes de Docker. Aunque puedes almacenar tus imágenes localmente en tu propia máquina durante el desarrollo, esta no es una solución escalable ni práctica para entornos de producción. Los registros resuelven este problema al proporcionar una ubicación en red (interna o pública) para subir (_push_) y descargar (_pull_) las imágenes de forma eficiente.

### Beneficios de usar un Container Registry

- **Colaboración:** Permite compartir imágenes fácilmente con compañeros de equipo, clientes o la comunidad global de desarrolladores.
    
- **Control de Versiones (Versioning):** Facilita el seguimiento de las diferentes iteraciones de una imagen mediante el uso de etiquetas (_tags_), como `v1.0.3` o `latest`. Esto es vital para realizar actualizaciones ordenadas o revertir cambios (_rollback_) si ocurre un fallo.
    
- **Seguridad:** Los registros privados ofrecen un entorno protegido con controles de acceso estrictos, ideal para almacenar imágenes que contienen código propietario o información sensible.
    
- **Automatización (CI/CD):** Son una pieza fundamental en las canalizaciones de Integración y Despliegue Continuo (CI/CD), permitiendo automatizar la construcción de la imagen y su posterior despliegue a los servidores.
    

### Tipos de Registros y Modelos de Alojamiento

Basándonos en la tabla del archivo "Screenshot_20260804_163021.png", a la hora de elegir un Container Registry se deben evaluar varias dimensiones:

**1. Hosting Type (Tipo de Alojamiento)**

- **Public (Públicos):** Abiertos a todo el mundo (ej. Docker Hub). Son ideales para proyectos de código abierto, colaboración comunitaria y distribución pública de software.
    
- **Private (Privados):** Esenciales para proteger datos sensibles, código propietario y proyectos internos de una organización.
    
- **Self-Hosted (Autoalojados):** Instalados en tu propia infraestructura. Ofrecen el máximo control y flexibilidad, pero requieren que tu equipo gestione el mantenimiento de los servidores (ej. Harbor).
    
- **Cloud-Hosted (Alojados en la Nube):** Gestionados por un proveedor (ej. AWS ECR, GCP Artifact Registry, Azure Container Registry). Son muy convenientes, altamente escalables y se integran de forma nativa con otros servicios del mismo proveedor.
    

**2. Security Features (Características de Seguridad)**

- **Básicas:** Autenticación simple, suficiente para proyectos públicos o poco sensibles.
    
- **Avanzadas:** Incluyen Control de Acceso Basado en Roles (RBAC), escaneo de vulnerabilidades y firma de imágenes (_Image Signing_). Son cruciales para cumplir con normativas y garantizar despliegues seguros.
    

**3. Integrations (Integraciones)**

- **Limitadas:** Adecuadas para proyectos independientes o flujos de trabajo sencillos.
    
- **Extensivas:** Ofrecen APIs, Webhooks y soporte nativo para herramientas CI/CD, permitiendo crear flujos de trabajo complejos y totalmente automatizados.
    

**4. Cost Model (Modelo de Costos)**

- **Free Tier:** Capas gratuitas con limitaciones, excelentes para experimentación.
    
- **Usage-Based (Basado en uso):** Pagas por lo que consumes (almacenamiento y ancho de banda). Es flexible pero puede escalar rápidamente.
    
- **Fixed Subscription (Suscripción fija):** Costos predecibles, aunque puede no ser rentable si el uso es bajo.
    
- **Open-Source (Código abierto):** El software es gratuito, pero requiere inversión en la infraestructura subyacente y mantenimiento.
    

### Información de Valor Extra (Enfoque en Cloud y Ciberseguridad)

Para darle un enfoque más robusto y profesional a tus apuntes:

- **Image Scanning en tiempo real:** En el ámbito de la ciberseguridad aplicada a contenedores, los registros modernos (como AWS ECR o Harbor) incluyen motores de escaneo estático. Cada vez que haces un _push_ de una imagen, el registro la escanea contra bases de datos de vulnerabilidades conocidas (CVEs). Puedes configurar políticas para que, si una imagen tiene vulnerabilidades "Críticas", el pipeline de despliegue se bloquee automáticamente.
    
- **Ciclo de vida y Retención (Lifecycle Policies):** En entornos Cloud, el almacenamiento cuesta dinero. Los registros permiten configurar reglas automáticas, por ejemplo: "Eliminar todas las imágenes que no tengan el tag _release_ y que tengan más de 30 días de antigüedad", optimizando así los costos de infraestructura.
    
- **Firma de Imágenes (Image Signing):** Herramientas como Docker Content Trust (DCT) o Sigstore permiten firmar criptográficamente las imágenes en el registro. Esto garantiza que la imagen que se despliega en producción es exactamente la misma que construyó el equipo de desarrollo, evitando ataques de manipulación en el medio (_man-in-the-middle_).


![[Docker Images-1785879032088.webp]]