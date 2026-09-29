
### Proveedor de Nube

La verdadera pregunta para comenzar es que proveedor de nube seleccionar, a simple vista parece que Azure es el ideal dado a la restricción de si o si tener que usar Microsoft Fabric, sin embargo hay que evalual algunos costos

GCP: Ofrece un costo de un 25% más economico que otros competidores en máquinas virtuales (Posible ventaja en el Computo de Escalado que se había dicho anteriormente)

Azure: Cuenta con precios buenos, sin embargo cuenta con una ventaja de que si se usa una licencia de Windows Server u otros productos de Microsoft ofrece ventaja hibrida y algunos descuentos (Puedes ser muy buena opción para poder manejar todo el tema de la Infraestructura Híbrida)

AWS: Yo lo descartaría de buenas a primeras porque me parece que tiene un costo muy alto y tiene algunos cargos adicionales complejos por transferencia de datos y servicios especificos (El cargo de transferencias de datos no es conveniente dado a la idea de tener algunas transferencias en caídas de nube pueden ser más costosas)

Mi idea principal sería realizar una especie de Infraestructura Hibrida con GCP, Azure y en Local, lo que veo principalemente es que se puede aprovechar lo barato que sea GCP en Computo, cosas como un GKE las Vms entre otras cosas de Computo y escalado nos ayudarían a que este pueda llegar a ser más conveniente. Por el lado de Azure nos ayudaría a gestionar las cosas de Fabric y todo el tema de la comunicación entre la Nube y lo Local. 

Para el tema de que se encuentren estables es hacer replicas de los servicios en la Nube Aliada, es decir, de que se tengan las cosas de Computo con GCP y la replica en caso de Falla sea en Computo de Azure. Siguiendo esta misma idea en viceversa con Azure y GCP

Esto nos asegura una buena disponibilidad en caso de falla de una nube y priorizar de que el servicio este siempre operativo.


