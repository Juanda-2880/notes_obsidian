
**Universidad Icesi — Facultad Barbiere de Ingeniería, Diseño y Ciencias Aplicadas**
**Asignatura:** Ciberseguridad
**Integrantes:**

* Juan David Pacheco Vargas — A00401844
* Esteban Guarin Valencia — A00402028

---
## Introducción y Contexto

### Introducción

En este documento se desarrolla un análisis técnico y exhaustivo sobre las vulnerabilidades y fallas de configuración detectadas en el servidor de la Universidad San Marino, planteando una estrategia formal de endurecimiento (_hardening_) estructurada a partir de los fundamentos teóricos de la ciberseguridad y los lineamientos del Center for Internet Security (CIS Benchmark para Ubuntu Server 24.04 LTS). El informe abarca la identificación y categorización de los riesgos presentes en la máquina, la formulación de medidas de remediación clasificadas según el triángulo CID (Confidencialidad, Integridad y Disponibilidad), la homologación directa de los controles con recomendaciones normativas internacionales, el diseño del prompt de soporte en Inteligencia Artificial y una síntesis reflexiva sobre las lecciones aprendidas en la administración y aseguramiento de servidores corporativos y académicos.

### Contexto

El punto de partida del escenario analizado corresponde al despliegue reciente de un servidor institucional basado en el sistema operativo Ubuntu Server 24.04 LTS, cuya finalidad es alojar la plataforma de aprendizaje virtual de la Universidad San Marino. Tras la instalación inicial efectuada por los encargados de tecnología (Liza y Juan), el equipo de auditoría identificó que el sistema operaba bajo configuraciones por defecto altamente permisivas, habiendo sacrificado deliberadamente mecanismos esenciales de protección perimetral —como el cortafuegos del sistema operativo— con el propósito de habilitar servicios de comunicación heredados y obsoletos (FTP y Telnet).

Esta situación, sumada a la ausencia de políticas de actualización, retención de registros (_logging_), gestión del ciclo de vida de identidades y resguardo criptográfico de credenciales, ha dejado al servidor expuesto a vectores de ataque triviales como accesos remotos no autorizados con privilegios de superusuario, intercepción de credenciales en tránsito y en reposo, y denegación de servicios por falta de respaldos. A continuación, se presenta el desarrollo ordenado de las preguntas del taller para transformar este entorno vulnerable en una infraestructura resiliente y alineada a los estándares de la industria.

---

## Respuestas al Cuestionario de Hardening

### Pregunta 1

**¿Qué elementos de este caso representan riesgos para la seguridad del sistema? Enuméralos y relaciónalos con conceptos del curso (como superficie de ataque, privilegios, cifrado, etc.).**

A continuación se analizan los hallazgos reportados en la auditoría, vinculando cada condición anómala con los conceptos teóricos de la ciberseguridad:

- **Acceso SSH como root habilitado globalmente (`0.0.0.0/0`):** Este elemento vulnera directamente el **Principio de Menor Privilegio (_Least Privilege_)** y debilita los controles de **Autenticación y Control de Acceso**. Permitir conexiones directas con la cuenta `root` elimina la trazabilidad individual de quién ejecuta los comandos (_Accountability_ o No Repudio), facilita ataques de fuerza bruta remotos contra una cuenta con nombre predecible y universal, y concede control irrestricto sobre el sistema operativo si la credencial es comprometida.
    
- **Presencia de cuentas inactivas por más de seis meses:** Corresponde a la existencia de **cuentas huérfanas o inactivas**, un fallo crítico en la **Gestión del Ciclo de Vida de Identidades (IAM)**. Estas cuentas amplían la **superficie de ataque** del servidor de forma innecesaria, ya que suelen conservar credenciales antiguas o desactualizadas que escapan al monitoreo continuo del usuario legítimo, convirtiéndose en puntos de entrada silenciosos para la persistencia o el movimiento lateral de un intruso.
    
- **Servicios obsoletos activos (FTP y Telnet) y ausencia de cifrado en tránsito:** La ejecución de demonios que no se utilizan incrementa directamente la **superficie de ataque** (_Attack Surface_), exponiendo puertos y procesos con posibles vulnerabilidades inherentes. A nivel criptográfico, tanto Telnet (puerto 23) como FTP (puerto 21) transmiten credenciales y datos en **texto plano (sin cifrar)**. Cualquier agente hostil ubicado en la misma red o en el trayecto de enrutamiento puede interceptar el tráfico mediante ataques de tipo _Man-in-the-Middle_ (MitM) o captura de paquetes (_packet sniffing_ con herramientas como Wireshark), quebrando por completo el servicio de **Confidencialidad**.
    
- **Deshabilitación total del cortafuegos nativo (Firewall desactivado):** Viola el postulado arquitectónico de **Defensa en Profundidad (_Defense in Depth_)** y anula el **Filtrado de Red y Control de Acceso Perimetral**. Desactivar el cortafuegos (iptables / UFW) deja todos los puertos del host expuestos a escaneos y enumeraciones de red (mediante herramientas como Nmap), impidiendo la segmentación del tráfico entrante y saliente.
    
- **Carencia total de actualizaciones y parches de seguridad en el sistema operativo:** Afecta la **Gestión de Vulnerabilidades del Ciclo de Vida de Software**. Un sistema operativo congelado en su estado base acumula fallos conocidos de seguridad (_Common Vulnerabilities and Exposures_, CVEs) en el kernel, librerías compartidas (como OpenSSL) y binarios esenciales. Esto abre la puerta a que herramientas automatizadas de explotación (como Metasploit) ejecuten _payloads_ remotos con un mínimo nivel de esfuerzo técnico.
    
- **Inexistencia de registro (_logging_) y de rotación activa de bitácoras:** Compromete el servicio de seguridad de **Rendición de Cuentas (_Accountability_ / Trazabilidad)** y el **No Repudio**. Sin logs centralizados y estructurados, es imposible detectar anomalías, investigar incidentes de seguridad (_Computer Forensics_) o responder a eventos adversos. Asimismo, la falta de rotación expone al servidor a una condición de **Denegación de Servicio (DoS)** si el disco llegara a saturarse por logs huérfanos, o deja al administrador a ciegas ante un atacante que altere el sistema sin dejar rastro.
    
- **Contraseñas almacenadas en texto plano en archivos de configuración web:** Representa una brecha flagrante en la **Confidencialidad de Credenciales** y un desconocimiento de las **Prácticas Criptográficas y Gestión de Secretos**. Cualquier vulnerabilidad en la aplicación web (por ejemplo, _Local File Inclusion_ o inyecciones SQL que lean el sistema de archivos) o una mala asignación de permisos de lectura a usuarios no privilegiados expondrá de manera inmediata las contraseñas de bases de datos o servicios conexos, permitiendo el escalamiento de privilegios vertical y horizontal.
    
- **Ausencia de políticas periódicas de revisión de privilegios y de copias de seguridad (_backup_):** Afecta directamente el pilar de **Disponibilidad** y la gobernanza del control de accesos. Sin un mecanismo de respaldo verificado, el servidor carece de resiliencia ante incidentes catastróficos, como ataques de _ransomware_, corrupción lógica del almacenamiento o errores humanos de administración, impidiendo la recuperación del servicio académico.
    

---

### Pregunta 2

**Propón al menos 5 acciones concretas que Liza y Juan debería implementar para aplicar hardening al servidor. Relaciónalas con las mejores prácticas vistas.**

Para asegurar el servidor institucional de la Universidad San Marino de manera integral, el equipo de TI debe ejecutar un conjunto de medidas técnicas estructuradas:

#### Bastionado del servicio OpenSSH y control estricto de accesos administrativos

Se debe restringir el acceso a la cuenta `root` editando el archivo `/etc/ssh/sshd_config` con la directiva `PermitRootLogin no`. Las sesiones administrativas deben gestionarse exclusivamente a través de cuentas de usuario nominales autenticadas mediante pares de llaves criptográficas asimétricas robustas (Ed25519 o RSA de al menos 4096 bits), deshabilitando por completo la autenticación por contraseña tradicional (`PasswordAuthentication no`). Para mitigar escaneos globales y ataques de fuerza bruta automatizados, se debe configurar una lista de control de acceso por red (permitiendo conexiones SSH únicamente desde la subred de gestión o la VPN de la universidad) y desplegar un servicio de prevención de intrusiones a nivel de host como Fail2ban. Esta acción se fundamenta en las mejores prácticas de autenticación robusta y principio de menor privilegio.

#### Supresión de servicios inseguros y reactivación del cortafuegos nativo (UFW)

Se deben desinstalar y purgar de inmediato los paquetes correspondientes a Telnet y servidores FTP no seguros (como `telnetd`, `vsftpd` o `proftpd`), reemplazando cualquier necesidad de transferencia de archivos por protocolos seguros cifrados como SFTP o SCP montados sobre el canal SSH. A nivel perimetral, se debe reactivar el cortafuegos mediante `ufw enable`, estableciendo una política por defecto de denegación del tráfico entrante (`ufw default deny incoming`) y de aceptación para el tráfico saliente (`ufw default allow outgoing`). Luego, se deben abrir únicamente los puertos estrictamente indispensables para el propósito del servidor (por ejemplo, el puerto TCP 443 para HTTPS y el puerto de administración SSH restringido por origen), reduciendo al mínimo indispensable la superficie de ataque del sistema.

#### Automatización de parches y gestión del ciclo de vida de vulnerabilidades

Es prioritario sincronizar y actualizar el sistema base ejecutando `apt-get update && apt-get dist-upgrade` para mitigar todos los fallos y vulnerabilidades de seguridad acumulados desde la instalación inicial. Adicionalmente, se debe instalar y parametrizar el paquete `unattended-upgrades`, asegurando que las actualizaciones críticas y los parches de seguridad del kernel y de los paquetes oficiales de Canonical se descarguen e instalen de forma automática sin requerir intervención manual constante. Esto materializa la práctica recomendada de mantenimiento preventivo y mitigación de ventanas de exposición ante exploits de día cero o públicos.

#### Bóveda de secretos y aseguramiento de archivos de configuración

Se deben eliminar de inmediato todas las credenciales en texto plano consignadas en los ficheros de la aplicación web. La mejor práctica dicta delegar el aprovisionamiento de credenciales a variables de entorno inyectadas dinámicamente en tiempo de ejecución o recurrir a gestores de secretos dedicados (como HashiCorp Vault o servicios de gestión de claves). En el sistema de archivos local, los archivos de configuración sensibles deben pertenecer exclusivamente al usuario del servicio web con permisos de acceso hiper-restringidos (como `chmod 600` o `chmod 640`), impidiendo que cualquier usuario no privilegiado o proceso externo del sistema operativo pueda leer su contenido.

#### Implementación de auditoría avanzada, rotación de logs y política de respaldos 3-2-1

Se debe desplegar y activar el demonio de auditoría del kernel (`auditd`) junto a la configuración persistente de `systemd-journald` o `rsyslog`, asegurando el registro detallado de eventos de autenticación, escalamiento de privilegios vía `sudo`, accesos a archivos críticos del sistema y modificaciones en el cortafuegos. Asimismo, se debe configurar `logrotate` con compresión periódica (`compress`) para garantizar la disponibilidad del espacio en disco y remitir las bitácoras a un servidor de recolección externo (_Syslog remoto / SIEM_) para salvaguardar su integridad contra manipulación. Por último, se debe formalizar una política de copias de seguridad basada en el estándar 3-2-1 (tres copias de los datos, en dos medios distintos, con al menos una copia fuera del sitio o cifrada en la nube), respaldando la base de datos de la plataforma y sus configuraciones periódicamente.

#### Depuración de identidades huérfanas y gobernanza de privilegios

Se debe realizar una auditoría sobre `/etc/passwd` y `/etc/shadow` para deshabilitar o eliminar las cuentas que no hayan registrado actividad durante los últimos meses, empleando comandos como `usermod -L` (para bloquear contraseñas) o asignando la shell `/usr/sbin/nologin`. Para prevenir la acumulación futura de cuentas zombies, se debe configurar en `/etc/default/useradd` el bloqueo automático de cuentas inactivas tras 30 días de caducidad de la clave, acompañado de una revisión trimestral formal de los usuarios pertenecientes al grupo administrativo `sudo`, cumpliendo con el principio de segregación de funciones.

---

### Pregunta 3

**Clasifica las acciones propuestas según los siguientes aspectos del triángulo CID (Confidencialidad, Integridad, Disponibilidad). Para responder esta pregunta ayúdate con una tabla donde en una columna esté situación encontrada, qué pilar afecta del triángulo CID, nivel de riesgo y solución propuesta.**

A continuación se consolida la matriz de riesgos y clasificación según los pilares fundamentales del triángulo de seguridad de la información:

|Situación encontrada|Pilar afectado del triángulo CID|Nivel de riesgo|Solución propuesta|
|---|---|---|---|
|**Acceso SSH como root habilitado desde cualquier IP (`0.0.0.0/0`)**|Confidencialidad, Integridad y Disponibilidad|**Crítico**|Deshabilitar el ingreso directo de root (`PermitRootLogin no`) en `/etc/ssh/sshd_config`, forzar autenticación exclusiva mediante llaves públicas (desactivar contraseñas) y restringir el acceso por IP/red corporativa.|
|**Cuentas de usuario inactivas por más de 6 meses**|Confidencialidad e Integridad|**Medio**|Auditar y deshabilitar o eliminar cuentas obsoletas; configurar caducidad automática de cuentas tras 30 días de inactividad (`useradd -D -f 30`) y deshabilitar shells interactivas (`/usr/sbin/nologin`) para usuarios de servicio.|
|**Servicios FTP y Telnet ejecutándose sin cifrado**|Confidencialidad e Integridad|**Alto**|Detener, deshabilitar y desinstalar los paquetes de Telnet y FTP (`telnetd`, `vsftpd`); sustituir la transferencia de datos por SFTP/SCP asegurado sobre SSH.|
|**Cortafuegos del sistema operativo deshabilitado**|Confidencialidad, Integridad y Disponibilidad|**Crítico**|Habilitar UFW (`ufw enable`), establecer política predeterminada de bloqueo entrante (`default deny incoming`) y abrir únicamente los puertos requeridos (TCP 443 para HTTPS y puerto SSH administrativo filtrado).|
|**Sistema operativo sin actualizaciones ni parches desde la instalación**|Confidencialidad, Integridad y Disponibilidad|**Crítico**|Realizar actualización inmediata de paquetes del sistema (`apt update && apt dist-upgrade`) y habilitar el servicio de parches automáticos de seguridad mediante `unattended-upgrades`.|
|**Ausencia de registros (_logging_) y de rotación activa de logs**|Integridad y Disponibilidad|**Alto**|Configurar `systemd-journald` y `rsyslog` para registrar eventos del sistema de forma persistente, activar el servicio `auditd` para auditar accesos de seguridad, y parametrizar `logrotate` para prevenir desbordamientos de disco.|
|**Contraseñas almacenadas en texto plano en archivos de configuración web**|Confidencialidad|**Crítico**|Extraer las credenciales de los ficheros de código; inyectarlas mediante variables de entorno protegidas o un gestor de secretos, y ajustar permisos de lectura estrictos (`chmod 600`) asignados únicamente al usuario de la aplicación.|
|**Falta de política de revisión periódica de privilegios y ausencia de copias de seguridad (_backups_)**|Integridad y Disponibilidad|**Alto**|Establecer un procedimiento trimestral de revisión de privilegios en `sudoers`; implementar una arquitectura de respaldo 3-2-1 con copias automatizadas y cifradas de base de datos y configuraciones fuera del servidor.|

---

### Pregunta 4

**Utilizando el benchmark adecuado del CIS, identifica para cada aspecto a resolver en este servidor qué recomendación sería la adecuada ubicando página, título, análisis de cómo la recomendación ayuda a solucionar la vulnerabilidad.**

Para dar solución a los hallazgos en la plataforma de la Universidad San Marino bajo **Ubuntu Server 24.04 LTS**, se utiliza el estándar **CIS Ubuntu Linux 24.04 LTS Benchmark v1.0.0** (publicado oficialmente por el Center for Internet Security para la distribución _Noble Numbat_):

#### Exposición del servicio SSH y acceso remoto directo del usuario root

- **Referencia CIS:** Sección 5.2.16 (Pág. 432 aprox.)
- **Título oficial:** _Ensure SSH PermitRootLogin is disabled_
- **Análisis técnico:** Esta recomendación instruye configurar el parámetro `PermitRootLogin no` en `/etc/ssh/sshd_config` o dentro de `/etc/ssh/sshd_config.d/`. Al deshabilitar el inicio de sesión remoto para la cuenta de superusuario, se neutraliza la posibilidad de que un atacante capture control absoluto del sistema atacando un usuario estandarizado. Los administradores se ven obligados a autenticarse primero con una cuenta nominal (trazable en bitácoras mediante _sudo_) y utilizando autenticación asimétrica por llaves criptográficas, garantizando la rendición de cuentas y frenando los ataques de diccionario y fuerza bruta en internet.

#### Cuentas de usuario sin actividad durante períodos prolongados

- **Referencia CIS:** Sección 5.4.1.4 (Pág. 468 aprox.)
- **Título oficial:** _Ensure inactive password lock is 30 days or less_
- **Análisis técnico:** La recomendación exige establecer que cualquier cuenta cuya contraseña haya expirado y permanezca sin uso durante 30 días sea deshabilitada automáticamente por el sistema (`useradd -D -f 30` y actualización del parámetro `INACTIVE` en `/etc/default/useradd`). Esta directiva aborda de raíz la problemática de las cuentas huérfanas en la universidad, garantizando que si un colaborador o estudiante abandona la institución o no se conecta en más de 6 meses, su acceso sea revocado de manera desatendida, impidiendo que credenciales olvidadas se utilicen como puntos de apoyo para persistencia de intrusos.

#### Servicios inseguros heredados en ejecución (Telnet y FTP)

- **Referencia CIS:** Sección 2.1.2 y Sección 2.2.9 (Págs. 194 y 212 aprox.)
- **Título oficial:** _Ensure telnet client/server is not installed_ / _Ensure FTP Server is not installed_
- **Análisis técnico:** El benchmark establece la eliminación total de los paquetes de Telnet (`apt purge telnetd telnet`) y de servidores FTP (`vsftpd`, `proftpd`, `pure-ftpd`). Al purgar estos paquetes se suprimen del espacio de usuario procesos expuestos a exploits remotos y se erradica la transmisión de credenciales y datos en claro por la red de la universidad. Esta acción fuerza el uso de protocolos cifrados de la suite SSH (SFTP/SCP), impidiendo la intercepción de tráfico y reduciendo la superficie de exposición del servidor.

#### Desactivación del cortafuegos nativo

- **Referencia CIS:** Sección 3.4.1.1 y Sección 3.4.1.3 (Págs. 265 y 268 aprox.)
- **Título oficial:** _Ensure ufw is installed_ / _Ensure ufw service is enabled_
- **Análisis técnico:** Estas recomendaciones orientan la verificación, instalación y activación persistente de _Uncomplicated Firewall_ (`ufw enable`). El benchmark exige definir una política base de descarte (_default drop/deny_ para tráfico entrante) y permitir únicamente las comunicaciones expresamente autorizadas por puertos aprobados. La habilitación del cortafuegos restablece la primera línea de defensa perimetral del host, bloqueando intentos de sondeo no autorizado, aislando demonios internos que escuchen en interfaces locales e impidiendo la interacción externa con puertos vulnerables.

#### Carencia de parches y obsolescencia del sistema operativo

- **Referencia CIS:** Sección 1.8 (Pág. 148 aprox.)
- **Título oficial:** _Ensure updates, patches, and additional security software are installed_
- **Análisis técnico:** CIS determina que el gestor de paquetes de la distribución debe estar sincronizado con los repositorios oficiales de parches y que todas las actualizaciones de seguridad deben desplegarse regularmente. El cumplimiento de este control mediante `apt-get dist-upgrade` y la activación del servicio `unattended-upgrades` cierra brechas de seguridad conocidas y documentadas en la base de datos nacional de vulnerabilidades (NVD/CVE). Esto previene que adversarios utilicen exploits automatizados para comprometer el kernel o librerías de enlace dinámico de Ubuntu 24.04 LTS.

#### Ausencia de logging y de rotación activa de bitácoras

- **Referencia CIS:** Sección 4.1.1.1, Sección 4.2.1.2 y Sección 4.2.2 (Págs. 325, 342 y 360 aprox.)
- **Título oficial:** _Ensure auditd service is enabled_ / _Ensure journald is configured to compress large log files_ / _Ensure logrotate is configured_
- **Análisis técnico:** Este conjunto de recomendaciones articula el marco de auditoría integral del host. La activación de `auditd` permite monitorizar eventos del kernel tales como modificaciones de permisos, accesos privilegiados y llamadas al sistema sensibles. A su vez, la parametrización de `journald` y `logrotate` asegura que los registros del sistema se organicen, se compriman y se retengan de forma predecible sin llenar el sistema de archivos. Esto garantiza la trazabilidad forense ante un incidente y resguarda la disponibilidad del almacenamiento.

#### Almacenamiento inseguro de credenciales y permisos excesivos

- **Referencia CIS:** Sección 6.1.10 y Sección 6.2.1 (Págs. 495 y 510 aprox.)
- **Título oficial:** _Ensure permissions on sensitive system configuration files are configured_
- **Análisis técnico:** Si bien la gestión de credenciales en aplicaciones web también involucra estándares a nivel de software (OWASP / CIS Web Application Benchmark), el CIS Ubuntu Benchmark establece reglas estrictas de permisos (`0600` o `0640`) y propiedad de usuario/grupo (`root:root` o usuarios dedicados de servicio) para cualquier archivo de configuración crítica en el sistema de archivos local. Esta recomendación mitiga el riesgo de que usuarios locales sin privilegios o procesos de red comprometidos puedan leer credenciales confidenciales en reposo.

#### Inexistencia de revisión periódica de privilegios y control de sudoers

- **Referencia CIS:** Sección 5.3.4 y Sección 5.3.7 (Págs. 445 y 452 aprox.)
- **Título oficial:** _Ensure sudo commands use pty_ / _Ensure sudo authentication timeout is configured_
- **Análisis técnico:** Estas recomendaciones exigen que la delegación de privilegios administrativos sea controlada meticulosamente a través de `sudo`, garantizando que la elevación de permisos requiera autenticación individual, se ejecute dentro de un pseudo-terminal para evitar ataques de inyección y caduque tras un período de inactividad breve (reduciendo la ventana de tiempo de una sesión desatendida). Esto complementa las políticas institucionales de auditoría de cuentas con privilegios administrativos.

---

### Pregunta 5

**Para el punto anterior ayúdate con chatgpt y entrega el prompt que diseñaste para que te ayudara a resolver esta pregunta.**

Para resolver la identificación precisa de las recomendaciones normativas en el benchmark oficial, se diseñó un prompt técnico avanzado aplicando técnicas de ingeniería de prompts (_Role Prompting_, delimitación de contexto, inyección de restricciones y formato estructurado en Markdown):

```markdown
Actúa como un Auditor Senior de Ciberseguridad y Especialista en DevSecOps con certificación 
en endurecimiento de infraestructura Linux y amplio dominio de los estándares del Center 
for Internet Security (CIS Benchmarks).

Contexto del caso práctico:
Nos encontramos en un laboratorio de aseguramiento para un servidor institucional recientemente 
adquirido por la Universidad San Marino. El servidor corre Ubuntu Server 24.04 LTS y tras 
una auditoría de seguridad se encontraron las siguientes vulnerabilidades críticas:
1. Acceso remoto por SSH permitido directamente como root desde cualquier IP (0.0.0.0/0).
2. Cuentas de usuario sin actividad ni inicio de sesión desde hace más de 6 meses.
3. Servicios heredados e inseguros como FTP y Telnet en ejecución (sin uso real).
4. El cortafuegos nativo del sistema operativo se encuentra completamente deshabilitado.
5. El sistema operativo no ha recibido actualizaciones ni parches de seguridad desde su instalación.
6. Ausencia de registro de logs del sistema y de rotación activa de bitácoras.
7. Contraseñas de la aplicación web almacenadas en texto plano en archivos de configuración.
8. No hay políticas de revisión periódica de privilegios ni esquemas de backup.

Instrucciones:
Utilizando como referencia técnica oficial el documento "CIS Ubuntu Linux 24.04 LTS Benchmark v1.0.0", 
identifica para cada uno de los aspectos problemáticos la recomendación normativa correspondiente. 
Presenta la información de forma técnica, detallada y estructurada con los siguientes parámetros:
- Número de sección y recomendación formal del benchmark CIS.
- Título oficial en inglés de la directiva según el benchmark.
- Número de página aproximado dentro de la guía oficial.
- Análisis técnico detallado explicando cómo la aplicación de dicha recomendación corrige la 
  vulnerabilidad, mitiga vectores de ataque concretos y reduce la superficie de exposición del sistema.
- Comandos de auditoría e implementación recomendados en Bash para entornos Debian/Ubuntu.

Restricciones:
- Mantén un lenguaje formal de auditoría de ciberseguridad.
- Evita explicaciones superficiales; fundamenta cada recomendación en principios de Confidencialidad, 
  Integridad, Disponibilidad y Menor Privilegio.
```

Este diseño garantiza que el modelo genere un mapeo exacto, eliminando ambigüedades sobre las directivas de CIS para la distribución Noble Numbat (Ubuntu 24.04) y proporcionando una base sólida para la justificación técnica de las soluciones.

---

## Conclusiones y Aprendizajes

La evaluación y propuesta de aseguramiento para el servidor de la Universidad San Marino evidencia que la operatividad de un servicio no puede estar desligada de la seguridad de la información. El caso evidencia el antipatrón clásico en administración de sistemas donde se opta por deshabilitar mecanismos protectores —como el cortafuegos— con el fin de facilitar la conectividad rápida de servicios, comprometiendo de manera severa los tres pilares del triángulo CID. El endurecimiento de sistemas (_hardening_) no constituye un procedimiento estático o aislado que se ejecuta únicamente tras la instalación, sino un proceso de gestión continua que debe alinearse con marcos de referencia probados como los CIS Benchmarks, garantizando la reducción sistemática de la superficie de ataque y la contención de amenazas contemporáneas.

El desarrollo de este laboratorio aportó aprendizajes clave en la formación profesional en ciberseguridad:

- La comprensión aplicada de cómo configuraciones por defecto permisivas crean vectores de ataque inmediatos, reforzando la necesidad de aplicar desde el primer momento el principio de menor privilegio y la defensa en profundidad en servidores productivos.
- La importancia de adoptar estándares de la industria, reconociendo en los CIS Benchmarks una herramienta indispensable para estructurar auditorías reproducibles y parametrizar sistemas operativos con directivas claras y auditables.
- La interiorización de que protocolos sin cifrado en tránsito (como FTP y Telnet) y el almacenamiento de contraseñas en texto plano anulan la confidencialidad y facilitan el escalamiento de privilegios, demandando la adopción mandataria de criptografía asimétrica para administración remota y gestores de secretos en capas de aplicación.
- La valorización del registro (_logging_), la auditoría del kernel (`auditd`) y las estrategias de respaldo como mecanismos insustituibles para la resiliencia operativa, el no repudio y la respuesta efectiva ante incidentes de seguridad.

