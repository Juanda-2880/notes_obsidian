#AWS #Cloud #Services 

### 🌐 1. Alcance Global (Global Scope)

Estos servicios no están atados a una ubicación geográfica específica. Operan a nivel mundial para toda tu cuenta de AWS.

**Recursos según la imagen:**

- **IAM** (Usuarios, grupos y roles).
    
- **Amazon Route 53** (Zonas alojadas y conjuntos de registros - DNS).
    
- **Amazon CloudFront** (Distribuciones - CDN).
    

> **💡 Tips de Examen y Buenas Prácticas:**
> 
> - **IAM:** Aunque los recursos están en regiones, el control de acceso es universal. Si creas una política de seguridad, aplica sin importar en qué región levantes tus servidores.
>     
> - **Rendimiento (CloudFront):** CloudFront no usa Regiones ni AZs tradicionales; utiliza **Edge Locations** (Ubicaciones de Borde) distribuidas por todo el mundo para cachear contenido estático. Esto significa que si un usuario en Europa pide un dato, se le sirve desde el borde más cercano, reduciendo la latencia drásticamente.
>     
> - **Route 53:** Es el servicio de DNS de AWS y tiene políticas de enrutamiento que pueden dirigir el tráfico a diferentes regiones del mundo según la salud del servidor o la ubicación del usuario.
>     

### 🗺️ 2. Alcance Regional (Region Scope)

Una Región es un área geográfica física en el mundo (ej. `us-east-1` en N. Virginia) que contiene múltiples Zonas de Disponibilidad.

**Recursos según la imagen:**

- **Amazon S3** (Buckets).
    
- **AMIs** (Amazon Machine Images).
    
- **Amazon CloudWatch** (Métricas).
    
- **EBS Snapshots** (Copias de seguridad de volúmenes EBS).
    
- **Amazon ElastiCache** (Clústeres).
    
- **VPC** (Virtual Private Cloud).
    

> **💡 Tips de Examen, Costos y Legales:**
> 
> - **Soberanía de Datos (Legal):** Esta es la razón principal para elegir una región. Si las leyes de un país exigen que la información de sus ciudadanos no cruce fronteras, _debes_ crear tus recursos (como buckets de S3 o la VPC) en la región de ese país.
>     
> - **Latencia y Rendimiento:** Por ejemplo, si tienes un sistema IoT automatizado de inyección de datos que monitorea variables agroclimáticas (como radiación solar, pH del suelo o precipitación), tu mejor práctica es desplegar la VPC y los servicios que procesan estas alertas (como Lambda o API Gateway) en la región geográfica más cercana a los cultivos reales para minimizar la latencia de red.
>     
> - **Costos de Transferencia:** La transferencia de datos _hacia adentro_ de AWS (Inbound) suele ser gratuita, pero la transferencia _hacia afuera_ (Outbound) o **entre diferentes regiones** tiene costo.
>     
> - **El truco de S3 (¡Pregunta clásica!):** El espacio de nombres de un bucket de S3 es **Global** (nadie más en el mundo puede usar el mismo nombre de bucket que tú), pero los datos y el bucket como tal existen físicamente en una **Región** específica.
>     
> - **AMIs y Snapshots:** Si creas una imagen (AMI) de un servidor o un Snapshot de un disco, ese respaldo se guarda en la región. Si quieres levantar ese mismo servidor en otra región, primero tienes que _copiar_ la AMI a la nueva región.
>     

### 🏢 3. Alcance de Zona de Disponibilidad (Availability Zone - AZ)

Una AZ está conformada por uno o más centros de datos físicos distintos dentro de una Región. Tienen energía, red y conectividad redundantes y separadas de otras AZs.

**Recursos según la imagen:**

- **Amazon EC2** (Instancias).
    
- **Amazon EBS** (Volúmenes de disco).
    
- **Amazon RDS** (Instancias de bases de datos relacionales).
    
- **Subnets** (Subredes de la VPC).
    

> **💡 Tips de Examen y Arquitectura:**
> 
> - **Alta Disponibilidad (High Availability - HA):** La regla de oro en AWS es nunca depender de una sola AZ. Si diseñas un backend, debes distribuir tus instancias EC2 y tus bases de datos RDS en al menos **dos AZs diferentes**. Así, si un centro de datos entero se inunda o pierde energía, tu sistema sigue funcionando desde la otra AZ.
>     
> - **La regla estricta de EC2 y EBS:** Un disco duro virtual (Volumen EBS) y el servidor (Instancia EC2) al que está conectado **DEBEN** estar en la misma AZ. Si tu script de Python para alertas masivas está corriendo en una instancia EC2 en la zona `us-east-1a`, su disco EBS no puede estar en la `us-east-1b`.
>     
> - **Subredes:** Como vimos en tus primeros apuntes, una subred no puede abarcar varias AZs; nace y muere dentro de una sola AZ.
