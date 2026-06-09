#AWS #Cloud #Services 

![[Limites de AWS-1780960658992.webp|444]]
### Límites de Servicio en AWS (Service Quotas)
#AWS #Services #Service_Quotas
AWS establece límites predeterminados en la cantidad de recursos que puedes crear por cuenta y por región. No se trata de una restricción arbitraria, sino de un mecanismo de seguridad y estabilidad.

**Propósito de los límites:** Existen principalmente por dos razones. La primera es evitar cargos excesivos: si un script entra en un bucle y comienza a lanzar cientos de instancias EC2, los límites actúan como freno para que la factura no se dispare. La segunda es proteger la infraestructura: impiden que un solo cliente consuma toda la capacidad de una región, garantizando que los recursos estén disponibles para todos y evitando el problema del “vecino ruidoso”.

### Límites Blandos (Soft Limits)
#AWS #Services 
Son límites que sí pueden modificarse. Si necesitas superarlos, debes abrir un caso con el soporte de AWS solicitando un aumento. Hoy en día, la mejor práctica es hacerlo desde la consola de **AWS Service Quotas**.

Un ejemplo práctico: de forma predeterminada, AWS establece un límite de 1,000 ejecuciones simultáneas para funciones Lambda por región. Si tienes una red de sensores que dispara 5,000 alertas al mismo tiempo, las funciones adicionales serán bloqueadas a menos que hayas solicitado previamente un aumento. Es importante tener en cuenta que subir demasiado un límite sin optimizar la arquitectura puede provocar saturación en tus propias bases de datos o cuellos de botella en la red.

### Límites Duros (Hard Limits)
#AWS #Services 
Son restricciones absolutas que no pueden modificarse bajo ninguna circunstancia. Están ligados a la arquitectura fundamental del servicio.

Algunos ejemplos:

- El número de VPCs por región es un límite blando, pero el bloque CIDR que asignas a una VPC no puede modificarse una vez creado, lo que constituye un límite duro.
    
- Un grupo de seguridad puede tener hasta 60 reglas de entrada o salida (límite blando), pero una instancia EC2 solo puede tener un máximo de 5 grupos de seguridad asociados (límite duro).
    
- En S3, el tamaño máximo de un objeto individual que puedes subir es de 5 terabytes, y este es un límite duro.
    

### Información de Valor (Tips de Examen y Buenas Prácticas)
#AWS #Trusted_Advisor

El servicio **AWS Trusted Advisor** es clave en este tema. Es el que te alerta de manera proactiva cuando estás alcanzando más del 80% de un límite de servicio. En los exámenes, si aparece esa pregunta, la respuesta correcta suele ser Trusted Advisor.

En cuanto a planificación, los aumentos de límites blandos no son automáticos. A veces requieren aprobación manual y pueden tardar días, por lo que conviene anticiparse y solicitarlos con semanas de anticipación antes de poner un proyecto en producción.

También es importante recordar que muchos límites se aplican por región. Si solicitas un aumento en `us-east-1`, no se replicará automáticamente en `us-west-2`; tendrás que hacer otra solicitud. Y aunque pedir un aumento no tiene costo, el uso de los recursos adicionales sí generará cargos en tu factura.

### ¿Trusted Advisor?
#AWS #Trusted_Advisor 

AWS Trusted Advisor es un servicio que analiza tu entorno en la nube y te ofrece recomendaciones basadas en buenas prácticas. Su objetivo es ayudarte a mantener tu infraestructura segura, eficiente y preparada para crecer. Funciona como una especie de “consultor automático” que revisa tu cuenta y te señala posibles problemas antes de que se conviertan en incidentes.

En relación con los límites de servicio, Trusted Advisor cumple un papel fundamental. Uno de sus chequeos más importantes es el de **Service Limits**, que te muestra cuánto estás usando de cada recurso frente al máximo permitido. Si detecta que estás cerca del 80% de un límite, te alerta para que tomes acción preventiva. Esto es especialmente útil con los **soft limits**, ya que te da tiempo de solicitar un aumento antes de que tus aplicaciones sufran bloqueos o throttling. De esta manera, evita que un pico de tráfico inesperado afecte la disponibilidad de tus sistemas.

Además de los límites, Trusted Advisor también revisa otras áreas clave como **optimización de costos**, **rendimiento**, **seguridad**, **tolerancia a fallos** y **excelencia operativa**. Por ejemplo, puede recomendarte eliminar recursos que no usas, ajustar configuraciones de seguridad o mejorar la resiliencia de tus aplicaciones. Todo esto se traduce en un entorno más estable y menos propenso a problemas financieros o técnicos.

El nivel de acceso a Trusted Advisor depende del plan de soporte que tengas en AWS. Con los planes básicos solo puedes ver algunos chequeos, principalmente los de límites y seguridad. En cambio, con los planes Business o Enterprise tienes acceso completo a todos los análisis y la posibilidad de integrarlo con **CloudWatch**, lo que permite recibir alertas automáticas cuando un límite está cerca de alcanzarse.

En la práctica, la mejor forma de aprovechar Trusted Advisor es usarlo de manera regular, refrescar sus chequeos y combinarlo con **AWS Service Quotas**. Mientras Service Quotas te permite solicitar aumentos de límites, Trusted Advisor te da visibilidad sobre el consumo actual y te avisa cuándo es momento de actuar. Así, ambos servicios trabajan juntos para que los límites de AWS no sean un obstáculo, sino un mecanismo de control que puedes gestionar de forma proactiva.

¿Quieres que te muestre cómo se complementan **Service Quotas** y **Trusted Advisor** en un flujo de trabajo típico de planificación de capacidad?