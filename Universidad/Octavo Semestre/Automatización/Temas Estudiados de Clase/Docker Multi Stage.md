#Universidad #Docker #Auto #PlatsII #Contexto #Images 

### El Problema: El peso y la seguridad de las imágenes tradicionales

El uso de contenedores (con herramientas como Docker) es el estándar actual para el desarrollo nativo en la nube. Teóricamente, los contenedores ofrecen portabilidad, eficiencia y un bajo consumo de recursos. Sin embargo, estos beneficios se pierden rápidamente si no construimos las imágenes correctamente.

El error más común al empezar con Docker es crear **imágenes monolíticas enormes**. Esto ocurre porque al compilar una aplicación (por ejemplo, en Node.js, Java o Go), se instalan múltiples dependencias, herramientas de desarrollo y librerías que solo son necesarias para compilar el código, pero **no** para ejecutarlo en producción.

Tener imágenes enormes genera tres problemas críticos:

1. **Pérdida de agilidad:** Tardan mucho más en construirse, descargarse (hacer _pull_) y desplegarse.
    
2. **Mayores costos:** Incrementan los costos de almacenamiento en la nube (Container Registries) y el consumo de red.
    
3. **Mayor superficie de ataque:** Al incluir herramientas de desarrollo (compiladores, consolas, gestores de paquetes), se abren brechas de seguridad que un atacante podría explotar.
    

### La Solución: Docker Multistage (Construcción en múltiples etapas)

Para resolver esto, Docker introdujo las **construcciones multietapa (_Multistage Builds_)**. Esta técnica consiste en dividir el `Dockerfile` en varias fases independientes.

La magia de este enfoque radica en que puedes usar una imagen base pesada y llena de herramientas para compilar tu aplicación en la "Etapa 1", y luego, en la "Etapa 2", usar una imagen base minimalista, **copiando únicamente los binarios o archivos finales generados en la etapa anterior**. Todo lo demás (el código fuente y las dependencias de desarrollo) se desecha.

**¿Cómo funciona técnicamente?** Se utilizan múltiples instrucciones `FROM` dentro de un mismo `Dockerfile`. Cada `FROM` inicia una nueva etapa. Para conectar las etapas, se nombran usando la palabra clave `AS` (ej. `FROM node:alpine AS builder`), lo que permite referenciarla más adelante para copiar solo lo que necesitas.

### Ejemplo Práctico: Aplicación web con React

Para ilustrar el poder del _Multistage_, analicemos el despliegue de un frontend hecho en React que se conecta a una arquitectura de microservicios.

#### Enfoque Estándar (Incorrecto)

Si hacemos una construcción tradicional, incluso usando una imagen base ligera como _Alpine Linux_, copiaríamos todo el código fuente y descargaríamos la inmensa carpeta `node_modules`. El resultado final sería una imagen de unos **502 MB**, llena de archivos que en producción no sirven para nada.

#### Enfoque Multietapa (Optimizado)

Dividimos el `Dockerfile` en dos etapas:

**Etapa 1: Compilación (_Builder_)**

1. Usamos una imagen de Node.js.
    
2. Nombramos la etapa con `AS react-build`.
    
3. Instalamos las dependencias y ejecutamos el comando de compilación (`npm run build`).
    
4. Esto genera una carpeta con archivos estáticos (HTML, CSS, JS optimizados) listos para producción.
    

**Etapa 2: Producción (_Production_)**

1. Iniciamos una **nueva** etapa usando una imagen de Nginx (un servidor web muy ligero) basada en Alpine.
    
2. Configuramos el servidor copiando un archivo `nginx.conf` personalizado (necesario para que Nginx maneje correctamente las rutas de _React Router_).
    
3. **El paso clave:** Usamos la instrucción `COPY --from=react-build` para extraer **solo** la carpeta con los archivos estáticos generados en la Etapa 1 y la ponemos en la carpeta pública de Nginx (`/usr/share/nginx/html`).
    

#### Estructura Conceptual del Dockerfile:


```Dockerfile
# --- ETAPA 1: Compilación ---
FROM node:18-alpine AS react-build
WORKDIR /app
COPY package.json ./
RUN npm install
COPY . ./
RUN npm run build
# Hasta aquí, la imagen es pesada, pero esta etapa se descartará.

# --- ETAPA 2: Producción ---
FROM nginx:alpine
# Copiamos la configuración del servidor web
COPY nginx.conf /etc/nginx/nginx.conf
# Copiamos SOLO los estáticos generados en la Etapa 1
COPY --from=react-build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### El Resultado Final

Al aplicar este modelo, la imagen final resultante pasa de pesar **502 MB** a tan solo **28 MB**.

Solo se conserva el servidor web Nginx y los archivos estáticos estrictamente necesarios. Se eliminó todo rastro de Node.js, `node_modules` y el código fuente original, logrando un contenedor altamente seguro, económico de almacenar y ultrarrápido de desplegar. Este principio es aplicable a cualquier lenguaje o _stack_ tecnológico que requiera compilación previa.