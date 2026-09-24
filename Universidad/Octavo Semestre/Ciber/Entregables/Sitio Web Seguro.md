**Curso:** Ciberseguridad  
**Integrantes:**
* **Juan David Pacheco Vargas** — Código: `A00401844`
* **Esteban Guarín Valencia** — Código: `A00402028`

**Entorno de Práctica:**
* **Sistema Operativo:** Lubuntu 16.04 LTS (i386) en máquina virtual
* **Dirección IP del Servidor:** `10.0.2.15/24` (Interfaz `enp0s3`)
* **Servidor Web:** Apache HTTP Server 2.4.18
* **Herramientas de Seguridad y Análisis:** OpenSSL 1.0.2g, Wireshark 2.6.10, Mozilla Firefox

---

## 1. RESUMEN

En el presente laboratorio se diseñó, implementó y evaluó una arquitectura de navegación web segura mediante el protocolo **HTTPS** (HTTP sobre TLS/SSL). Para ello, se estableció una **Autoridad Certificadora (CA)** raíz local sobre un servidor Linux Lubuntu, generando su respectivo par de llaves criptográficas RSA de 4096 bits protegidas mediante el algoritmo simétrico AES-256 y un certificado raíz autofirmado bajo el estándar ITU-T X.509. Posteriormente, se emitió una Solicitud de Firma de Certificado (**CSR**) para el servidor web (`10.0.2.15`), la cual fue suscrita y validada por la CA creada.

Asimismo, se configuró el servidor **Apache2**, gestionando de forma segura las llaves privadas (eliminación de passphrase para arranque no interactivo y asignación de permisos Unix estrictos `400`), activando el módulo criptográfico `mod_ssl`, deshabilitando la compresión HTTP (`mod_deflate`) para mitigar vulnerabilidades de canal lateral y parametrizando los hosts virtuales para los puertos `80` (HTTP) y `443` (HTTPS). 

Finalmente, mediante el analizador de paquetes de red **Wireshark** y el navegador web **Mozilla Firefox**, se realizó un análisis comparativo del tráfico: se constató la vulnerabilidad crítica de intercepción de credenciales y payload en texto plano sobre HTTP, el mecanismo de protección del navegador ante autoridades no reconocidas (`SEC_ERROR_UNKNOWN_ISSUER`), la correcta validación de la cadena de confianza tras instalar el certificado raíz en el almacén de certificados, y la hermeticidad del canal seguro mediante el análisis detallado del protocolo de negociación (*TLS Handshake*) y el transporte de datos encapsulados en registros *Application Data*.

---

## 2. OBJETIVOS

### 2.1. Objetivo General
Implementar un entorno de comunicaciones web seguro mediante el despliegue de una Infraestructura de Clave Pública (PKI) local, la configuración del servidor web Apache sobre HTTPS y la comprobación de la confidencialidad e integridad del canal mediante análisis de tráfico de red.

### 2.2. Objetivos Específicos
1. Construir y parametrizar una Autoridad de Certificación (CA) interna en Linux empleando OpenSSL con cifrado asimétrico robusto.
2. Formular un requerimiento de certificado (CSR) para el servidor web y firmarlo digitalmente a través de la clave privada de la CA propia.
3. Desplegar y asegurar un servidor web Apache2 en Linux, configurando sus *Virtual Hosts* para operar sobre TLS/SSL en el puerto TCP 443.
4. Exportar e importar certificados X.509 en el almacén de confianza de Mozilla Firefox, comprendiendo el concepto de confianza transitiva y cadena de confianza (*Chain of Trust*).
5. Capturar e interpretar paquetes de red mediante Wireshark, comparando el intercambio en texto plano (HTTP) frente a la sesión cifrada y autenticada (HTTPS).
6. Analizar la secuencia del *TLS Handshake* (negociación de suites de cifrado, intercambio de parámetros, validación de certificados y conmutación a cifrado simétrico).

---

## 3. MARCO CONCEPTUAL

Para la ejecución y comprensión rigurosa de este laboratorio se fundamentaron los siguientes conceptos:

* **Criptografía Asimétrica (Llave Pública / Llave Privada):** Paradigma criptográfico basado en la asimetría matemática donde un par de llaves vinculadas cumple funciones complementarias: lo que cifra la llave pública solo puede ser descifrado por la llave privada correspondiente, y lo que firma la llave privada es verificable por cualquier entidad que posea la llave pública. En esta práctica se empleó el algoritmo **RSA** con longitudes de clave de $4096$ bits.
* **Criptografía Simétrica:** Mecanismo en el que emisor y receptor comparten un único secreto criptográfico para cifrar y descifrar la información. Es computacionalmente mucho más eficiente que la criptografía asimétrica, por lo cual TLS utiliza criptografía asimétrica para la autenticación y negociación del secreto, e inmediatamente transiciona a un cifrado simétrico (como AES) para la transferencia masiva de datos.
* **Infraestructura de Clave Pública (PKI) y Autoridad Certificadora (CA):** Marco de políticas, procesos y algoritmos que permiten vincular de manera fidedigna la identidad de una persona u organización con una clave pública. La CA actúa como un tercero de confianza (*Trusted Third Party*) que expide y firma digitalmente certificados.
* **Certificado Digital X.509:** Estándar internacional que define el formato de los certificados de clave pública. Contiene información crítica como el Sujeto (*Subject*), Emisor (*Issuer*), Periodo de Validez (*Not Before / Not After*), Clave Pública, Algoritmo de Firma y Extensiones (incluyendo el *Common Name* - CN o *Subject Alternative Name* - SAN).
* **Certificate Signing Request (CSR):** Bloque de texto codificado que contiene los datos del solicitante y su clave pública. Se remite a una CA para que esta verifique la identidad y genere el certificado final firmado con su clave privada.
* **Protocolo TLS (Transport Layer Security):** Sucesor del ya obsoleto SSL. Opera sobre la capa de transporte (TCP) y bajo la capa de aplicación (HTTP), proporcionando tres pilares esenciales:
  1. *Confidencialidad:* Cifrado del canal frente a escuchas pasivas (*eavesdropping*).
  2. *Integridad:* Detección de cualquier alteración en el tránsito de los datos mediante códigos de autenticación de mensajes (HMAC).
  3. *Autenticación:* Validación de la identidad del servidor (y opcionalmente del cliente) previniendo ataques de tipo Man-in-the-Middle (MitM).

---

## 4. DESARROLLO DE LA PRÁCTICA Y PROCEDIMIENTO PASO A PASO

### Fase 1: Inicialización, Reconocimiento y Preparación del Sistema

Se inició la máquina virtual con Lubuntu 16.04 ingresando con las credenciales de laboratorio (`user` / `password`).

![[Sitio Web Seguro-1790223047223.webp]]
*Figura 1: Inicio de sesión en la máquina virtual Lubuntu 16.04.*

A continuación, se consultó la configuración de red del equipo para determinar la dirección IP asignada a la interfaz Ethernet. La dirección IPv4 identificada fue `10.0.2.15/24` sobre la interfaz `enp0s3`. Este valor es fundamental, ya que debe coincidir con el *Common Name* (CN) de los certificados del servidor web para evitar incongruencias de identidad.

```bash
ip addr
```

![[Sitio Web Seguro-1790223047292.webp]]
*Figura 2: Verificación de la dirección IP asignada al host (10.0.2.15).*

Posteriormente, se actualizaron los índices locales de paquetes del gestor de software para garantizar la disponibilidad de las librerías requeridas:

```bash
sudo apt-get update
```

![[Sitio Web Seguro-1790223047294.webp]]
*Figura 3: Actualización de repositorios con apt-get update.*

Se procedió a la instalación/verificación de la suite criptográfica **OpenSSL**:

```bash
sudo apt-get install openssl
```

![[Sitio Web Seguro-1790223047328.webp]]
*Figura 4: Instalación y actualización de la herramienta OpenSSL.*

Se ingresó al shell con privilegios de superusuario (`root`) para construir un directorio de trabajo exclusivo para la gestión de certificados, asignándole permisos Unix restringidos `700` (`rwx------`), garantizando que ningún usuario no privilegiado tenga lectura o acceso al material criptográfico:

```bash
sudo su
cd /root
mkdir certs
chmod 700 certs
cd certs
```

![[Sitio Web Seguro-1790223047325.webp]]
*Figura 5: Creación del directorio seguro /root/certs con permisos 700.*

---

### Fase 2: Creación de la Autoridad de Certificación (CA)

Para erigir la CA, el primer paso consistió en generar su clave privada maestra (`ca.key`). Dicha clave se configuró con una longitud de 4096 bits mediante el algoritmo RSA y protegida contra almacenamiento en texto plano mediante cifrado AES de 256 bits, exigiendo una frase de paso (*passphrase*):

```bash
openssl genrsa -aes256 -out ca.key 4096
```

Seguidamente, se generó el certificado raíz autofirmado de la CA (`ca.crt`) con una vigencia de 365 días:

```bash
openssl req -new -x509 -days 365 -key ca.key -out ca.crt
```

Durante el diligenciamiento interactivo del Nombre Distinguido (*Distinguished Name* - DN), se suministraron los siguientes parámetros:
* **Country Name (2 letter code):** `CO`
* **State or Province Name:** `Valle del Cauca`
* **Locality Name:** `Cali`
* **Organization Name:** `Universidad Icesi`
* **Organizational Unit Name:** `Curso Ciberseguridad`
* **Common Name:** `CA del equipo 10.0.2.15`
* **Email Address:** `pachecoguarin@gmail.com`

![[Sitio Web Seguro-1790223047336.webp]]
*Figura 6: Generación de la clave privada de la CA (4096 bits) y expedición del certificado raíz autofirmado ca.crt.*

Para comprobar que la clave privada de la CA fue generada con los parámetros criptográficos correctos y constatar la exigencia de la frase de paso, se procedió a inspeccionarla:

```bash
openssl rsa -noout -text -in ca.key
```

![[Sitio Web Seguro-1790223047338.webp]]
*Figura 7: Volcado en texto claro de los componentes matemáticos (módulo y exponente) de la clave privada de la CA.*

De forma análoga, se analizó la estructura interna del certificado raíz generado (`ca.crt`):

```bash
openssl x509 -noout -text -in ca.crt
```

![[Sitio Web Seguro-1790223047287.webp]]
*Figura 8: Metadatos del certificado X.509 de la CA (Emisor, Validez, Sujeto y Algoritmo sha256WithRSAEncryption).*

---

### Fase 3: Generación del Certificado del Servidor Web

Con la CA operativa, se procedió a crear la identidad criptográfica para el servidor web Apache. En primer lugar, se generó la clave privada RSA de 4096 bits para el servidor:

```bash
openssl genrsa -aes256 -out server.key 4096
```

![[Sitio Web Seguro-1790223047334.webp]]
*Figura 9: Creación de la clave privada cifrada del servidor web (server.key).*

A partir de esta clave privada, se formuló la solicitud de firma de certificado (`server.csr`). En esta solicitud, el campo **Common Name (CN)** se definió taxativamente con la dirección IP del servidor (`10.0.2.15`), permitiendo que los clientes web vinculen la identidad del host con la IP a la cual se conectan:

```bash
openssl req -new -key server.key -out server.csr
```

* Parámetros ingresados: `CO` / `Valle del Cauca` / `Cali` / `Universidad Icesi` / `Ciberseguridad` / `CN=10.0.2.15` / `pachecoguarin@hotmail.com`.

![[Sitio Web Seguro-1790223047343.webp]]
*Figura 10: Diligenciamiento de la solicitud de firma de certificado (CSR) con CN=10.0.2.15.*

Se verificó el contenido de la solicitud `server.csr` para validar que los atributos del sujeto y la clave pública estuviesen debidamente estructurados:

```bash
openssl req -noout -text -in server.csr
```

![[Sitio Web Seguro-1790223047341.webp]]
*Figura 11: Inspección de los campos de la solicitud server.csr antes de ser sometida a la CA.*

Posteriormente, la CA procedió a firmar el requerimiento `server.csr` empleando su certificado `ca.crt` y su clave privada `ca.key`. Asimismo, se abordó una problemática operativa crucial: dado que la clave `server.key` original posee *passphrase*, el servidor web se bloquearía al iniciar requiriendo la contraseña de forma interactiva en la consola. Para solucionar esto y permitir el arranque automatizado de Apache como servicio del sistema, se extrajo una copia desprotegida de la clave (`server.key.insecure`) y se reemplazó por la original:

```bash
# Extracción de la clave sin frase de paso
openssl rsa -in server.key -out server.key.insecure
mv server.key server.key.secure
mv server.key.insecure server.key

# Firma del certificado del servidor por la CA
openssl x509 -req -days 365 -in server.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out server.crt
```

![[Sitio Web Seguro-1790223047335.webp]]
*Figura 12: Generación de la clave privada desprotegida y firma del certificado del servidor por parte de la CA.*

---

### Fase 4: Instalación y Configuración de Apache como Servidor Web Seguro

Se instaló el servidor web Apache en la máquina virtual:

```bash
sudo apt-get install apache2 -y
```

![[Sitio Web Seguro-1790223047340.webp]]
*Figura 13: Instalación del paquete del servidor web Apache2.*

Una vez instalado, se creó el directorio de almacenamiento seguro `/etc/apache2/ssl` con permisos `700`:

```bash
cd /etc/apache2
mkdir ssl
chmod 700 ssl
cd ssl
```

![[Sitio Web Seguro-1790223047247.webp]]
*Figura 14: Creación del directorio /etc/apache2/ssl para el alojamiento de certificados.*

Se copiaron la clave privada desprotegida (`server.key`) y el certificado firmado (`server.crt`) desde `/root/certs` hacia `/etc/apache2/ssl`, fijando permisos restrictivos `400` (`r--------`), evitando que otros procesos o usuarios del sistema tengan lectura de la clave privada:

```bash
cp /root/certs/server.key .
cp /root/certs/server.crt .
chmod 400 *
```

![[Sitio Web Seguro-1790223047250.webp]]
*Figura 15: Transferencia de server.key y server.crt con asignación de permisos 400.*

A nivel de configuración de módulos de Apache:
1. Se habilitó el soporte para SSL/TLS:
   ```bash
   a2enmod ssl
   ```
   ![[Sitio Web Seguro-1790223047257.webp]]
   *Figura 16: Habilitación del módulo mod_ssl en Apache.*

2. Se inhabilitó el módulo de compresión HTTP (`deflate`) para evitar ataques de canal lateral basados en compresión (tales como CRIME o BREACH) y facilitar la inspección pedagógica de paquetes:
   ```bash
   a2dismod -f deflate
   service apache2 restart
   ```
   ![[Sitio Web Seguro-1790223047254.webp]]
   *Figura 17: Inhabilitación del módulo mod_deflate y reinicio del servicio Apache.*

3. Se activó el VirtualHost de SSL creando un enlace simbólico hacia los sitios habilitados:
   ```bash
   ln -s /etc/apache2/sites-available/default-ssl.conf /etc/apache2/sites-enabled/000-default-ssl.conf
   ```
   ![[Sitio Web Seguro-1790223047260.webp]]
   *Figura 18: Activación del sitio SSL enlazando default-ssl.conf en sites-enabled.*

4. Se crearon copias de seguridad de los archivos de configuración originales:
   ```bash
   cd /etc/apache2/sites-available
   cp 000-default.conf 000-default.orig
   cp default-ssl.conf default-ssl.orig
   ```
   ![[Sitio Web Seguro-1790223047261.webp]]
   *Figura 19: Respaldo de los archivos 000-default.conf y default-ssl.conf.*

5. Se construyó el documento web personalizado en `/var/www/html/index.html`:
   ```bash
   vim /var/www/html/index.html
   ```
   ```html
   <html>
   <head>
     <title>Servidor de Esteban & Pacheco</title>
   </head>
   <body>
     Esta es una pagina en el espacio del servidor de Esteban & Pacheco.
     Dependiendo del tipo de acceso que se haga (mediante http o https) sera
     posible o no leer el contenido capturado con Wireshark.
   </body>
   </html>
   ```
   ![[Sitio Web Seguro-1790223047263.webp]]
   *Figura 20: Edición del archivo index.html representativo para el laboratorio.*

6. Se parametrizó el archivo `000-default.conf` para el puerto 80 asociándolo a la IP `10.0.2.15`:
   ![[Sitio Web Seguro-1790223047265.webp]]
   *Figura 21: Configuración de ServerAdmin y ServerName (10.0.2.15:80) en 000-default.conf.*

7. Se editó el archivo `default-ssl.conf` para el puerto 443. Originalmente apuntaba a los certificados ficticios *snakeoil*:
   ![[Sitio Web Seguro-1790223047267.webp]]
   *Figura 22: Configuración predeterminada de certificados snakeoil en default-ssl.conf.*

   Se modificaron las directivas para apuntar a los certificados válidos generados en la práctica:
   * `ServerAdmin webmaster@10.0.2.15`
   * `ServerName 10.0.2.15:443`
   * `SSLEngine on`
   * `SSLCertificateFile /etc/apache2/ssl/server.crt`
   * `SSLCertificateKeyFile /etc/apache2/ssl/server.key`

   ![[Sitio Web Seguro-1790223047269.webp]]
   *Figura 23: Configuración final de default-ssl.conf vinculando server.crt y server.key.*

Se reinició el servicio Apache para aplicar la totalidad de las configuraciones:
```bash
apache2ctl restart
```

---

### PREGUNTA No. 1 DE LA GUÍA
> **Explique para qué sirven los cambios que acaba de hacer.**

**Respuesta Técnica:**
Los cambios efectuados sobre el servidor Apache cumplen propósitos estructurales a nivel de transporte, criptografía y arquitectura de servicios:

1. **Definición de Identidad en los VirtualHosts (`ServerName 10.0.2.15:80` y `10.0.2.15:443`):**  
   Al delimitar el nombre canónico y el puerto de escucha, Apache puede discriminar las peticiones que ingresan por el puerto estándar de texto claro (80) de aquellas que requieren una capa de transporte seguro (443), evitando ambigüedades en la resolución del host y garantizando que el servidor responda únicamente cuando la petición se oriente a su interfaz designada.
2. **Habilitación del Motor Criptográfico (`SSLEngine on`):**  
   Instruye al módulo `mod_ssl` de Apache para que inicie la negociación del protocolo TLS sobre las conexiones entrantes al socket TCP 443 antes de procesar cualquier cabecera o solicitud HTTP de la capa de aplicación.
3. **Punteros a la Clave Privada y Certificado (`SSLCertificateFile` y `SSLCertificateKeyFile`):**  
   * `SSLCertificateFile /etc/apache2/ssl/server.crt`: Proporciona la clave pública del servidor y la firma digital de la CA, documento que el servidor enviará obligatoriamente al cliente en el mensaje *Server Hello* para identificarse.
   * `SSLCertificateKeyFile /etc/apache2/ssl/server.key`: Permite a Apache descifrar el secreto pre-maestro (*Pre-Master Secret*) remitido por el cliente durante el intercambio de llaves o firmar los parámetros del intercambio Diffie-Hellman efímero, haciendo posible la derivación de las claves simétricas de sesión.
4. **Desactivación del Módulo Deflate (`a2dismod -f deflate`):**  
   Inhabilita la compresión gzip/deflate de las respuestas HTTP. En entornos seguros, combinar compresión previa con cifrado introduce vulnerabilidades como CRIME (*Compression Ratio Info-leak Made Easy*), donde un atacante puede deducir tokens secretos analizando las variaciones en el tamaño del payload cifrado. Asimismo, en el marco académico, permite inspeccionar la longitud real de las tramas en Wireshark sin transformaciones adicionales.
5. **Permisos Restringidos (`chmod 400` y `chmod 700`):**  
   Aplican el principio de mínimo privilegio en el sistema de archivos Unix. Al impedir que usuarios sin privilegios lean `server.key`, se previene la exfiltración local de la clave, lo cual comprometería toda la confidencialidad histórica y futura de las sesiones del servidor.

---

### Fase 5: Prueba del Servicio en HTTP y Análisis de Tráfico Plano

Para corroborar la operación del servicio y comparar el nivel de exposición de los datos, se procedió a instalar Wireshark:

```bash
sudo apt-get install wireshark -y
sudo wireshark
```

![[Sitio Web Seguro-1790223047272.webp]]
*Figura 24: Instalación de la herramienta de análisis de paquetes Wireshark.*

![[Sitio Web Seguro-1790223047271.webp]]
*Figura 25: Ejecución de Wireshark con privilegios administrativos e inicio de captura en la interfaz 'any'.*

Con Wireshark capturando sobre la interfaz `any`, se abrió Mozilla Firefox y se navegó a la dirección en texto claro: `http://10.0.2.15`. El navegador desplegó la página sin advertencias, pero sin ningún distintivo de seguridad o cifrado.

![[Sitio Web Seguro-1790223047277.webp]]
*Figura 26: Navegación exitosa en HTTP plano (puerto 80).*

Al inspeccionar los paquetes capturados en Wireshark, se identificó la solicitud HTTP de tipo `GET / HTTP/1.1` y la respuesta del servidor (`HTTP/1.1 304 Not Modified` y peticiones subsecuentes a `favicon.ico` con código 404):

![[Sitio Web Seguro-1790223047344.webp]]
*Figura 27: Captura en Wireshark de la conversación HTTP en el puerto 80 (GET / y respuesta 304).*

![[Sitio Web Seguro-1790223047290.webp]]
*Figura 28: Detalle de tramas HTTP y DNS en Wireshark evidenciando ausencia total de cifrado.*

---

### PREGUNTA No. 2 DE LA GUÍA
> **Revise los paquetes capturados. ¿Qué puede ver en el contenido?**

**Respuesta Técnica:**
En la captura del tráfico HTTP (puerto TCP 80) se puede observar la **totalidad de la conversación en texto claro (plaintext)**. Específicamente:
* En el paquete número 10 (`GET / HTTP/1.1`) se observan sin ofuscación las cabeceras del protocolo HTTP: el método de solicitud (`GET`), la versión del protocolo (`HTTP/1.1`), el agente de usuario (*User-Agent*), las cabeceras de lenguaje y codificación aceptada, y las directivas de control de caché (`If-Modified-Since`, `If-None-Match`).
* En el paquete número 12 se observa la respuesta del servidor web (`HTTP/1.1 304 Not Modified`), incluyendo las cabeceras del servidor (`Date`, `Server: Apache/2.4.18 (Ubuntu)`, `ETag`).
* Cuando el servidor transmite el cuerpo HTML (código de respuesta 200), **el payload completo es legible directamente en ASCII**: se observa el título `<title>Servidor de Esteban & Pacheco</title>` y el contenido textual íntegro de la página web.

**Implicación de Seguridad:** Cualquier actor malicioso ubicado en el mismo segmento de red (o ejecutando un ataque de envenenamiento ARP / *Man-in-the-Middle*) puede realizar *sniffing* pasivo y reconstruir credenciales, cookies de sesión, tokens de autenticación y datos confidenciales transmitidos por los usuarios sin requerir ninguna clave de descifrado.

---

### Fase 6: Intento de Acceso por HTTPS y Diagnóstico del Error

Se procedió a acceder al servidor empleando el esquema seguro: `https://10.0.2.15`. Inmediatamente, Mozilla Firefox interrumpió la conexión y desplegó una pantalla de alerta crítica: **"Your connection is not secure"** con el código de error `SEC_ERROR_UNKNOWN_ISSUER`.

![[Sitio Web Seguro-1790223047279.webp]]
*Figura 29: Error de certificado en Firefox: SEC_ERROR_UNKNOWN_ISSUER.*

---

### PREGUNTA No. 3 DE LA GUÍA
> **¿Qué mensaje de error obtiene? ¿Por qué?**

**Respuesta Técnica:**
* **Mensaje de Error Obtenido:**  
  `SEC_ERROR_UNKNOWN_ISSUER` (*The certificate is not trusted because the issuer certificate is unknown* / *El emisor del certificado es desconocido*).
* **Causa Raíz:**  
  Los navegadores modernos (como Mozilla Firefox) incorporan un almacén interno de certificados de confianza (*Trust Store* / *Root CA Store*) que contiene las claves públicas de autoridades comerciales pre-validadas a nivel global (tales como DigiCert, Let's Encrypt, Sectigo, GlobalSign, etc.).  
  Cuando el servidor Apache envió su certificado `server.crt`, Firefox verificó que este fue emitido y firmado digitalmente por la entidad `CA del equipo 10.0.2.15`. Al intentar construir la **Cadena de Confianza** (*Chain of Trust*), Firefox consultó su almacén local buscando el certificado raíz correspondiente para validar la firma matemática. Al no encontrarlo (puesto que se trata de una CA privada creada localmente para el laboratorio), el navegador no puede comprobar criptográficamente la legitimidad del emisor. Ante la imposibilidad de descartar que el certificado haya sido falsificado por un atacante en un ataque de suplantación (*spoofing* o MitM), el navegador bloquea preventivamente la sesión para proteger al usuario.

---

### Fase 7: Instalación del Certificado de la CA en Mozilla Firefox

Para solucionar la advertencia y establecer la relación de confianza entre el cliente y el servidor, se procedió a importar el certificado raíz de la CA en el navegador.

Primero, se copió el archivo `ca.crt` desde `/root/certs` hacia el escritorio del usuario no privilegiado (`/home/user/Desktop/`) y se le otorgaron permisos de lectura universal (`644`):

```bash
sudo su
cp /root/certs/ca.crt /home/user/Desktop/
chmod 644 /home/user/Desktop/ca.crt
```

![[Sitio Web Seguro-1790223047333.webp]]
*Figura 30: Copia del certificado raíz ca.crt al escritorio del usuario.*

![[Sitio Web Seguro-1790223138341.webp]]
*Figura 31: Verificación de ca.crt disponible en el explorador de archivos en ~/Desktop.*

A continuación, en Firefox:
1. Se desplegó el menú principal y se seleccionó **Preferences**:
   ![[Sitio Web Seguro-1790223047282.webp]]
   *Figura 32: Ingreso al panel de preferencias de Firefox.*

2. Se seleccionó la pestaña lateral **Privacy & Security**:
   ![[Sitio Web Seguro-1790223047339.webp]]
   *Figura 33: Sección Privacy & Security en las preferencias del navegador.*

3. Se navegó hacia la sección inferior *Certificates* y se pulsó el botón **View Certificates...**:
   ![[Sitio Web Seguro-1790223047329.webp]]
   *Figura 34: Localización de la opción View Certificates.*

4. En la ventana emergente *Certificate Manager*, se seleccionó la pestaña **Authorities** y se presionó el botón **Import...**:
   ![[Sitio Web Seguro-1790223047331.webp]]
   *Figura 35: Pestaña Authorities en el Administrador de Certificados de Firefox.*

5. Se cargó el archivo `ca.crt` ubicado en el escritorio. En el cuadro de diálogo de autorización *Downloading Certificate*, se marcó la casilla de verificación:  
   **"Trust this CA to identify websites"** (*Confiar en esta CA para identificar sitios web*). Se presionó **OK**:

   ![[Sitio Web Seguro-1790223138381.webp]]
   *Figura 36: Configuración del nivel de confianza para la nueva CA en Firefox.*

---

### Fase 8: Validación de Conexión Segura y Análisis Criptográfico con Wireshark

Con la CA registrada en el almacén de confianza del cliente, se recargó la dirección `https://10.0.2.15`. La página web cargó de inmediato exhibiendo el **icono de candado verde cerrado**, indicando que la conexión está debidamente cifrada y la identidad del servidor ha sido validada sin excepciones de seguridad:

![[Sitio Web Seguro-1790223047284.webp]]
*Figura 37: Acceso exitoso a https://10.0.2.15 con validación completa y candado verde de seguridad.*

---

### PREGUNTA No. 4 DE LA GUÍA
> **¿Qué pasa ahora? ¿Por qué?**

**Respuesta Técnica:**
* **Qué sucede:**  
  La página web se visualiza correctamente, sin advertencias ni pantallas de bloqueo, mostrando en la barra de direcciones el protocolo `https://` y el icono de candado verde de seguridad plenamente validado.
* **Por qué ocurre:**  
  Al importar manualmente el certificado `ca.crt` en la pestaña *Authorities* con el flag de confianza para identificación de sitios web, el navegador incorporó la clave pública de la CA en su almacén seguro de raíces de confianza (*Trust Store*).  
  Cuando el servidor Apache presenta su certificado `server.crt`, Firefox extrae la firma digital estampada en él y la valida matemáticamente utilizando la clave pública de la CA recién instalada. Dado que la firma es íntegra y válida, y que el *Common Name* (`10.0.2.15`) coincide con la dirección consultada en la barra URL, la cadena de confianza queda formalmente cerrada:  
  $$\text{Cliente (Firefox)} \longrightarrow \text{Confía en la CA importada} \longrightarrow \text{La CA garantiza la identidad de 10.0.2.15} \longrightarrow \text{Conexión Segura}$$

---

### PREGUNTA No. 5 DE LA GUÍA
> **¿Qué observa en la captura de Wireshark?**

**Respuesta Técnica:**
Al capturar la sesión sobre el puerto 443 en Wireshark, el comportamiento del tráfico difiere radicalmente de HTTP:

![[Sitio Web Seguro-1790223138342.webp]]
*Figura 38: Captura en Wireshark del protocolo de enlace TLS 1.2 (Client Hello, Server Hello, Cipher Spec, Application Data).*

![[Sitio Web Seguro-1790223138382.webp]]
*Figura 39: Secuencia de paquetes de datos de aplicación (Application Data) cifrados y acuses TCP.*

En la secuencia de paquetes se observa:
1. **Apertura de la sesión de transporte:** Se efectúa el saludo de tres vías de TCP (*3-Way Handshake*): paquetes SYN, SYN/ACK y ACK en el puerto de destino `443`.
2. **Fase de Negociación TLS (Handshake):**  
   * Paquete 85: `Client Hello` emitido por la máquina cliente hacia el servidor.
   * Paquete 87: `Server Hello, Change Cipher Spec, Encrypted Handshake Message` (en TLS 1.2 agrupado) donde el servidor responde, selecciona el conjunto de cifrado (*cipher suite*) y entrega su certificado digital.
   * Paquetes 88 y 89: El cliente envía su intercambio de clave (*Client Key Exchange*), la señalización `Change Cipher Spec` y su propio `Encrypted Handshake Message`.
3. **Fase de Transferencia Segura:** A partir del paquete 91 y en todas las tramas subsiguientes (paquetes 92, 95, 99, 100, 108, 110), el protocolo deja de identificar capas de aplicación como "HTTP" y las etiqueta exclusivamente como **`TLSv1.2 Application Data`**.

---

### PREGUNTA No. 6 DE LA GUÍA
> **¿Puede ver el contenido de la página en texto claro?**

**Respuesta Técnica:**
**No, es completamente imposible ver el contenido de la página en texto claro.**  
Al inspeccionar el contenido de los paquetes clasificados como `Application Data` en Wireshark, la carga útil (*payload*) está completamente cifrada mediante algoritmos simétricos de bloque o flujo (como AES). Los datos que viajan por el medio físico consisten en un flujo de bytes pseudoaleatorios con alta entropía.

A diferencia de la captura en HTTP donde se leían las etiquetas HTML `<html>`, `<title>` y el texto de los autores en ASCII puro, en HTTPS **las cabeceras HTTP (método GET, rutas, cookies, agentes), los parámetros de consulta y el cuerpo HTML están totalmente blindados**. Un atacante interceptando el canal únicamente puede conocer la dirección IP de origen, la IP de destino, los puertos TCP utilizados y el tamaño aproximado de los paquetes transmitidos, preservando la confidencialidad de la información.

---

### PREGUNTA No. 7 DE LA GUÍA
> **Interprete la negociación que se ve al inicio de la conexión.**

**Respuesta Técnica (Desglose del Handshake TLS 1.2):**
De acuerdo con la captura de la *Figura 38* (paquetes 81 al 91), la negociación inicial se estructura en los siguientes hitos técnicos:

```
Cliente (10.0.2.15)                                    Servidor (10.0.2.15:443)
       |                                                         |
       |------------------- 1. TCP SYN ------------------------->| (Paquete 81)
       |<------------------ 2. TCP SYN, ACK ---------------------| (Paquete 83)
       |------------------- 3. TCP ACK ------------------------->| (Paquete 84)
       |                                                         |
       |------------------- 4. Client Hello -------------------->| (Paquete 85)
       |                                                         |
       |<--------- 5. Server Hello, Certificate, ---------------| (Paquete 87)
       |<---------    Server Key Exchange, Server Hello Done ----|
       |                                                         |
       |---------- 6. Client Key Exchange ---------------------->| (Paquete 89)
       |---------- 7. Change Cipher Spec ----------------------->|
       |---------- 8. Encrypted Handshake Message (Finished) ---->|
       |                                                         |
       |<--------- 9. Change Cipher Spec ------------------------|
       |<--------- 10. Encrypted Handshake Message (Finished) ---|
       |                                                         |
       |<================ 11. Application Data (Cifrado) =======>| (Paquetes 91+)
```

1. **Establecimiento de Conexión TCP (Paquetes 81, 83, 84):**  
   Se ejecuta el *three-way handshake* TCP (`SYN` $\rightarrow$ `SYN/ACK` $\rightarrow$ `ACK`) para establecer un canal confiable orientado a la conexión en el puerto de destino `443`.
2. **Client Hello (Paquete 85):**  
   El cliente inicia la negociación TLS informando al servidor sus capacidades criptográficas:
   * Versión máxima de TLS soportada (en este caso TLS 1.2).
   * Un número pseudoaleatorio generado por el cliente (*Client Random* de 32 bytes) utilizado posteriormente para calcular las llaves de sesión.
   * Lista de suites de cifrado soportadas (*Cipher Suites*), que combinan algoritmos de intercambio de llaves (ECDHE/RSA), autenticación (RSA/ECDSA), cifrado simétrico (AES-128/256 en modo GCM/CBC) y funciones hash (SHA-256/384).
   * Parámetros de compresión y extensiones (como SNI - *Server Name Indication*).
3. **Server Hello, Certificate y Server Hello Done (Paquete 87):**  
   El servidor evalúa las capacidades enviadas por el cliente y responde:
   * *Server Hello:* Selecciona la mejor suite de cifrado común y envía su propio número aleatorio (*Server Random*).
   * *Certificate:* Remite su certificado digital X.509 (`server.crt`), el cual contiene su clave pública y está firmado por la CA.
   * *Server Hello Done:* Notifica al cliente que concluyó su fase inicial y espera su respuesta.
4. **Validación del Certificado e Intercambio de Claves (Paquete 89):**  
   * El cliente verifica el certificado del servidor contra su almacén de confianza (gracias a `ca.crt`).
   * El cliente genera un valor secreto denominado **Pre-Master Secret**, lo cifra con la clave pública del servidor y lo remite en el mensaje *Client Key Exchange*. (Ambos extremos, combinando el *Client Random*, *Server Random* y el *Pre-Master Secret*, derivan de forma idéntica e independiente las claves simétricas de sesión: clave de cifrado cliente-servidor, clave de cifrado servidor-cliente y claves MAC de integridad).
5. **Change Cipher Spec y Encrypted Handshake Message:**  
   * Ambas partes envían el mensaje `Change Cipher Spec`, indicando que a partir de ese instante todo el tráfico subsiguiente será transmitido utilizando el algoritmo de cifrado y las claves simétricas recién negociadas.
   * Cada parte envía un mensaje `Encrypted Handshake Message` (también denominado *Finished*), que contiene un hash de todos los mensajes previos del saludo. Si el receptor puede descifrarlo y verificar el hash, se comprueba que ningún mensaje intermedio fue manipulado por un tercero (*anti-tampering*).
6. **Transmisión de Datos de Aplicación (Paquetes 91+):**  
   El canal seguro queda establecido. Las solicitudes y respuestas HTTP convencionales son encapsuladas dentro de registros seguros **TLS Application Data**, garantizando confidencialidad, autenticidad e integridad de extremo a extremo.

---

## 5. PREGUNTAS FINALES DE EVALUACIÓN

### ¿Qué conceptos utilizó para ejecutar esta práctica?
1. **Infraestructura de Clave Pública (PKI):** Arquitectura jerárquica de confianza para emisión, revocación y gestión de certificados digitales.
2. **Criptografía Asimétrica (RSA) y Simétrica (AES):** Uso de RSA (4096 bits) para la firma y autenticación de identidades, y AES (256 bits) para la protección de claves maestras y el cifrado del canal de transporte.
3. **Autoridad de Certificación (CA) y Certificados Raíz:** Creación de una entidad de confianza local responsable de estampar firmas digitales que avalan la pertenencia de una clave pública a un sujeto determinado.
4. **Solicitud de Firma de Certificado (CSR):** Generación de paquetes PKCS#10 que portan la clave pública y los datos de identidad corporativa (*Distinguished Name*) requeridos para la emisión de credenciales digitales.
5. **Estándar ITU-T X.509:** Estructura de datos normalizada para certificados de clave pública, comprendiendo campos críticos como Sujeto, Emisor, Fechas de Validez, Algoritmo de Hash y Extensiones de Uso de Llave.
6. **Cadena de Confianza (*Chain of Trust*):** Relación de confianza transitiva donde un cliente valida un certificado final apoyándose en un certificado intermedio o raíz presente en su depósito local de confianza (*Trust Store*).
7. **Protocolo TLS/SSL sobre TCP (Puerto 443):** Capa intermedia de seguridad que orquesta el apretón de manos criptográfico (*TLS Handshake*) y el transporte hermético de peticiones web.
8. **Administración y Configuración del Servidor Apache2:** Implementación de *VirtualHosts*, activación de directivas de cifrado mediante `mod_ssl`, mitigación de riesgos con `mod_deflate` y gestión de permisos de seguridad en sistemas basados en Unix.
9. **Análisis Forense y de Protocolos de Red con Wireshark:** Monitoreo y disección de tramas Ethernet, paquetes IP, segmentos TCP y registros TLS para evaluar la postura de seguridad de un canal de comunicaciones.

---

### ¿Qué aprendió en esta práctica?
* **La vulnerabilidad inherente de las comunicaciones en texto claro:** Constatamos empíricamente cómo protocolos históricos como HTTP dejan desprotegido al usuario frente a escuchas pasivas, haciendo trivial la captura de contenido confidencial mediante analizadores como Wireshark.
* **El funcionamiento interno y la necesidad de una PKI:** Comprendimos que la criptografía por sí sola no garantiza seguridad sin autenticación: tener una clave pública no sirve de nada si no podemos asegurar a quién pertenece. La Autoridad de Certificación resuelve el problema de la suplantación de identidad en Internet.
* **El significado técnico de las advertencias del navegador:** Comprendimos por qué los navegadores bloquean sitios con certificados autofirmados o de CAs privadas (`SEC_ERROR_UNKNOWN_ISSUER`). La advertencia no significa necesariamente que el sitio esté intervenido, sino que el navegador no posee la clave pública de la entidad firmante para corroborar matemáticamente su legitimidad.
* **La criticidad de la protección de las llaves privadas:** Aprendimos el delicado compromiso entre seguridad y operatividad. Al retirar la frase de paso de la clave del servidor (`server.key`) para permitir que Apache inicie sin intervención humana, el archivo queda desprotegido en reposo, lo que exige blindarlo obligatoriamente con permisos de sistema de archivos rigurosos (`chmod 400`).
* **La sincronización simétrica/asimétrica en TLS:** Visualizamos cómo TLS resuelve el dilema del rendimiento criptográfico: utiliza la costosa criptografía asimétrica únicamente durante el apretón de manos inicial para autenticar y establecer un secreto compartido, y conmuta inmediatamente a algoritmos simétricos ultrarrápidos para el transporte masivo de los datos.

---

## 6. CONCLUSIONES

1. Se logró con éxito el despliegue de una infraestructura de servidor web seguro HTTPS utilizando Apache2 y OpenSSL en un entorno Linux Lubuntu, implementando una CA raíz propia y emitiendo un certificado X.509 válido para la dirección `10.0.2.15`.
2. Se comprobó la efectividad de la cadena de confianza digital: tras instalar el certificado raíz `ca.crt` en el almacén de Mozilla Firefox, el navegador validó la firma del servidor eliminando las advertencias y estableciendo un canal cifrado autenticado simbolizado por el candado verde.
3. Mediante el análisis comparativo en Wireshark, se demostró la diferencia cuantitativa y cualitativa entre HTTP y HTTPS: mientras que en el puerto 80 todo el contenido viaja en texto plano accesible para cualquier interceptor, en el puerto 443 toda la capa de aplicación queda sellada en registros *Application Data*, impidiendo la lectura no autorizada y garantizando la confidencialidad e integridad de la comunicación.
4. El correcto aseguramiento de un servidor HTTPS no depende únicamente del uso de certificados, sino de una gestión integral de la configuración: asignación estricta de permisos Unix a los archivos criptográficos (`400` y `700`), inhabilitación de funciones que generen fugas de canal lateral (como el módulo `deflate`) y una rigurosa concordancia entre el nombre del servidor (*Common Name*) y el recurso contactado.

