

![[Servicios Clave para CloudOps-1780955570900.webp|146]]

---

### Amazon Identity and Access Management
#AWS #IAM 

IAM permite el control de acceso a los recursos, es usado para la autenticacion y quiene pueden acceder a que recursos. IAM no es solo por autetnicacion, es usado para aplicaciones y otros accesos de servicios de AWS.

El usuario es quien determina que recursos puede acceder y que acciones pueden realizar sobre esos recursos, IAM borra la necesidad de compartir credenciales o permisos a otras personas o sistemas, lo hace mas facil, asi como el habilitar o deshabilitar un usuario 

Hay que pensar en IAM como el que maneja todo el tema de configurar, llanza, maneja y terminas los recursos de AWS. Este da permisos granulares sobre el access permissionn y no es solo para personas, es tambien para aplicaciones, sustemas o algo que necesite realizar llamadas programmaticaas 

Entre las caracteristicas a tener en cuenta son:
- No tiene cargos en la cuenta de AWS
- Permite crear Users, Groups y Roles
- Aplicar Politicaas a ellos para controlar el acceso a servicio de AWS

Cuando se crean usuarios se puede decidir que tipo de acceso va a tener, si programmtic o console access, o inluco ambos

el programmatic realizar la autenticacion por medio de Key ID y secret Access Key y da acceso a las API, AWS CLI, SDK y otras herramientas de desarrollo 

Por otro lado el Console Access permite que se un un account ID o un alias, luego el IAM user con contraseña, y se puede habilitar MFA para el codigo de autenticacion


| Email y Contraseña    | Associado a una cuenta de AWS (root)                                            |
| --------------------- | ------------------------------------------------------------------------------- |
| IAM user y contraseña | Usado para el accesso de AWS Management Console                                 |
| Access Keys           | Usualmente usado para el AWS CLI y programmatic request como serían APIs y SDKs |
| MFA                   | Un nivel extra de seguridad y se puede activar enn cuentas Root y en IAM users  |
| Key Pairs             | Es usado para algunos servicios esepcificos de AWS como EC2                     |

IMAGENES EN OTRO FORMATO 





Explicar External IDs