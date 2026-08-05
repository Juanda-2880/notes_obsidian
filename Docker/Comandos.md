#Docker #Comandos

### 1. `docker run <nombre_de_la_imagen>`

**Explicación:** Crea y ejecuta un nuevo contenedor a partir de una imagen especificada (por ejemplo, `ubuntu`, `nginx`, `alpine`). _Nota:_ Si la imagen no está en tu computadora localmente, Docker la descargará automáticamente desde Docker Hub antes de iniciar el contenedor.

### 2. `docker ps`

**Explicación:** Muestra una lista de los contenedores que están **actualmente en ejecución**. _Tip adicional:_ Si quieres ver todos los contenedores (incluso los que están detenidos), debes usar el comando `docker ps -a`.

### 3. Ejecutar un contenedor en segundo plano (Detached mode)


```
❯ docker run -d nginx
5683cf723d82abe90331533492fb86230336af364886b55152c7e8911bc2fa71
```

**Explicación:** El flag `-d` (detached) le indica a Docker que ejecute el contenedor en segundo plano. Esto permite que el contenedor siga funcionando sin bloquear tu terminal. La larga cadena alfanumérica que devuelve (`5683cf...`) es el **ID único del contenedor** que acaba de crearse.

### 4. Forzar la detención de un contenedor


```
❯ docker kill unruffled_shtern
unruffled_shtern
```

**Explicación:** Apaga de forma forzosa e inmediata el contenedor que lleva el nombre (o ID) `unruffled_shtern`. Al finalizar con éxito, la terminal te devuelve el nombre del contenedor afectado. _(Nota: Docker asigna nombres aleatorios divertidos como este si no le asignas uno tú mismo)._

### 5. Ejecutar un contenedor con puertos y nombre personalizado


```
❯ docker run -d -p 8080:80 --name web-server nginx
3278b1147bed2f39201b6468f8455d22af9eec98f9c4103fa59ea071b43465b0
```

**Explicación:** Este es un comando muy completo. Hace varias cosas a la vez:

- `-d`: Ejecuta el contenedor en segundo plano.
    
- `-p 8080:80`: Mapea (conecta) el puerto **8080** de tu máquina física (host) al puerto **80** del contenedor. Así podrás ver el servidor web entrando a `localhost:8080` en tu navegador.
    
- `--name web-server`: Le asigna el nombre personalizado "web-server" al contenedor, para que sea más fácil de identificar y gestionar (en lugar de usar un nombre aleatorio o el ID).
    
- `nginx`: Es la imagen que se va a utilizar.
    

### 6. Detener un contenedor de forma segura



```
❯ docker stop web-server
```

**Explicación:** Detiene el contenedor llamado `web-server` de manera controlada y segura.


### 7. `docker images`

_(También se puede usar `docker image ls`)_ **Explicación:** Muestra una lista de todas las imágenes que ya están descargadas de manera local en tu computadora. Te permite ver detalles como el nombre del repositorio, la etiqueta (versión), el ID de la imagen y cuánto espacio ocupan en tu disco duro.

### 8. `docker pull <nombre_de_la_imagen>`


```
❯ docker pull ubuntu
```

**Explicación:** Descarga una imagen desde un registro (por defecto, Docker Hub) a tu computadora local **sin ejecutarla**. _Casos de uso:_ Es muy útil si quieres actualizar una imagen a su última versión o si necesitas descargar imágenes pesadas con anticipación para no tener que esperar al momento de hacer un `docker run`.

### 9. Filtrar y simplificar la lista de contenedores

- **`docker ps --filter name=<nombre>`:** Filtra la lista de contenedores en ejecución para mostrar _solo_ aquel(los) cuyo nombre coincida con lo que escribas. Muy útil cuando tienes un servidor con decenas de contenedores y buscas uno en particular.
    
- **`docker ps -q`:** El flag `-q` (quiet) hace que la terminal devuelva **únicamente los IDs** numéricos de los contenedores en ejecución, omitiendo toda la demás información (nombres, puertos, estado, etc.).
    

### 10. Ejecución masiva de comandos (Sustitución de comandos)



```
❯ docker stop $(docker ps -q)
```

**Explicación:** Este es un "truco" de la terminal muy poderoso. Toma el resultado de `docker ps -q` (una lista de puros IDs) y se los pasa al comando `docker stop`. El resultado: **Detiene todos los contenedores en ejecución al mismo tiempo.**

_Tip avanzado:_ Como bien notaste, esto se puede combinar con otros comandos. Para borrar (remove) todos los contenedores de tu computadora, necesitas incluir también los que están detenidos usando el flag `-a` (all): `docker rm $(docker ps -aq)` -> Borra absolutamente todos los contenedores.

### 11. Eliminar una imagen local



```
❯ docker rmi <ID_o_Nombre>
# O también:
❯ docker image rm <ID_o_Nombre>
```

**Explicación:** Elimina una imagen de tu almacenamiento local para liberar espacio. _Importante:_ Docker no te dejará borrar una imagen si hay algún contenedor (incluso si está detenido) que haya sido creado a partir de ella. Primero debes borrar el contenedor con `docker rm` y luego la imagen con `docker rmi`.

### 12. Ver los registros (Logs)

- **`docker logs <ID_o_Nombre>`:** Imprime en la consola todo el texto que la aplicación dentro del contenedor ha escupido desde que se inició. Es fundamental para saber por qué un contenedor falló o se cerró inesperadamente (especialmente útil si lo corriste con `-d`).
    
- **`docker logs -f <ID_o_Nombre>`:** El flag `-f` (follow) hace que te quedes "conectado" a los logs. En lugar de solo imprimir el historial y salir, la terminal se queda abierta mostrando en tiempo real cualquier mensaje nuevo que genere el contenedor.
    

### 13. Entrar a la consola de un contenedor en ejecución


```
❯ docker exec -it <ID_o_Nombre> /bin/bash
```

**Explicación:** Permite ejecutar un comando dentro de un contenedor _que ya está encendido_.

- `-i` (interactive) y `-t` (tty): Juntos (`-it`) abren una sesión de terminal interactiva.
    
- `/bin/bash`: Es el programa que quieres abrir adentro (la consola bash). Si es una imagen muy ligera (como Alpine), es posible que bash no exista y debas usar `/bin/sh`. Básicamente, este comando es como "hacer SSH" o meterte dentro del contenedor para navegar por sus carpetas y ver sus archivos.
    

### 14. Construir tus propias imágenes (Docker Build)


```
❯ docker build -t myapp:v1 .
```

**Explicación:** Lee un archivo llamado `Dockerfile` para crear una imagen nueva desde cero.

- `docker build`: El comando principal para construir.
    
- `-t myapp:v1` (tag): Le asigna un nombre (`myapp`) y una etiqueta/versión (`v1`) a la imagen que vas a crear. Si omites los dos puntos y la versión, Docker le pondrá por defecto la versión `latest`.
    
- `.` (el punto final): Es fundamental. Le indica a Docker que el "contexto" (donde debe buscar el `Dockerfile` y los archivos de tu proyecto) es la **carpeta actual** en la que estás ubicado en la terminal.
    

### 15. Solicitar ayuda (`--help`)


```
❯ docker --help
❯ docker build --help
```

**Explicación:** Muestra el manual de uso. Si escribes `docker --help`, verás la lista general de todos los comandos disponibles. Lo más útil es combinarlo con un comando específico (como `docker run --help` o `docker rm --help`) para ver todas las opciones, flags y variables que ese comando en particular acepta.
