#AWS #Cloud #Services


![[Servicios Clave para CloudOps-1780953375924.webp|129]]

----
### Amazon Virtual Private Cloud 
 #AWS #VPC

Se usa para configurar la red en entornos de nube. Todos los recursos de infraestructura se despliegan dentro de una VPC (servicios como EC2, RDS, etc.). La VPC otorga un control total sobre un entorno de red **aislado lógicamente** de otras redes virtuales en la nube de AWS.

Cada VPC está definida y restringida a una **Región** específica (no puede abarcar múltiples regiones), pero puedes tener varias VPCs dentro de la misma región. Haciendo uso de las VPCs, se pueden segmentar las redes creando **Subredes (Subnets)**, ya sean públicas o privadas.

**Conceptos Clave:**

- **Redes Virtuales:** Aisladas lógicamente.
    
- **Características Configurables:** Rangos de IP (CIDR blocks), tablas de enrutamiento (routing tables), puertas de enlace (gateways) y configuraciones de seguridad.
    

> ** Información de Valor (Tip de Examen):** > * **Subredes:** A diferencia de la VPC, una subred está restringida a una sola **Zona de Disponibilidad (AZ)**.
> 
> - **Seguridad:** Las VPCs usan dos capas de seguridad: **Security Groups** (a nivel de instancia, son _stateful_ o con estado) y **Network ACLs** (a nivel de subred, son _stateless_ o sin estado).
>     
> - **Caso de uso práctico:** Si tienes un servidor procesando datos entrantes de sensores (temperatura, humedad), puedes colocarlo en una subred pública para recibir el tráfico, pero tu base de datos debe ir estrictamente en una subred privada.
>

----


![[Servicios Clave para CloudOps-1780954334109.webp|211|137x137]]

----

### Amazon Elastic Compute Cloud
#AWS  #EC2 #AMI

AWS ofrece una gran cantidad de opciones de cómputo, siendo Amazon EC2 una de las principales. Nos permite aprovisionar capacidad de cómputo virtual en la nube. Prácticamente todo lo que se puede hacer en un servidor _On-Premise_ (local), se puede hacer en una instancia EC2.

Soporta múltiples sistemas operativos como Windows, RedHat, Ubuntu, Amazon Linux, entre otros. Se pueden crear copias exactas o imágenes de los servidores en cualquier momento con unos pocos clics o mediante un llamado a la API. Estas imágenes se llaman **AMIs (Amazon Machine Images)** y se usan para lanzar instancias duplicadas rápidamente.

EC2 es **altamente escalable**, lo que significa que puedes añadir más instancias cuando la demanda aumente (Scale Out) y destruirlas cuando ya no se necesiten (Scale In). Para cada instancia, se pueden seleccionar atributos de cómputo específicos (CPU, RAM, Almacenamiento, Red) según la necesidad, ayudando a la optimización de costos y rendimiento para cualquier proyecto o utilidad del negocio.

**Conceptos Clave:**

- Entornos de cómputo en la nube.
    
- **AMIs:** Plantillas para crear instancias.
    
- **Escalabilidad y Optimización:** Adaptable a las necesidades del negocio.
    

> **💡 Información de Valor (Tip de Examen):** > * **Modelos de Precios:** Debes conocer la diferencia entre **On-Demand** (pago por uso sin compromiso), **Reserved Instances** (compromiso de 1 a 3 años para mayor descuento), y **Spot Instances** (aprovecha capacidad no utilizada a precios muy bajos, ideal para cargas de trabajo tolerantes a fallos o scripts de simulación de datos masivos que puedan interrumpirse).
> 
> - **User Data:** EC2 permite pasar un script (User Data) que se ejecuta automáticamente solo durante el primer arranque de la instancia, ideal para automatizar instalaciones (como instalar Python y dependencias).
>

---

![[Servicios Clave para CloudOps-1780955570900.webp|146]]

---

### Amazon Identity and Access Management
#AWS #IAM 

IAM se usa para gestionar el control de acceso a los recursos de AWS para usuarios y programas. Se utiliza para definir **quién** está autenticado (logueado) y **qué** recursos tiene autorizados a usar.

IAM soporta un control granular sobre los permisos de acceso, permitiendo determinar exactamente qué llamadas a la API de cada servicio puede hacer un usuario o programa. Al asignar permisos, siempre se debe aplicar el **Principio de Menor Privilegio** (dar solo los permisos estrictamente necesarios para hacer el trabajo).

Se pueden crear varios Usuarios (IAM Users), por lo que nunca es necesario compartir credenciales. Una opción avanzada de IAM es la **Identity Federation** (Federación de Identidades), con la cual los usuarios pueden hacer _Single Sign-On (SSO)_ para acceder a una cuenta de AWS usando las credenciales de su directorio corporativo activo. Utiliza estándares como _Security Assertion Markup Language 2.0_ (SAML) para intercambiar información de identidad y seguridad entre el Proveedor de Identidad (IdP) y AWS.

**Conceptos Clave:**

- Autenticación y Autorización.
    
- Principio de menor privilegio.
    
- Federación de Identidades (SAML 2.0).
    

> **💡 Información de Valor (Tip de Examen):** > * **IAM Roles vs IAM Users:** Los _Users_ son para personas o aplicaciones externas (con Access Keys). Los _Roles_ son asumidos temporalmente por servicios de AWS.
> 
> - **Caso de uso práctico:** Si escribes un script en una función **AWS Lambda** para generar alertas automáticas y necesitas que guarde registros, no le creas un usuario; le asignas un _IAM Role_ que tenga una política adjunta con permisos de escritura hacia CloudWatch.
>     
> - **IAM es Global:** A diferencia de VPC o EC2, IAM no requiere seleccionar una región; la configuración aplica globalmente para toda la cuenta.
>