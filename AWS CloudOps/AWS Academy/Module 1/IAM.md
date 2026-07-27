

![[Servicios Clave para CloudOps-1780955570900.webp|146]]

---

### Amazon Identity and Access Management
#AWS #IAM #Cloud 

IAM es el servicio que permite el control de acceso seguro a los recursos de AWS. Se encarga tanto de la **autenticación** (verificar quién es el usuario o sistema) como de la **autorización** (determinar a qué recursos tienen acceso y qué acciones específicas pueden realizar). IAM no se limita únicamente a usuarios humanos; también gestiona el acceso para aplicaciones y otros servicios de AWS.

El administrador de la cuenta es quien determina a qué recursos se puede acceder y qué acciones se pueden ejecutar sobre ellos (mediante llamadas a la API). IAM elimina la necesidad de compartir credenciales o contraseñas entre personas o sistemas, facilitando la gestión del ciclo de vida del acceso, como habilitar, auditar o deshabilitar usuarios de forma segura.

Debes pensar en IAM como el punto de control centralizado que otorga los permisos granulares necesarios para configurar, lanzar, administrar y terminar cualquier recurso dentro de tu entorno de AWS.

**Características principales a tener en cuenta:**

- 💡 **Servicio Global:** IAM no requiere selección de región; los usuarios, grupos, roles y políticas creados aplican a nivel global en toda la cuenta de AWS.
    
- **Es gratuito:** No tiene cargos adicionales en la factura de AWS; solo pagas por los recursos que los usuarios lanzan.
    
- Permite crear **Usuarios (Users)**, **Grupos (Groups)** y **Roles**.
    
- Permite aplicar **Políticas (Policies)** escritas en formato JSON a estas entidades para denegar u otorgar el acceso a los servicios de AWS.
    
- Al crear un usuario IAM, se puede decidir su tipo de acceso: **Programmatic Access** (acceso programático), **Console Access** (acceso a la consola de administración), o ambos.
    

### Tipos de Credenciales y Métodos de Acceso

|Tipo de Credencial|Descripción y Caso de Uso|
|---|---|
|**Email y Contraseña**|Asociado a la cuenta raíz (Root User) de AWS. 💡 _Solo debe usarse para la configuración inicial, tareas de facturación o cierre de cuenta. No debe usarse para tareas diarias._|
|**IAM User y Contraseña**|Usado para el acceso a la interfaz gráfica (AWS Management Console). El inicio de sesión requiere el ID de la cuenta (o su Alias), el nombre de usuario y la contraseña.|
|**Access Keys**|Compuesto por un _Access Key ID_ y un _Secret Access Key_. Es usado para realizar peticiones programáticas, como las que se hacen a través de AWS CLI, APIs y SDKs.|
|**MFA (Autenticación Multifactor)**|Una capa de seguridad extra mediante un código temporal (Token). 💡 _Es una de las mejores prácticas de seguridad y se debe activar tanto en la cuenta Root como en los usuarios IAM._|
|**Key Pairs (Pares de Claves)**|Utilizados para la autenticación a nivel de sistema operativo (SSH para Linux o RDP para Windows) en servicios específicos como Amazon EC2.|

### Análisis de Imágenes y Explicación de Conceptos

#### 1. Políticas y Permisos (Policies and permissions)

|Tipo de Política|Actúa como Permiso de Política|Actúa como Límite de Permisos (Boundaries)|Se usa como Política Administrada (Managed)|Se usa como Política Integrada (Inline)|
|---|---|---|---|---|
|**Identity-based** (Basada en Identidad)|✔||✔|✔|
|**Resource-based** (Basada en Recursos)|✔||||
|**SCP** (Service Control Policy)||✔|||
|**ACLs** (Listas de Control de Acceso)|✔||||

💡 **Explicación Completa:** La imagen ilustra la jerarquía y frecuencia de uso de las políticas, siendo la base (Identity-based) la más frecuente y las ACLs las menos frecuentes.

- **Identity-based Policies:** Se adjuntan directamente a usuarios, grupos o roles de IAM. Pueden ser _Managed_ (AWS gestiona la política y puedes reusarla) o _Inline_ (creadas e incrustadas directamente en un usuario/rol específico).
    
- **Resource-based Policies:** No se adjuntan al usuario, sino al recurso en sí (Ej. S3 Bucket Policies, políticas de colas SQS). Responden a la pregunta: _"¿Quién puede invocar o acceder a este recurso?"_
    
- **SCP (Políticas de Control de Servicios):** Se aplican a nivel de AWS Organizations. No otorgan permisos por sí solas, sino que actúan como una "barrera" (Permission Boundary) que define el máximo nivel de permisos que las cuentas miembro pueden tener.
    
- **ACLs:** Son un mecanismo de seguridad heredado (legacy) utilizado principalmente en Amazon S3 y VPC (Network ACLs). En IAM puro, rara vez se usan hoy en día en favor de las políticas JSON.
    

#### 2. Permisos Basados en Usuarios vs. Basados en Recursos

**User-Based Permissions (¿A qué tiene acceso esta entidad?)**

|Entidad (Usuario/Grupo)|Recurso X|Recurso Y|Recurso Z|
|---|---|---|---|
|**Bob**|Read, Write, List|-|-|
|**Larry**|-|Read|Read|
|**Managers (Grupo)**|List|List|List|

**Resource-Based Permissions (¿Quién tiene acceso a este recurso?)**

|Recurso|Bob|Doug|Jim|Sara|Larry|Sam|
|---|---|---|---|---|---|---|
|**Recurso X**|Read, Write, List|Read, Write, List|Read, List|List|-|-|
|**Recurso Y**|Read, Write, List|-|-|-|Read|Write, List|

💡 **Explicación Completa:** Estas tablas demuestran que en AWS la evaluación de acceso a menudo requiere mirar dos lados de la moneda.

- En el modelo **User-Based**, si auditas a "Bob", ves directamente todo lo que él puede tocar (en este caso, escribir, leer y listar el Recurso X).
    
- En el modelo **Resource-Based**, la perspectiva cambia al recurso. Si miras las políticas del "Recurso X" (por ejemplo, un Bucket de S3), verás una lista de todos los agentes (Bob, Doug, Jim, Sara) que tienen permitido interactuar con él.
    
- 💡 _Tip de Examen:_ En la misma cuenta de AWS, si una política basada en identidad O una política basada en recursos otorga el acceso (y no hay un `Deny` explícito), el acceso será permitido.
    

#### 3. Uso de Roles IAM (Using IAM Roles)

**Flujo del diagrama:**

1. Origen: Servicios de AWS (ej. EC2) o identidades federadas mediante Single Sign-On (SAML 2.0, OAuth 2.0).
    
2. Acción: Usando Roles de IAM, un usuario/sistema puede proveer una delegación temporal.
    
3. Destino: Cambiar de rol (Switch Roles) para acceder a recursos en la misma cuenta de AWS o en otras cuentas de AWS (Cross-Account Access).
    

💡 **Explicación Completa:** A diferencia de un usuario IAM, **un Rol no tiene credenciales a largo plazo** (no tiene contraseña ni access keys permanentes). En su lugar, un rol es "asumido" temporalmente por entidades de confianza. Cuando un servicio asume un rol, AWS STS (Security Token Service) le devuelve credenciales temporales.

- **Caso de uso 1 (Servicios de AWS):** Le das un rol a una máquina virtual (EC2) para que pueda leer archivos de S3 de manera segura, sin tener que guardar claves dentro del código de la máquina.
    
- **Caso de uso 2 (Federación):** Permites que empleados que se autentican en el Active Directory de la empresa (SAML 2.0) asuman un rol en AWS para trabajar en la consola sin tener que crearles un usuario IAM manual a cada uno.
    
- **Caso de uso 3 (Cross-Account):** Das permiso a un usuario de la Cuenta A para que asuma un rol en la Cuenta B y administre recursos allí.
    

#### 4. Mejores Prácticas de IAM (IAM Best Practices)

Estas prácticas son fundamentales para las preguntas situacionales del examen:

- 💡 **Evitar usar credenciales Root para la administración diaria:** Bloquea la cuenta Root y usa usuarios IAM o roles administrativos.
    
- 💡 **Delegar funciones administrativas con el principio del menor privilegio (Least-Privilege):** Otorga únicamente los permisos estrictamente necesarios para completar una tarea.
    
- 💡 **Usar Roles IAM para proporcionar acceso entre cuentas (Cross-account access):** No compartas usuarios IAM ni crees usuarios duplicados en varias cuentas; usa la delegación de roles.
    
- 💡 **Implementar MFA:** Añade un nivel extra de seguridad a las cuentas requiriendo un token dinámico además de la contraseña.
    

### Explicación Adicional: External IDs (IDs Externos)

💡 **¿Qué es un External ID?** Un External ID es un fragmento de información (generalmente una cadena de texto alfanumérica única, como un UUID) que se utiliza en las **Políticas de Confianza (Trust Policies)** de un Rol IAM para agregar una capa extra de seguridad cuando otorgas acceso a terceros (Cross-Account Access). Se define mediante la condición `sts:ExternalId`.

**¿Para qué sirve?** Sirve específicamente para prevenir una vulnerabilidad de seguridad conocida como el **"Confused Deputy Problem"** (El problema del delegado confundido).

**Ejemplo de cómo funciona (Caso de examen):**

1. Contratas a una empresa externa (SaaS, como un monitor de rendimiento en la nube) para que analice los recursos de tu cuenta de AWS.
    
2. Para darles acceso, creas un Rol en tu cuenta y en la política de confianza especificas que la cuenta de AWS de la empresa SaaS puede asumir ese rol (`sts:AssumeRole`).
    
3. **El Problema:** Si otro cliente de esa misma empresa SaaS adivina tu ARN del Rol, podría entrar al panel de la empresa SaaS y proporcionar tu ARN. La empresa SaaS (el delegado confundido) usaría sus permisos para entrar a tu cuenta y le mostraría tu información al atacante.
    
4. **La Solución (External ID):** La empresa SaaS genera un External ID único para ti (ej. `12345-ABC`). Tú agregas la condición en la política del rol en tu cuenta de AWS indicando que el rol _solo_ puede ser asumido si, al hacer la petición, se entrega ese `ExternalId` exacto. Como el atacante no conoce ese ID secreto, no puede engañar a la empresa SaaS para que asuma tu rol.