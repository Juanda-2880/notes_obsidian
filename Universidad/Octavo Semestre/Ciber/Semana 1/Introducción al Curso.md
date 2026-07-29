#Ciberseguridad #Universidad 

Salvaguardar la información ha sido una prioridad desde que los seres humanos necesitaron mantener datos seguros y privados. En la antigüedad, se utilizaban técnicas de cifrado simples como el **Cifrado César** para asegurar únicamente la **confidencialidad** de los mensajes.

Sin embargo, a medida que la tecnología avanza, las demandas de seguridad evolucionan. Hoy en día, la seguridad de la información no se limita a proteger el mensaje en tránsito, sino que tiene un objetivo triple reflejado en la **Tríada CID**: _Confidencialidad, Integridad y Disponibilidad_.

> **Nota clave - Ciberseguridad vs. Seguridad de la Información:** > Aunque a veces se usan como sinónimos, la **ciberseguridad es una parte de la seguridad de la información**. La ciberseguridad se enfoca específicamente en proteger los activos digitales abordando las amenazas contra la información procesada, almacenada y transportada por sistemas interconectados (Internet). A diferencia de la seguridad de la información general, la ciberseguridad **no** incluye riesgos por desastres naturales, errores físicos de personal o seguridad de instalaciones físicas individuales.

### Superficie de Ataque

Es el conjunto total de puntos, interfaces o vectores de entrada por donde un atacante puede intentar interactuar con un sistema, explotar una vulnerabilidad y comprometer una red o dispositivo.

### Principales Interfaces de la Superficie de Ataque:

1. **Interfaces Físicas:** Puertas de acceso a servidores, puertos USB expuestos, cables de red accesibles, switches sin transceptores protegidos y hardware en general.
    
2. **Interfaces Digitales y de Red:** Redes Wi-Fi públicas o corporativas, puertos lógicos abiertos, firewalls mal configurados y routers.
    
3. **Software y Sistemas Operativos:** El kernel del sistema operativo, APIs expuestas, interfaces de línea de comandos (CLI) y servicios del sistema vulnerables.
    
4. **Aplicaciones y Servicios Desplegados:** Servidores web (HTTP/HTTPS), sistemas de correo electrónico (SMTP/IMAP), bases de datos expuestas y aplicaciones web/móviles.
    
5. **Dispositivos Inteligentes e Internet de las Cosas (IoT):** Sensores, cámaras de seguridad y dispositivos domésticos o industriales conectados con credenciales por defecto o firmware desactualizado.
    
6. **Interfaces de Usuario y Software Cliente:** Navegadores web, lectores de correo y cualquier interfaz donde un usuario interactúe y pueda ser víctima de ingeniería social.


## 3. La Tríada CID (Confidencialidad, Integridad y Disponibilidad)

### A. Asegurar la Confidencialidad

Garantiza que la información esté protegida contra el acceso o la divulgación no autorizada.

- **Consecuencias si NO se cuida:** Revelación de información protegida por leyes de privacidad (como datos médicos o financieros), pérdida de confianza del público, pérdida de ventajas competitivas frente al mercado, demandas legales contra la empresa e interferencia con la seguridad nacional.
    
- **Métodos de Control:** * **Controles de acceso:** Autenticación robusta y asignación de privilegios mínimos.
    
    - **Permisos de archivos:** Reglas del sistema operativo para restringir lectura/escritura (ej. _chmod_ en Linux).
        
    - **Cifrado (Criptografía):** Transformar el texto plano en texto cifrado tanto en tránsito como en reposo (ej. AES, RSA).
        

### B. Asegurar la Integridad

Garantiza la exactitud y completitud de la información, protegiéndola de modificaciones, alteraciones o destrucciones no autorizadas.

- **Consecuencias si NO se cuida:** Datos inexactos, toma de decisiones erróneas basadas en datos alterados, fraudes financieros (como cambiar el monto de una transferencia bancaria), fallas inducidas en el hardware mediante configuraciones corruptas y pérdida de cumplimiento regulatorio (_Loss of Compliance_).
    
- **Métodos de Control:** * **Controles de acceso:** Evitar que usuarios no autorizados editen información.
    
    - **Monitoreo y Registro (Logging):** Auditoría constante de quién modificó qué cosa y cuándo.
        
    - **Firmas Digitales:** Garantizar la autenticidad y el no repudio del emisor.
        
    - **Hashes Criptográficos:** Funciones matemáticas (como SHA-256) que generan un valor único para verificar que el archivo no ha cambiado ni un solo bit.
        
    - **Backups y Cifrado:** Respaldos limpios para restaurar datos en caso de corrupción.
        

### C. Asegurar la Disponibilidad

Garantiza el acceso oportuno y confiable a la información y a los sistemas informáticos por parte de los usuarios autorizados cuando lo requieran.

- **Consecuencias si NO se cuida:** Pérdida de funcionalidad y efectividad operacional, pérdida de tiempo productivo de los empleados, interrupción de servicios críticos (caída de ingresos), multas severas por parte de reguladores gubernamentales, demandas de clientes y fallas en alcanzar los objetivos estratégicos de la empresa.
    
- **Métodos de Control:** * **Redundancia:** Duplicación de hardware, fuentes de poder, almacenamiento y conexiones de red para evitar puntos únicos de fallo.
    
    - **Arquitecturas de Alta Disponibilidad:** Balanceadores de carga y clústeres de servidores dinámicos.
        
    - **Replicación de Datos:** Copiado en tiempo real de bases de datos a ubicaciones secundarias.
        
    - **Backups:** Copias de seguridad periódicas almacenadas fuera de línea (offline) para recuperarse de ataques destructivos como el Ransomware.
        
    - **Planes de Continuidad del Negocio (BCP) y Recuperación ante Desastres (DRP):** Estrategias organizacionales detalladas para reaccionar ante contingencias severas.

### MC Cumber Cube

### Dimensión 1: Los Estados de la Información (Eje X)

- **Procesamiento (Data in Use):** Datos que están siendo utilizados activamente por el procesador o la memoria RAM en la ejecución de una operación (ej. la actualización de una base de datos en tiempo real).
    
- **Almacenamiento (Data at Rest):** Datos grabados y guardados en medios físicos o lógicos de almacenamiento, como discos duros (HDD), unidades de estado sólido (SSD) o cintas de respaldo.
    
- **Transmisión (Data in Transit):** Datos que se mueven o viajan a través de una red de comunicaciones, desde un punto de origen a un destino (ej. paquetes circulando por cables de red o enlaces Wi-Fi).
    

### Dimensión 2: Las Medidas de Seguridad / Salvaguardas (Eje Y)

- **Tecnología (Technology):** Herramientas de hardware y software diseñadas para proteger los estados de la información. Incluye firewalls, sistemas de detección de intrusos (IDS/IPS), software de cifrado y parches de seguridad automatizados.
    
- **Políticas y Procedimientos (Policies and Procedures):** Las reglas, directrices y normativas administrativas que dictan cómo debe operar la organización de forma segura. Define los estándares de cumplimiento, control de contraseñas y qué acciones tomar en caso de un incidente.
    
- **Concienciación, Entrenamiento y Educación (Awareness, Training, Education):** El factor humano. Iniciativas diseñadas para asegurar que los usuarios y administradores conozcan las amenazas, identifiquen ataques de ingeniería social y sepan cómo aplicar las políticas de seguridad en su día a día.
    

### Dimensión 3: Los Objetivos de Seguridad (Eje Z)

- **Confidencialidad**
    
- **Integridad**
    
- **Disponibilidad**
    

### Integración de las Tres Dimensiones:

El valor del cubo radica en que **un sistema no está seguro si se descuida una sola de las 27 celdas resultantes de la intersección**. Por ejemplo: Para proteger la información en **Transmisión** (Dimensión 1) y garantizar su **Confidencialidad** (Dimensión 3), no basta con aplicar **Tecnología** como el cifrado HTTPS; también se requiere una **Política** empresarial que prohíba el uso de HTTP claro y la **Educación** del usuario para que no acepte certificados inválidos en su navegador.



![[Introducción al Curso-1785354833847.webp|304]]




### Vishing

El **Vishing** es una modalidad de estafa y ataque de **ingeniería social** que se realiza a través de llamadas telefónicas convencionales o sistemas de Voz sobre IP (VoIP).

- **¿Cómo funciona?:** El atacante suplanta la identidad de una entidad de confianza (como un banco, el departamento de soporte técnico de una empresa o una entidad gubernamental) para engañar a la víctima.
    
- **Objetivo:** Manipular emocionalmente a la persona (usualmente infundiendo urgencia o miedo) para que revele información sensible, como credenciales de acceso, contraseñas temporales (OTP), números de tarjetas de crédito o para que realice transferencias de dinero de manera voluntaria.
    
- **Mitigación:** Educación del usuario (_Awareness_), verificación de canales oficiales de comunicación y la política estricta de que ninguna entidad legítima solicita contraseñas por llamada telefónica.

