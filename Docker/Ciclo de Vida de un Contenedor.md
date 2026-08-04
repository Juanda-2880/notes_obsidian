#Docker #Containers 


![[Corriendo Contenedores con Docker-1785816618854.webp]]

**1. Creación y Ejecución:**

- Al utilizar el comando `docker run <image>`, si no se especifica un _tag_ (etiqueta), Docker descargará y utilizará por defecto la versión `latest`.
    
- El comando `docker run` es, en realidad, la combinación de dos acciones que también pueden ejecutarse de manera independiente: `docker create` (que prepara la capa del contenedor) y `docker start` (que lo arranca).
    

**2. Estado en Ejecución (Container Running):**

- Una vez que el contenedor está corriendo, es posible interactuar con él o monitorearlo utilizando comandos como `docker logs` (para leer los registros), `docker inspect` (para ver sus detalles técnicos en profundidad) o `docker exec` (para entrar a la terminal o ejecutar procesos adicionales dentro del contenedor activo).
    

**3. Transiciones desde el Estado de Ejecución:** Teniendo un contenedor en estado de _Running_, tenemos múltiples opciones (asumiendo que no hay una política de reinicio automático en acción):

- **Pausar (`docker pause <cId>`):** No detiene el contenedor por completo. Suspende los procesos y **mantiene el contenido en la memoria**. Para reanudarlo exactamente donde quedó, se utiliza `docker unpause <cId>`.
    
- **Detener (`docker stop <cId>`):** Detiene el contenedor de forma elegante (_gracefully_) y **limpia el contenido en memoria**.
    
- **Forzar detención (`docker kill <cId>`):** Fuerza la detención inmediata del contenedor mediante la señal del sistema SIGKILL (por defecto).
    
- **Salida Natural (`exit 0`):** Indica que el proceso interno del contenedor finalizó su tarea sin ningún error.
    
- **Salida con Errores (`exit <non-zero>`):** Un código de salida distinto de cero significa que el contenedor falló o encontró un error. De esta métrica es de donde se alimentan las **políticas de reinicio (restart policies)** de Docker para saber si deben volver a levantar el servicio.
    

**4. Estado Detenido (Container Stopped):**

- Cuando un contenedor llega a este estado, la instancia sigue existiendo en el disco local.
    
- Se puede visualizar en la lista de contenedores inactivos usando `docker ps -a`.
    
- Aún es posible consultarlo usando `docker logs` o `docker inspect`.
    
- **Reactivación:** Si queremos volver a arrancarlo, basta con ejecutar `docker start <cId>`, pasando nuevamente al estado de _Running_.
    

**5. Eliminación (Container Removed):**

- Si ya no necesitamos el contenedor y queremos liberar el espacio, debemos ejecutar `docker rm <cId>`. Esto remueve de forma permanente el contenedor y su contenido, por lo que no volverá a estar disponible.