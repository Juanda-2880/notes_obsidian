#AWS #Cloud #Services 

![[Limites de AWS-1780960658992.webp|444]]
### 🚦 Límites de Servicio en AWS (Service Quotas)

AWS impone límites predeterminados en la cantidad de recursos que puedes crear por cada cuenta y por cada región. Esto no es para restringirte, sino como un mecanismo de seguridad y estabilidad.

**El Propósito (¿Por qué existen?):** Como menciona la imagen en el lado derecho, existen por dos razones vitales:

1. **Limitar cargos excesivos (Protección Financiera):** Imagina que un script automatizado para inyección masiva de datos o de generación de alertas entra en un bucle infinito por error y empieza a lanzar cientos de instancias EC2. Si no hubiera límites, la factura a fin de mes sería astronómica. Los límites actúan como un freno de emergencia.
    
2. **Mantener la red sana (Protección de la Infraestructura):** Evita que un solo cliente consuma toda la capacidad de una región, garantizando que haya recursos disponibles para todos (el concepto de "Vecino Ruidoso" o _Noisy Neighbor_).
    

### 🔼 Límites Blandos (Soft Limits)

#AWS #AWS_Service_Quotas

Son límites predeterminados que **sí se pueden cambiar**. Si necesitas superarlos, debes abrir un caso con el _Soporte de AWS_ solicitando un aumento (Limit Increase).

- **Tip de Examen:** Hoy en día, la mejor práctica para solicitar este aumento es usar la consola de **AWS Service Quotas**.
    
- **Caso de uso práctico:** De forma predeterminada, AWS tiene un límite blando de 1,000 ejecuciones simultáneas para funciones Lambda por región. Si tienes una red de sensores agroclimáticos (temperatura, humedad, precipitación) en campos de cultivo y ocurre un evento repentino que dispara 5,000 alertas al mismo tiempo, las funciones extra serán bloqueadas (Throttling) a menos que hayas pedido a AWS que te suba ese límite blando con anticipación.
    
- **Nota de la imagen:** _Exceder algunos límites blandos puede degradar el rendimiento._ Si subes demasiado un límite sin optimizar tu arquitectura, podrías saturar tus propias bases de datos o cuellos de botella de red.
    

### 🔽 Límites Duros (Hard Limits)

Son restricciones absolutas que **no pueden ser modificadas** bajo ninguna circunstancia, ni siquiera pagando por soporte premium. Suelen estar ligados a la arquitectura fundamental del servicio.

- **Ejemplos de Límites Duros (¡Preguntas de examen!):**
    
    - No puedes tener más de **5 VPCs** por región (Soft Limit) peroooo, el bloque CIDR (rango de IPs) que le asignas a una VPC no puede ser modificado una vez creado (Hard Limit).
        
    - Un grupo de seguridad (Security Group) no puede tener más de **60 reglas** de entrada o salida (Soft Limit), pero una instancia EC2 solo puede tener asignados un máximo de **5 grupos de seguridad** (Hard Limit).
        
    - El tamaño máximo de un objeto (archivo) individual que puedes subir a un bucket de S3 es de **5 Terabytes** (Hard Limit).
        

### 💡 Información de Valor (Tips de Examen y Buenas Prácticas)

#AWS #aws_Trusted_Advisor

- **AWS Trusted Advisor:** Es el servicio que **debes** asociar con los límites. En el examen, si te preguntan: _"¿Qué servicio te alerta de manera proactiva cuando estás alcanzando más del 80% de tu límite de servicio?"_, la respuesta correcta casi siempre es **AWS Trusted Advisor**.
    
- **Planificación (Best Practice):** Los aumentos de límites blandos no son automáticos. A veces un humano en AWS debe aprobarlos, lo que toma días. La buena práctica dicta que debes prever tus picos de tráfico y solicitar los aumentos con semanas de anticipación antes de lanzar un proyecto a producción.
    
- **Nivel de la Cuenta vs Nivel Regional:** Recuerda que muchos de estos límites se aplican **por región**. Si pides que te aumenten el límite de instancias EC2 en `us-east-1` (Norte de Virginia), ese límite no se aumentará mágicamente en `us-west-2` (Oregon); tendrás que hacer otra solicitud.
    
- **Costos:** Solicitar un aumento de límite **no cuesta dinero**. Sin embargo, aprovisionar y encender esos recursos adicionales una vez que te los autorizan, sí generará cargos en tu factura.