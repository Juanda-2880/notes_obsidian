
**Universidad Icesi — Facultad Barbiere de Ingeniería, Diseño y Ciencias Aplicadas** **Asignatura:** Ciberseguridad
**Integrantes:**

- Juan David Pacheco Vargas — A00401844
- Esteban Guarin Valencia — A00402028
---

## 1. Introducción

Este informe documenta la ejecución de la segunda mitad de la práctica de laboratorio No. 1, correspondiente a:

1. El uso del cliente gráfico **WinSCP** para efectuar transferencia segura de archivos entre una máquina Windows y una máquina virtual **Lubuntu**, mediante los protocolos **SCP** y **SFTP**, verificando su seguridad con **Wireshark**.
2. La configuración de **autenticación de doble factor (2FA)** en el servicio SSH del servidor Lubuntu, empleando una contraseña convencional junto con una **contraseña de un solo uso basada en tiempo (TOTP)** generada por la aplicación **Google Authenticator**.

Se presenta el procedimiento realizado paso a paso, la evidencia fotográfica de cada etapa y las respuestas a las preguntas de análisis planteadas en la guía.

---

## 2. Verificación de conectividad entre las máquinas

Antes de iniciar la transferencia de archivos, fue necesario confirmar que ambas máquinas virtuales (Lubuntu y Windows) contaran con direccionamiento IP válido dentro de la misma red, y que existiera conectividad entre ellas.

### 2.1 Dirección IP de la máquina Lubuntu

Se ejecutó el comando `ip addr` (o `ifconfig`) en la terminal de la máquina Lubuntu para obtener la dirección IP asignada al servidor SSH.

![[WinSCP-1789414315273.webp]]

### 2.2 Dirección IP de la máquina Windows

De forma análoga, se consultó la configuración de red de la máquina Windows mediante el comando `ipconfig`, con el fin de identificar la dirección IP del cliente que se conectará al servidor.

![[WinSCP-1789414336137.webp]]

### 2.3 Prueba de conectividad (ping)

Con ambas direcciones IP identificadas, se realizó una prueba de conectividad desde la máquina Lubuntu hacia la máquina Windows mediante el comando `ping`, confirmando que existía comunicación a nivel de red entre ambos equipos antes de establecer la sesión SSH/SFTP.

![[WinSCP-1789414366311.webp]]

---

## 3. Uso de WinSCP para transferencia segura de archivos

### 3.1 Instalación y primer inicio de WinSCP

Se descargó e instaló WinSCP en la máquina Windows, seleccionando durante la instalación la interfaz tipo **"Commander"**, la cual presenta los directorios del equipo local y del equipo remoto lado a lado, facilitando la transferencia de archivos mediante arrastrar y soltar.

Al iniciar la aplicación por primera vez, se mostró el cuadro de inicio de sesión, donde se debieron diligenciar los campos correspondientes al host remoto:

![[WinSCP-1789414384943.webp]]

### 3.2 Conexión de WinSCP con la máquina Lubuntu

En el campo **Host name** se introdujo la dirección IP de la máquina Lubuntu (obtenida en la sección 2.1), y en el campo **User name** el nombre de la cuenta de usuario configurada en el servidor. Al presionar **Login**, WinSCP realizó el proceso de _handshake_ SSH-2 y la autenticación correspondiente, estableciendo la sesión y desplegando la vista de archivos local/remoto.

![[WinSCP-1789414417816.webp]]

### 3.3 Verificación del protocolo y algoritmo de cifrado empleados

Una vez establecida la conexión, se consultó la información técnica de la sesión mediante la opción **Session → Server/Protocol Information**, la cual reporta el protocolo de sesión, la implementación de SSH del servidor, el algoritmo de cifrado negociado y el protocolo de transferencia de archivos en uso.

![[WinSCP-1789414438092.webp]]

---

## 4. Preguntas de análisis — WinSCP

### Pregunta 5. ¿Qué algoritmo de cifrado están empleando su cliente WinSCP y el servidor Linux?

De acuerdo con la información reportada en el cuadro **Server and Protocol Information** (sección 3.3), el algoritmo de cifrado simétrico negociado entre el cliente WinSCP y el servidor OpenSSH corresponde a una variante de **AES (Advanced Encryption Standard)**. Este algoritmo es seleccionado automáticamente durante la fase de negociación del _handshake_ SSH-2, en la cual el cliente y el servidor intercambian sus listas de algoritmos soportados (en orden de preferencia) y se acuerda el primero que ambas partes tengan en común. AES es hoy uno de los cifrados por bloques más robustos y ampliamente adoptados, lo que explica su uso por defecto tanto en el cliente como en el servidor de esta práctica.

### Pregunta 6. Transferencia de un archivo de texto y captura con Wireshark: ¿qué protocolo de capa de aplicación se emplea?

Se creó un archivo de texto plano en la máquina Windows y se transfirió a la máquina Lubuntu utilizando la interfaz de WinSCP, mientras se capturaba simultáneamente el tráfico de red con Wireshark, filtrando por la dirección IP del servidor.

Al analizar la captura, se observó que, tras el _handshake_ inicial de SSH-2 (idéntico en estructura al estudiado en la primera parte del laboratorio), la totalidad de la sesión —incluyendo la negociación de la transferencia y el envío del archivo— se transporta encapsulada dentro del **protocolo SSH**, específicamente utilizando el subsistema **SFTP (SSH File Transfer Protocol)**, que es el protocolo de transferencia configurado por defecto en WinSCP (visible también en el campo _File transfer protocol_ del cuadro de la sección 3.3). A diferencia de FTP tradicional, SFTP no abre un canal de datos independiente en texto claro, sino que reutiliza el mismo canal cifrado establecido por SSH para transmitir tanto los comandos de manejo de archivos como su contenido.

### Pregunta 7. ¿Es posible ver texto plano más allá del intercambio inicial? Conclusión sobre la seguridad del protocolo

Más allá de los paquetes correspondientes al intercambio inicial de algoritmos y claves (que por diseño del protocolo SSH viajan sin cifrar, ya que aún no existe una clave de sesión), **no fue posible observar en texto plano ningún fragmento del contenido transferido**, ni el nombre completo de las rutas, ni el texto escrito en el archivo de prueba. Todo el tráfico posterior al establecimiento de la clave de sesión se encuentra cifrado con el algoritmo simétrico negociado (AES), y su integridad se protege mediante los algoritmos de autenticación de contenido (HMAC) acordados en el mismo _handshake_.

**Conclusión:** SFTP, al operar sobre el canal cifrado de SSH, ofrece un nivel de seguridad muy superior al de protocolos de transferencia de archivos tradicionales como FTP, los cuales transmiten tanto las credenciales como los datos en texto claro. La confidencialidad (mediante cifrado simétrico), la integridad (mediante códigos de autenticación de mensaje) y la autenticación del servidor (mediante su clave pública/_host key_) que provee SSH se heredan automáticamente en toda sesión SFTP, lo que convierte a WinSCP en una herramienta adecuada para la transferencia de archivos sensibles en entornos de producción.

---

## 5. Configuración de autenticación de doble factor con token por software (Google Authenticator)

Una vez validado el funcionamiento de WinSCP, se procedió a configurar en el servidor Lubuntu un segundo factor de autenticación basado en **contraseñas de un solo uso basadas en tiempo (TOTP)**, generadas por la aplicación **Google Authenticator**. Este esquema combina:

- Un factor de **conocimiento**: la contraseña habitual de la cuenta.
- Un factor de **posesión**: el código TOTP generado por la aplicación instalada en el dispositivo móvil del usuario.

### 5.1 Instalación de dependencias de compilación

Utilizando la cuenta `user` (con privilegios administrativos vía `sudo`), se actualizaron los catálogos de paquetes y se instalaron las librerías necesarias para compilar el módulo PAM de Google Authenticator:

```bash
sudo apt-get update
sudo apt-get install libpam0g-dev make autoconf libtool libqrencode-dev
```

![[WinSCP-1789414486492.webp]]

### 5.2 Descarga del código fuente de Google Authenticator

Se descargó el paquete de código fuente del proyecto _google-authenticator-libpam_ directamente desde el repositorio oficial en GitHub:

```bash
wget https://github.com/google/google-authenticator-libpam/archive/master.zip
```

![[WinSCP-1789414582318.webp]]

### 5.3 Descompresión del paquete fuente

El archivo descargado se renombró y descomprimió para acceder a su contenido:

```bash
mv master.zip googleauth.zip
unzip googleauth.zip
cd google-authenticator-libpam-master
```

![[WinSCP-1789414609949.webp]]

### 5.4 Compilación e instalación del módulo

Se ejecutó la secuencia estándar de compilación (`bootstrap`, `configure`, `make`) para generar e instalar el módulo PAM:

```bash
./bootstrap.sh
./configure
make
sudo make install
```

![[WinSCP-1789414645693.webp]]

### 5.5 Configuración de PAM para el servicio SSH

Se editó el archivo `/etc/pam.d/sshd` para incorporar el módulo de Google Authenticator como requisito adicional de autenticación, agregando la línea `auth required pam_google_authenticator.so` antes de la directiva de autenticación estándar de Unix:

```bash
sudo nano /etc/pam.d/sshd
```

![[WinSCP-1789414676200.webp]]

### 5.6 Habilitación de autenticación por desafío-respuesta en SSH

Posteriormente, se modificó el archivo de configuración del demonio SSH (`/etc/ssh/sshd_config`), cambiando el parámetro `ChallengeResponseAuthentication` de `no` a `yes`, con el fin de permitir que el servicio solicite el código TOTP durante el proceso de autenticación. El cambio se aplicó reiniciando el servicio:

```bash
sudo nano /etc/ssh/sshd_config
sudo service ssh restart
```

![[WinSCP-1789414718805.webp]]

### 5.7 Generación de la clave secreta TOTP

Ya dentro de la cuenta de usuario que utilizaría el segundo factor, se ejecutó el asistente de configuración `google-authenticator`, el cual generó una clave secreta única, su representación como código QR, y cinco códigos de emergencia (_scratch codes_) para casos en que el dispositivo móvil no esté disponible.

![[WinSCP-1789414810985.webp]]

### 5.8 Parámetros de seguridad seleccionados

Durante el asistente se respondieron las siguientes preguntas de configuración, priorizando la seguridad del esquema:

|Pregunta|Respuesta|Justificación|
|---|---|---|
|¿Tokens basados en tiempo (TOTP)?|**Sí (y)**|Es el estándar solicitado por la práctica y por Google Authenticator.|
|¿Actualizar `~/.google_authenticator`?|**Sí (y)**|Necesario para persistir la clave secreta y los parámetros.|
|¿Restringir el uso múltiple del mismo token?|**Sí (y)**|Evita ataques de repetición (_replay_) y mitiga el riesgo de intercepción tipo _man-in-the-middle_.|
|¿Ampliar la ventana de sincronización (de 3 a 17 códigos válidos)?|**No (n)**|Los relojes de los equipos y del teléfono están correctamente sincronizados; una ventana más amplia debilitaría la seguridad del esquema.|
|¿Habilitar limitación de intentos (_rate-limiting_)?|**Sí (y)**|Protege contra ataques de fuerza bruta sobre el código TOTP.|

![[WinSCP-1789414844896.webp]]

### 5.9 Instalación de Google Authenticator en el dispositivo móvil

Se instaló la aplicación **Google Authenticator** en un dispositivo Android desde la Play Store, y se agregó la cuenta mediante el ingreso manual de la clave secreta generada en el paso 5.7 (opción _Enter provided key_), evitando así escanear el código QR desde la URL pública generada por el asistente, práctica desaconsejada por exponer la clave secreta a un servicio externo.

![[WinSCP-1789414864245.webp]]

### 5.10 Verificación del funcionamiento del esquema de doble factor

Finalmente, se estableció una conexión SSH desde la máquina Windows hacia la máquina Lubuntu utilizando la cuenta configurada. El sistema solicitó, en orden, el **Verification Code** (código TOTP generado en el dispositivo móvil) y la **contraseña** habitual de la cuenta, otorgando acceso únicamente al superar ambos factores de autenticación.

![[WinSCP-1789414905331.webp]]

---

## 6. Preguntas de análisis — Google Authenticator

### Pregunta 8. Ventajas y desventajas del sistema de autenticación de doble factor con token por software

**Ventajas:**

1. **Mayor resistencia frente al robo o adivinanza de la contraseña.** Aunque un atacante logre obtener la contraseña de la cuenta (por _phishing_, fuerza bruta o filtración de una base de datos), no podrá autenticarse sin el código TOTP vigente, que cambia cada 30 segundos y solo puede generarse a partir de la clave secreta almacenada en el dispositivo del usuario legítimo.
2. **Bajo costo de implementación.** A diferencia de un token físico dedicado (como una llave USB de seguridad), este esquema no requiere hardware adicional: basta con un teléfono inteligente y una aplicación gratuita, lo que facilita su adopción a gran escala.

**Desventajas:**

1. **Dependencia de la sincronización de reloj.** El correcto funcionamiento del esquema depende de que el reloj del servidor y el del dispositivo móvil estén razonablemente sincronizados; un desfase mayor a la ventana de tolerancia configurada puede impedir la autenticación de un usuario legítimo.
2. **Riesgo de pérdida de acceso ante pérdida del dispositivo.** Si el usuario pierde el teléfono o este se daña sin haber respaldado la clave secreta ni los códigos de emergencia, queda sin forma de generar el segundo factor, lo que puede derivar en la pérdida total de acceso a la cuenta (mitigado parcialmente por los _scratch codes_, pero estos son de un solo uso y en cantidad limitada).

### Pregunta 9. Conclusiones

- La combinación de un factor de conocimiento (contraseña) con un factor de posesión (token TOTP) reduce significativamente la superficie de ataque frente a esquemas de autenticación basados en un único factor, ya que obliga al atacante a comprometer simultáneamente dos elementos de naturaleza distinta.
- El protocolo SSH, al negociar dinámicamente algoritmos de cifrado e integridad durante su _handshake_, permite incorporar mecanismos de autenticación adicionales (como PAM y Google Authenticator) sin sacrificar la confidencialidad del canal de comunicación.
- Herramientas como WinSCP demuestran que la seguridad ofrecida por SSH no se limita al acceso interactivo por terminal, sino que se extiende de forma transparente a la transferencia de archivos mediante SFTP, heredando las mismas garantías de cifrado e integridad.
- La correcta configuración de un servidor SSH robusto requiere de un enfoque de defensa en profundidad: deshabilitar protocolos obsoletos (SSH v1), restringir la autenticación por contraseña cuando sea posible, y complementar el acceso con factores adicionales como TOTP.

### Pregunta 10. ¿Qué aprendieron en este laboratorio?

En esta práctica se consolidó la comprensión del funcionamiento interno del protocolo SSH y de los servicios que se apoyan en él para la transferencia segura de archivos (SCP/SFTP), así como la capacidad práctica de configurar un esquema de autenticación de doble factor mediante software (TOTP) sobre un servidor Linux. Se evidenció, mediante el análisis de tráfico con Wireshark, la diferencia entre la información que viaja en texto claro (propia del intercambio inicial de algoritmos) y la que viaja cifrada una vez establecida la clave de sesión, reforzando la importancia de emplear protocolos cifrados en cualquier escenario de transmisión de credenciales o datos sensibles sobre una red.
