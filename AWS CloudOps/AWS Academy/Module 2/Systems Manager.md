

![[Systems Manager-1782249019727.webp|224]]

-----

#AWS #Systems_Manager

AWS Systems Manager es un servicio que simplifica la administración, configuración y aplicación de parches en recursos de AWS y en entornos **On-Premises** (híbridos). Una gran ventaja es que ofrece soporte nativo tanto para Linux como para Windows.

Permite automatizar tareas operativas, crear y configurar Amazon Machine Images (AMIs), y administrar sistemas operativos a escala. Casi todo se puede automatizar ejecutando scripts remotamente. Además, permite mantener un inventario de las instancias EC2 para verificar que estén instaladas y configuradas correctamente según los estándares definidos.

> **💡 Dato clave para el examen:** Para que SSM pueda administrar una instancia EC2, dicha instancia debe cumplir tres requisitos: tener instalado el **SSM Agent**, tener un **IAM Role** asociado con los permisos adecuados (como `AmazonSSMManagedInstanceCore`) y tener conectividad con los endpoints de SSM (ya sea por internet o mediante VPC Endpoints para entornos privados).

## 1. Automation

Permite realizar tareas comunes y repetitivas de operaciones de TI de forma segura en los recursos de AWS.

- Funciona mediante **Documentos de Automatización** (Automation Documents), escritos en JSON o YAML, que contienen pasos secuenciales.
    
- Permite ejecutar tareas de rutina como tomar snapshots, reiniciar instancias o actualizar AMIs.
    
- Se pueden monitorear y probar las automatizaciones para verificar que los resultados sean los esperados.
    
- **Integración:** Se puede integrar con Amazon EventBridge (anteriormente CloudWatch Events) para que, al ocurrir un evento específico, se dispare automáticamente un documento de automatización.
    

## 2. Session Manager

Es una de las características más importantes para la seguridad. Permite acceder a la consola o terminal de las instancias de forma interactiva **sin necesidad de abrir puertos de entrada (inbound ports)**, ni usar contraseñas, llaves SSH o _Bastion Hosts_.

- Mejora considerablemente la seguridad de la VPC al no requerir puertos abiertos (como el 22 para SSH o el 3389 para RDP).
    
- Se integra con IAM para aplicar permisos granulares sobre quién puede acceder a qué instancia.
    
- Todos los comandos ejecutados pueden ser guardados y auditados utilizando AWS CloudTrail, Amazon S3 o CloudWatch Logs.
    

## 3. Run Command

Permite ejecutar comandos de forma remota y a escala en múltiples instancias EC2 o servidores _On-Premises_ sin necesidad de conectarse interactivamente a ellas. Es ideal para ejecutar scripts o aplicar configuraciones masivas en una flota de servidores.

## 4. Patch Manager

Despliega actualizaciones de sistemas operativos y parches de software de forma automatizada a lo largo de flotas de instancias EC2 y servidores _On-Premises_.

- **Patch Baseline:** Define las reglas de aprobación automática de parches (por ejemplo, aprobar parches de seguridad críticos 7 días después de su lanzamiento) y la lista de parches rechazados.
    
- **Patch Groups:** Permite organizar las instancias usando etiquetas (Tags) para aplicar parches específicos a grupos específicos (ej. grupo de "Desarrollo" vs. grupo de "Producción").
    
- Los parches no se aplican solo cuando hay errores, sino como un proceso de cumplimiento (_compliance_) regular.
    
- Proporciona resultados detallados para realizar auditorías sobre el estado de actualización de las instancias.
    

## 5. Maintenance Windows (Ventanas de Mantenimiento)

Permite programar ventanas de tiempo específicas para ejecutar tareas administrativas y de mantenimiento en las instancias de forma automática, minimizando el impacto en la producción.

- Se pueden configurar límites de concurrencia (cuántas tareas simultáneas ejecutar a la vez) y umbrales de error (detener la tarea si fallan _N_ instancias).
    
- **Pasos de configuración:**
    
    1. Crear la ventana de mantenimiento (definiendo el horario cron/rate).
        
    2. Registrar los _Targets_ (los recursos o grupos de instancias afectados).
        
    3. Asignar las _Tasks_ (tareas a ejecutar, como _Run Command_, _Automation_, _AWS Lambda_ o _Step Functions_).
        

## 6. State Manager

Es un servicio de administración de configuración seguro y escalable que asegura que las instancias EC2 y servidores _On-Premises_ mantengan un estado consistente o deseado (ej. asegurar que el antivirus esté instalado o el firewall configurado).

- Utiliza documentos SSM (JSON o YAML) para definir el estado deseado.
    
- Asocia las instancias con el documento y programa con qué frecuencia (schedule) se debe aplicar o verificar este estado.
    
- Los registros (logs) de ejecución se pueden guardar en un bucket de S3 para su posterior revisión.
    

## 7. Parameter Store

Facilita la gestión de datos de configuración y la gestión de secretos (contraseñas, claves de bases de datos, cadenas de conexión).

- Permite guardar texto plano o datos cifrados (usando la integración nativa con **AWS KMS** mediante la opción `SecureString`).
    
- Evita tener contraseñas codificadas directamente en el código fuente (_hardcoding_).
    
- Se integra estrechamente con IAM para controlar el acceso granular a los parámetros.
    
- > **💡 Dato clave para el examen:** En código o en _Run Command_, es mucho más seguro invocar estos parámetros dinámicamente. Aunque Parameter Store maneja secretos, si requieres _rotación automática_ de credenciales de bases de datos de forma nativa, el examen suele apuntar a **AWS Secrets Manager**.
    

## 8. Inventory

Recolecta información de metadatos sobre las instancias y el software instalado en ellas: aplicaciones, archivos, configuraciones de red, servicios de Windows, roles del servidor, actualizaciones y propiedades del sistema.

- Permite visualizar estos datos de forma comprensiva sin necesidad de iniciar sesión en cada instancia.
    
- Se utiliza para auditorías de licencias, monitoreo de integridad de archivos y descubrimiento de activos.
    

## 9. Explorer / OpsCenter / Insights

- **Explorer:** Es un panel (Dashboard) personalizable que muestra datos operativos de los recursos, integrando información de CloudTrail, AWS Config (cambios de configuración), Inventory y el cumplimiento de parches.
    
- **OpsCenter:** Centraliza la gestión de problemas operativos (llamados _OpsItems_). Ayuda a los ingenieros de operaciones a ver, investigar y resolver problemas combinando datos de CloudWatch, EventBridge y AWS Personal Health Dashboard.