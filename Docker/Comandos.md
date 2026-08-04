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


----

